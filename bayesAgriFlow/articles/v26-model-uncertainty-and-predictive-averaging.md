# Model Uncertainty and Predictive Averaging

## Model uncertainty is not one problem

Bayesian model uncertainty can refer to uncertainty about which
scientific model generated the data, uncertainty about which model will
predict new observations best, or uncertainty about which model should
guide a decision. Those questions are related but they are not
interchangeable. Version 1.0.0 keeps them separate in the API.

Classical Bayesian model averaging integrates over a declared
candidate-model set using posterior model probabilities (Hoeting et al.
1999). Predictive stacking instead chooses weights to optimize the
quality of the combined predictive distribution under out-of-sample
assessment (Yao et al. 2018). The numerical weights can look similar in
a particular dataset, but their meanings are different.

### Learning objectives

- distinguish posterior model probabilities from predictive stacking
  weights;
- construct formal BMA weights from marginal likelihoods and prior model
  probabilities;
- construct stacking or pseudo-BMA weights from PSIS-LOO objects;
- average posterior predictions while retaining between-model
  uncertainty;
- report model uncertainty without turning a weighting method into a
  causal or mechanistic conclusion.

### Agronomic motivating example

Consider a nitrogen-response experiment in which linear, quadratic, and
saturating response structures are all scientifically plausible over the
observed dose range. A single selected model hides the uncertainty about
response shape. A model-averaged analysis can propagate that uncertainty
into predicted yield or downstream economic decisions.

The teaching dataset `model_selection` also includes useful and nuisance
predictors. It is intentionally constructed so that different predictive
models can be compared without implying that every included variable is
a manipulable treatment.

``` r

library(bayesAgriFlow)
dat <- bayes_teaching_data("model_selection")
str(dat)
#> 'data.frame':    160 obs. of  11 variables:
#>  $ plot            : int  1 2 3 4 5 6 7 8 9 10 ...
#>  $ block           : Factor w/ 8 levels "B1","B2","B3",..: 1 2 3 4 5 6 7 8 1 2 ...
#>  $ nitrogen        : num  102.9 123.9 104.6 98.3 30 ...
#>  $ water           : num  407 338 256 400 449 ...
#>  $ soil_p          : num  16.95 18.57 26.2 19.83 5.89 ...
#>  $ organic_matter  : num  1.59 2.54 2.64 2.54 3.25 ...
#>  $ canopy_index    : num  0.804 0.833 0.632 0.697 0.704 ...
#>  $ correlated_proxy: num  0.612 0.587 0.567 0.279 0.193 ...
#>  $ noise1          : num  0.276 1.198 -1.966 -1.1 -1.498 ...
#>  $ noise2          : num  -0.619 -0.31 0.443 0.523 -0.967 ...
#>  $ yield           : num  9.46 9.69 9.25 9.68 8.64 ...
summary(dat$yield)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   7.018   8.859   9.527   9.460  10.064  11.645
```

### Three distinct targets

- **Evidence target:** posterior probability assigned to each candidate
  model, conditional on model priors and within-model priors.
- **Predictive target:** weights that make the combined predictive
  distribution perform well for future data.
- **Decision target:** expected utility after model uncertainty has been
  propagated to outcomes relevant to actions.

| Quantity | Function | Interpretation |
|----|----|----|
| Posterior model probability | [`bayes_bma_weights()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_bma_weights.md) | Belief over a declared candidate-model set under marginal-likelihood BMA |
| Stacking weight | `bayes_model_weights(..., method="stacking")` | Predictive-combination weight optimized from pointwise predictive performance |
| Pseudo-BMA+ weight | `bayes_model_weights(..., method="pseudobma+")` | Predictive weight with Bayesian-bootstrap stabilization |
| Model-average draw | [`bayes_model_average()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_model_average.md) | Mixture draw that propagates within- and between-model predictive uncertainty |

### Formal Bayesian model averaging

For candidate models $`M_1,\ldots,M_K`$, posterior model probability is
proportional to the marginal likelihood times the prior model
probability. The marginal likelihood integrates the likelihood over the
within-model prior, so BMA inherits the sensitivity of Bayes factors to
those priors.

