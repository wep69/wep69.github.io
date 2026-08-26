# Distributional Regression Beyond the Mean

## Why this model class exists

Version 1.0.0 extends `bayesAgriFlow` from classical and flexible
response models to complex experimental systems. This chapter focuses on
modeling mean, residual scale, shape and precision, heteroscedasticity
as a scientific estimand, posterior distributional effects, prediction,
diagnostics, and model comparison. The statistical backend does not
define the scientific question. The experimental units, observation
process, likelihood, prior, and estimand must be stated before
computation. See B "urkner (2018).

## Learning objectives

After completing this vignette, the reader should be able to:

1.  identify the experimental and observational units;
2.  distinguish the biological process from the observation process;
3.  construct the corresponding `bayes_design`;
4.  choose priors on scientifically meaningful scales;
5.  run prior predictive checks before fitting;
6.  fit the model with `brms` and CmdStanR when available;
7.  review R-hat, ESS, MCSE, divergences and posterior predictive
    checks;
8.  interpret posterior effects without reducing the analysis to one
    threshold;
9.  evaluate sensitivity to important modeling choices; and
10. report the analysis reproducibly.

## Teaching data

``` r

dat <- bayes_teaching_data("distributional")
str(dat)
head(dat)
```

The bundled data are simulated and frozen for teaching and validation.
They are not empirical evidence. Their known data-generating structure
makes them useful for parameter recovery and workflow tests.

## Design first

A Bayesian analysis does not repair an incorrectly identified
experimental unit. Before writing a formula, document the randomization,
repeated observations, grouping factors, measurement process, and
scientifically meaningful contrasts.

## Core package workflow

The 1.0.0 complex-system workflow has the same high-level stages as
simpler package analyses:

``` text
scientific question
  -> design and observation audit
  -> likelihood and prior
  -> prior predictive check
  -> posterior computation
  -> sampler diagnostics
  -> posterior predictive check
  -> effects / predictions / decisions
  -> sensitivity analysis
  -> reproducible report
```

## Constructing the model

The exact constructor for this vignette is shown below. Heavy models are
not evaluated during routine vignette building.

``` r

des <- bayes_distributional(dat, "yield", ~ irrigation * treatment, list(sigma = ~ irrigation + treatment), group = "block")
bayes_design_audit(des)
bayes_plan(des)
```

## Priors and prior predictive reasoning

Priors should be checked on the scale of the variables and on the scale
of generated observations. In complex models, default priors can be
convenient starting points, but a convenient prior is not automatically
a scientifically defensible prior.

``` r

pr <- bayes_prior("regularizing")
pr
```

For Stan-based complex models, the exact prior predictive distribution
should be generated through the brms path with prior-only sampling. The
native conjugate simulator is intentionally restricted to simpler
Gaussian teaching models.

## CmdStanR computation

``` r

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  iter_warmup = 1000,
  iter_sampling = 1000,
  seed = 20260824
)
ctrl
```

A larger CPU count does not compensate for poor geometry, weak
identification, or an inappropriate likelihood. Parallel chains improve
elapsed time but not the conceptual quality of the model.

## Fitting locally

``` r

fit <- bayes_fit(des, engine = "brms", prior = pr, compute = ctrl)
fit
bayes_summary(fit)
```

## Sampler diagnostics

``` r

diag <- bayes_diagnose(fit)
diag
```

Interpretation should stop when serious divergences or convergence
failures remain unresolved. ESS, R-hat and MCSE answer computational
questions, not scientific questions.

## Posterior predictive checks

``` r

ppc <- bayes_pp_check(fit, ndraws = 200)
ppc
```

Posterior predictive checks ask whether replicated data from the fitted
model reproduce features of the observed data that matter for the
scientific problem. They do not prove that the model is true.

## Posterior prediction

``` r

pred <- bayes_predict(fit, type = "mean")
head(pred$summary)
```