[`bayes_bma_weights()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_bma_weights.md)
accepts log marginal likelihoods rather than Bayes factors because the
log scale is numerically stable and makes the model-prior contribution
explicit.

``` r

log_ml <- c(linear = -120.4, quadratic = -116.1, cubic = -117.8)
w_equal <- bayes_bma_weights(log_ml)
w_equal
#> bayesAgriFlow model weights
#> Method: marginal-likelihood BMA
#>       model     weight
#> 1    linear 0.01134256
#> 2 quadratic 0.83594421
#> 3     cubic 0.15271323
w_informed <- bayes_bma_weights(
  log_ml,
  prior_weights = c(linear = 0.50, quadratic = 0.35, cubic = 0.15)
)
w_informed
#> bayesAgriFlow model weights
#> Method: marginal-likelihood BMA
#>       model     weight
#> 1    linear 0.01765880
#> 2 quadratic 0.91101514
#> 3     cubic 0.07132605
```

### Why BMA weights are conditional quantities

A posterior model probability is not the probability that a model is
scientifically true in an unrestricted universe. It is conditional on
the candidate set, the model prior, the parameter priors, and the
likelihoods. Adding or removing a candidate can redistribute posterior
model probability.

This conditionality should be explicit in agronomic reporting,
particularly when candidate models are merely convenient approximations
to a response process.

### Fit-level BMA with repeated bridge sampling

When candidate models have been fitted with `brms`,
[`bayes_bma_from_fits()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_bma_from_fits.md)
provides the integrated route from fitted models to posterior model
probabilities. It repeats bridge sampling for every candidate, stores
every log marginal likelihood estimate, summarizes their numerical
dispersion, and only then computes BMA weights from the mean log
marginal likelihoods. This is intentionally more transparent than
returning one unexplained Bayes-factor number.

``` r

# bma <- bayes_bma_from_fits(
#   list(linear = fit_linear, quadratic = fit_quadratic),
#   prior_weights = c(linear = 0.5, quadratic = 0.5),
#   repetitions = 3,
#   seed = 20260824
# )
# bma$stability
# bma$weights
```

Large dispersion among repeated bridge estimates is a computational
warning. It should trigger more posterior draws, improved
bridge-sampling settings, or reconsideration of whether the requested
marginal-likelihood comparison is stable enough to report.

### Predictive stacking

Stacking addresses a different problem. It seeks a convex combination of
predictive distributions that performs well under leave-one-out
predictive assessment. It is particularly attractive when none of the
candidate models is expected to be literally true. Yao et al. (2018)
develop this predictive perspective.