Predictions should be distinguished from latent parameters and from
treatment contrasts. Extrapolation beyond the design region deserves
separate scrutiny.

## Practical relevance and effect interpretation

ROPE, probability of direction, meaningful-effect thresholds and Bayes
factors answer different questions. A large posterior probability of a
positive effect does not by itself establish practical importance.
Conversely, a posterior concentrated inside a scientifically justified
ROPE supports practical equivalence under that model and prior.

## Model comparison

PSIS-LOO can compare predictive adequacy among compatible fitted models.
Bayes factors compare marginal likelihoods under explicitly specified
priors. Neither should be used as an automatic model-selection ritual.

``` r

if (FALSE) {
  loo1 <- bayes_loo(fit)
  loo1
}
```

## Sensitivity analysis

At minimum, sensitivity analysis should consider:

- prior scale and prior location;
- likelihood family;
- dependency or hierarchy specification;
- influential design assumptions;
- alternative scientifically plausible definitions of practical
  importance; and
- computational stability.

## Reporting checklist

A report should state the response and predictors, experimental units,
observation model, hierarchy, priors and rationale, software and
backend, chain settings, posterior summaries, diagnostic evidence,
posterior predictive checks, sensitivity analyses, and limitations.
BARG-style reporting remains applicable to these more complex models.

## Common mistakes

1.  treating the backend as the model;
2.  ignoring the experimental unit;
3.  choosing a likelihood by habit rather than by the observation
    process;
4.  interpreting a Bayes factor as an effect size;
5.  treating a credible interval as a frequentist confidence interval;
6.  interpreting convergence as model adequacy;
7.  reporting only posterior means;
8.  failing to check prior predictive behavior;
9.  hiding data exclusions or missingness handling; and
10. presenting predictions outside the data-supported region without
    qualification.

## Reproducibility block

``` r

sessionInfo()
bayes_compute_info(ctrl)
```

## Starter script

The following skeleton is intentionally explicit.

``` r

# 1. Inspect data
# 2. Define design
# 3. Audit design
# 4. Specify priors
# 5. Perform prior predictive checks
# 6. Configure CmdStanR
# 7. Fit posterior
# 8. Diagnose computation
# 9. Perform posterior predictive checks
# 10. Summarize effects and predictions
# 11. Run sensitivity analyses
# 12. Generate a reproducible report
```

## Advanced extensions

Version 1.0.0 exposes this data structure to the current
model-uncertainty and decision layers, including predictive comparison,
model averaging where appropriate, projection-predictive tools for
compatible reference models, and utility-based decisions, without making
unsupported automatic choices.

## Exercises

1.  Change the prior scale and explain what changes in prior predictive
    space.
2.  Fit a scientifically plausible alternative model and compare
    posterior predictive behavior.
3.  Define a practical threshold in the units of the response.
4.  Identify which conclusions are sensitive to the structural
    assumptions.
5.  Write a methods paragraph that another analyst could reproduce.
6.  State what evidence would make you reject the current model
    structure.
7.  Explain which quantities are estimates, which are predictions, and
    which are decisions.
8.  Describe what additional data would most reduce scientific
    uncertainty.

## References

The package bibliography provides the methodological references cited
above. Software versions and exact backend configuration should
additionally be reported for every fitted analysis.

### Design audit questions

- What was randomized?
- At what level was treatment assigned?
- Which observations share latent causes?
- Which variables describe the observation process rather than biology?
- Which contrasts were planned before seeing the results?

### Prior audit questions

- Is the prior plausible on the original response scale?
- Does the prior permit impossible biological values?
- Are group-level SD priors compatible with plausible heterogeneity?
- Are weakly identified parameters receiving enough regularization?
- Would a reasonable alternative prior alter the scientific conclusion?

### Posterior audit questions

- Are all reported effects identified by the design?
- Are intervals sufficiently precise for the decision?
- Do important posterior correlations reveal weak identification?
- Are transformed response-scale quantities reported when link functions
  are used?