The implementation uses
[`loo::loo_model_weights()`](https://mc-stan.org/loo/reference/loo_model_weights.html)
through a `brms`-backed `bayes_fit`. The model list must describe the
same response observations so that pointwise predictive quantities are
comparable.

``` r

# Heavy example: requires brms, loo, and a working Stan backend.
# fits <- list(linear = fit_linear, quadratic = fit_quadratic, flexible = fit_spline)
# w_stack <- bayes_model_weights(fits, method = "stacking", cores = 4)
# w_stack
# w_pbma <- bayes_model_weights(fits, method = "pseudobma+", bb_n = 2000, seed = 20260824)
```

### PSIS-LOO diagnostics still matter

- Inspect Pareto-$`k`$ diagnostics before trusting LOO-derived weights.
- A weighting procedure cannot repair a severely misspecified likelihood
  by itself.
- If observations are clustered, temporal, spatial, or otherwise
  dependent, the predictive unit used by cross-validation must match the
  intended generalization target.
- Large changes in weights under reasonable alternative validation
  schemes are substantive sensitivity results.

### Pseudo-BMA and pseudo-BMA+

Pseudo-BMA converts relative predictive scores to model weights.
Pseudo-BMA+ adds a Bayesian bootstrap over pointwise predictive
contributions and is generally less optimistic about small apparent
score differences. These are predictive heuristics, not posterior model
probabilities.

For teaching, `bayesAgriFlow` labels the method in the returned object
and in the print method so that the semantic distinction remains
visible.

### Model-averaged prediction

Averaging parameter coefficients is usually not meaningful when models
have different parameterizations. Prediction provides a common scale.
[`bayes_model_average()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_model_average.md)
therefore combines model-specific posterior predictions rather than
trying to average arbitrary parameter vectors.

The function returns both weighted mean draws and mixture draws. The
mixture draws sample a model component according to the supplied weights
for each retained draw, preserving between-model dispersion that would
disappear from a simple weighted mean.

``` r

# Heavy example.
# avg <- bayes_model_average(
#   fits,
#   newdata = data.frame(nitrogen = seq(0, 220, length.out = 50)),
#   weights = w_stack,
#   type = "mean",
#   ndraws = 1500,
#   seed = 20260824
# )
# head(avg$summary)
```

### A simple mixture demonstration without Stan

The logic of predictive mixtures can be studied directly. The example
below creates posterior predictions under two candidate response models
and samples the model identity according to declared weights. This is
not a replacement for
[`bayes_model_average()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_model_average.md);
it makes the source of between-model uncertainty visible.

``` r

set.seed(20260824)
pred_a <- rnorm(3000, 6.0, 0.30)
pred_b <- rnorm(3000, 6.5, 0.45)
w <- c(A = 0.35, B = 0.65)
component <- sample(names(w), 3000, replace = TRUE, prob = w)
mix <- ifelse(component == "A", pred_a, pred_b)
quantile(mix, c(.025, .5, .975))
#>     2.5%      50%    97.5% 
#> 5.520436 6.288694 7.299126
```

### Candidate-model construction

- Define models from scientific alternatives or predictive purposes
  before inspecting the final weighting results.
- Respect model hierarchy when interaction or polynomial terms are
  present.
- Do not generate a large model universe merely because software can
  enumerate it.
- When the candidate set is exploratory, say so and focus interpretation
  on predictive performance rather than confirmatory evidence.

### M-closed and M-open reasoning

BMA is often described under an M-closed view in which one of the
candidate models is treated as the data-generating model. Stacking is
naturally suited to an M-open or approximation-oriented view in which
the useful goal is prediction even if every candidate is imperfect.

The package does not automatically choose between these philosophies.
The analyst specifies whether evidence or prediction is the target.

### Sensitivity to model priors

``` r

log_ml <- c(m1 = -80.0, m2 = -79.4, m3 = -79.8)
prior_sets <- list(
  equal = c(m1=.333, m2=.333, m3=.334),
  simple_favoring = c(m1=.70, m2=.20, m3=.10),
  complex_favoring = c(m1=.10, m2=.35, m3=.55)
)
lapply(prior_sets, function(p) bayes_bma_weights(log_ml, p)$weights)
#> $equal
#>        m1        m2        m3 
#> 0.2470850 0.4502183 0.3026966 
#> 
#> $simple_favoring
#>        m1        m2        m3 
#> 0.5899387 0.3071252 0.1029361 
#> 
#> $complex_favoring
#>         m1         m2         m3 
#> 0.07094649 0.45245524 0.47659828
```

### Sensitivity to candidate models

A sensitivity analysis can also vary the candidate set. If a posterior
model probability changes dramatically after adding a scientifically
plausible model, the original probability should not be reported as if
it were context-free.

Predictive stacking weights can likewise change when a complementary
model enters the pool because the optimum is for the combined predictive
distribution.

### Prediction scale and estimand

- Use expected-response predictions when the target is mean yield or
  mean disease severity.
- Use posterior predictive draws when future observational variability
  itself matters.
- For economic decisions, confirm whether prices apply to latent mean
  production or realized harvest outcomes.
- For multivariate fits, define the response before averaging
  predictions; version 1.0.0 deliberately requires compatible
  two-dimensional prediction matrices in the generic averaging path.

### What not to do

- Do not call a stacking weight a posterior model probability.
- Do not interpret a high model weight as proof that every predictor in
  that model is important.
- Do not use marginal-likelihood BMA without documenting within-model
  priors.
- Do not select a single model from a weight table and then pretend
  model uncertainty vanished.
- Do not combine models fitted to different observations without an
  explicitly justified predictive alignment.

### Frequentist crosswalk for teaching

AIC weights, cross-validated ensemble weights, frequentist model
averaging, Bayes factors, posterior model probabilities, and stacking
weights can all appear in model-uncertainty discussions. They arise from
different objectives. The package crosswalk is pedagogical and does not
claim numerical equivalence among them.

For a Bayesian analysis, the inferential interpretation must follow the
actual Bayesian quantity that was computed.

### Reporting checklist

- Describe the candidate-model set and why each model was included.
- State whether the target was evidence, prediction, or both.
- For BMA, report model priors, parameter priors, marginal-likelihood
  method, and sensitivity.
- For stacking or pseudo-BMA, report the LOO scheme, Pareto diagnostics,
  and weighting method.
- For model-averaged predictions, report how between-model uncertainty
  was retained.
- Do not rely on qualitative weight labels in place of numerical results
  and predictive plots.

### Reproducible starter workflow

``` r

# 1. Specify scientifically meaningful candidate models.
# 2. Fit each candidate with identical observations and defensible priors.
# 3. Check each model separately with bayes_diagnose() and bayes_pp_check().
# 4a. Evidence target: obtain stable marginal likelihoods, then bayes_bma_weights().
# 4b. Prediction target: bayes_model_weights(..., method = "stacking").
# 5. Average common-scale predictions with bayes_model_average().
# 6. Perform sensitivity analysis over priors, model set, and validation unit.
# 7. Carry the predictive distribution into bayes_utility() only if a decision is required.
```

### References and next steps

For formal BMA, see Hoeting et al. (1999). For predictive stacking, see
Yao et al. (2018). Projection predictive reduction is treated separately
in the next vignette because selecting a compact submodel from a
reference model is not the same operation as averaging a candidate-model
set.

Proceed to `v27-projection-predictive-selection.Rmd` when the scientific
need is parsimony or variable reduction rather than a weighted ensemble.

### Study questions

1.  Which inferential quantity in this vignette answers the scientific
    question, and which quantities are only diagnostics or computational
    aids?
2.  Which assumptions enter through the likelihood, which through the
    prior, and which through the decision model?
3.  What would change if the action costs changed while the posterior
    distribution stayed fixed?
4.  What would change if the candidate model set changed while the
    observed data stayed fixed?
5.  Which result can be interpreted as a probability and which is only a
    relative predictive weight?
6.  How should sensitivity to prior choice or utility choice be
    documented in a scientific report?
7.  Which analysis step should be pre-specified when the analysis is
    intended to be confirmatory?
8.  What posterior predictive check would be most informative for the
    agronomic response considered here?
9.  Which uncertainty source is represented by posterior draws and which
    additional uncertainty source is introduced by model averaging?
10. When would a simpler model be scientifically preferable even if its
    predictive score is slightly worse?

### Worked interpretation patterns

#### A dominant BMA weight

Report that the posterior probability is concentrated on one candidate
conditional on the declared model set and priors. Do not report that the
model has been proven true.

#### Diffuse BMA weights

Treat the result as substantial model uncertainty and propagate it to
predictions rather than selecting the numerically largest weight by
habit.

#### Dominant stacking weight

Interpret the weight as a predictive allocation within the candidate
ensemble, after checking the pointwise LOO diagnostics.

#### Different BMA and stacking rankings

This is not a contradiction. The procedures optimize different targets
and can reasonably favor different models.

#### Similar predictive scores

If uncertainty in score differences is large, emphasize predictive
equivalence and avoid overinterpreting small weight differences.

#### Unstable Pareto diagnostics

Repair or rethink the validation scheme before interpreting LOO-based
weights.

#### Prior-sensitive marginal likelihood

Present the sensitivity analysis because it is part of the evidence
result, not an optional appendix detail.

#### Model set sensitivity

Show how posterior probabilities or predictive weights change after
adding scientifically plausible alternatives.

#### Ensemble improves prediction

Report the predictive gain on the target scale and retain the ensemble
if operational constraints allow it.

#### Single deployment model required

Use projection predictive reduction or another principled reduction
strategy instead of selecting the largest ensemble weight automatically.

### Glossary for this vignette

#### Candidate model

A member of the explicitly defined set being compared or averaged.

#### Marginal likelihood

Likelihood integrated over the within-model prior distribution.

#### Model prior

Probability assigned to each candidate model before the current data are
used.

#### Posterior model probability

Updated probability over the candidate model set after combining model
priors and marginal likelihoods.

#### Stacking weight

Predictive combination weight estimated to improve a mixture predictive
distribution.

#### Pseudo-BMA+

Predictive weighting method that uses Bayesian-bootstrap stabilization
of pointwise predictive scores.

#### PSIS-LOO

Pareto-smoothed importance-sampling approximation to leave-one-out
predictive assessment.

#### Mixture prediction

Predictive distribution obtained by mixing component predictive
distributions according to model weights.

#### Between-model uncertainty

Variation caused by uncertainty over model structure in addition to
within-model posterior variation.

#### M-open

Perspective in which no candidate model needs to be literally true and
prediction can be the main target.

### Questions to answer before publication

#### Why these models?

State the scientific or predictive reason for every candidate rather
than treating enumeration as a neutral operation.

#### Why these priors?

For BMA, justify within-model priors because they influence marginal
likelihoods.

#### Why this weighting method?

Tie BMA to evidence over the candidate set and stacking to predictive
combination.

#### What is the validation unit?

Define whether generalization concerns plots, blocks, subjects,
environments, years, or another unit.

#### How was uncertainty propagated?

Explain whether the final prediction is a weighted mean or a full
predictive mixture.

#### What changed under sensitivity analysis?

Report changes across priors, candidate sets, and reasonable validation
choices.

#### What does the weight not mean?

Explicitly avoid causal, mechanistic, or truth claims not implied by the
weighting procedure.

#### How does this affect decisions?

If a decision follows, carry the averaged predictive distribution into
utility rather than choosing a model first without justification.

Hoeting, Jennifer A., David Madigan, Adrian E. Raftery, and Chris T.
Volinsky. 1999. “Bayesian Model Averaging: A Tutorial.” *Statistical
Science* 14 (4): 382–417. <https://doi.org/10.1214/ss/1009212519>.

Yao, Yuling, Aki Vehtari, Daniel Simpson, and Andrew Gelman. 2018.
“Using Stacking to Average Bayesian Predictive Distributions.” *Bayesian
Analysis* 13 (3): 917–1007. <https://doi.org/10.1214/17-BA1091>.