- Are derived quantities accompanied by uncertainty?

### Predictive audit questions

- Do replicated data reproduce central tendency?
- Do they reproduce dispersion and tails?
- Do they reproduce group-to-group heterogeneity?
- Do they reproduce the feature that motivated the complex model?
- Where does prediction become extrapolation?

### Communication audit questions

- Is uncertainty visible?
- Are priors named and justified?
- Are software and seeds recorded?
- Are failed model variants documented when relevant?
- Is the frequentist crosswalk clearly labeled as pedagogical?

## Model-specific technical notes

### Beyond modeling the mean

Classical regression often allows predictors to affect the conditional
mean while keeping residual variation constant. Distributional
regression permits scientifically chosen predictors to affect other
parameters such as residual SD, shape, or precision. This is especially
useful when treatment changes both expected yield and variability.

### Mean and variance are separate estimands

A treatment can increase mean yield while also increasing variability.
These are different scientific effects and should receive separate
priors, posterior summaries, and practical interpretations.

``` r

des_d <- bayes_distributional(
  bayes_teaching_data("distributional"),
  response = "yield",
  fixed = ~ irrigation + treatment,
  distributional = list(sigma = ~ treatment),
  group = "block",
  family = "gaussian"
)
```

### Parameter links

Scale and shape parameters are constrained. `brms` uses appropriate link
functions internally, so a coefficient in a `sigma` model is generally
not a direct SD difference on the original scale. Use posterior
transformations or predictions for scientific communication.

### Supported parameters in 1.0.0

The unified constructor exposes:

- `sigma` for Gaussian and Student-t models;
- `nu` for Student-t models;
- `shape` for negative-binomial and Gamma models; and
- `phi` for Beta models.

This restricted set avoids claiming support for arbitrary distributional
parameters before they are explicitly validated.

### Heterogeneous residual SD versus distributional regression

The earlier heterogeneous-residual interface models sigma by a declared
grouping factor.
[`bayes_distributional()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_distributional.md)
generalizes the idea to an explicit formula for sigma or another
supported parameter, including continuous predictors and interactions.

## Validation targets for the frozen distributional dataset

Verify locally that:

1.  treatment-dependent residual SD exists in the frozen simulation
    truth;
2.  mean-model coefficients are recoverable;
3.  sigma-model coefficients have the correct direction;
4.  posterior predictive dispersion differs appropriately by treatment;
5.  a homoscedastic model shows systematic predictive deficiencies;
6.  [`bayes_distributional_effects()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_distributional_effects.md)
    returns the intended parameter coefficients;
7.  conclusions are stable to reasonable sigma priors; and
8.  transformed response-scale variability is reported rather than only
    link-scale coefficients.

## Reporting example structure

State which distributional parameters were modeled, their predictor
formulas and links, priors for both location and distributional
submodels, posterior effects on all modeled parameters, response-scale
implications for variability or shape, posterior predictive checks
stratified by the relevant predictor, and sensitivity to alternative
variance structures.

## Advanced exercises

1.  Fit homoscedastic and treatment-dependent sigma models.
2.  Compare posterior predictive SD by treatment.
3.  Transform sigma coefficients to interpretable response-scale ratios.
4.  Allow irrigation to predict sigma and assess whether it is
    identified.
5.  Fit a Student-t sensitivity model and examine `nu`.
6.  Explain why larger variance is not automatically poorer treatment
    performance.
7.  Define a decision criterion involving both mean and risk.
8.  Compare predictive adequacy with PSIS-LOO while retaining scientific
    interpretation.
9.  Explain how a Beta precision model would differ from a Gaussian
    sigma model.
10. Draft a table with separate location- and scale-model results.

B "urkner, Paul-Christian. 2018. “Advanced Bayesian Multilevel Modeling
with the r Package Brms.” *The R Journal* 10 (1): 395–411.
<https://doi.org/10.32614/RJ-2018-017>.
