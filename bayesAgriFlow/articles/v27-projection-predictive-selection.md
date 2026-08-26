# Projection Predictive Selection

## A reference-model strategy for variable reduction

Projection predictive selection starts from a well-regularized Bayesian
reference model and asks how much of its predictive information can be
retained by a smaller model. It is not a sequence of independent
null-hypothesis tests. Piironen and Vehtari (2017) compare predictive
selection strategies, and Pavone et al. (2023) discuss the
reference-model approach in detail.

### Learning objectives

- understand why the reference model is the scientific starting point;
- separate search, evaluation, and final submodel choice;
- use cross-validated projection when model reduction will be reported;
- interpret `suggest_size()` as a heuristic rather than an automatic
  scientific truth;
- connect variable reduction to agronomic interpretation and prediction.

### Motivating agronomic problem

Imagine a yield model with nitrogen, water, initial soil phosphorus,
soil organic matter, canopy index, a correlated proxy variable, and two
deliberately irrelevant measurements. The scientific objective may be to
obtain a smaller monitoring model for deployment while retaining most of
the predictive performance of a richer Bayesian reference fit.

The `model_selection` teaching dataset was created for this purpose.
Because some predictors are correlated, univariate screening is
especially unattractive.

``` r

library(bayesAgriFlow)
dat <- bayes_teaching_data("model_selection")
cor(dat[c("nitrogen", "water", "soil_p", "organic_matter", "canopy_index", "correlated_proxy", "noise1", "noise2")])
#>                       nitrogen       water      soil_p organic_matter
#> nitrogen          1.0000000000 -0.05092985 -0.03257131  -0.0006707551
#> water            -0.0509298523  1.00000000 -0.08232986  -0.0456445384
#> soil_p           -0.0325713136 -0.08232986  1.00000000   0.0591888089
#> organic_matter   -0.0006707551 -0.04564454  0.05918881   1.0000000000
#> canopy_index      0.7114019922  0.33123985 -0.13822243  -0.0004851916
#> correlated_proxy  0.8671516655 -0.05223190  0.23553538   0.0161699599
#> noise1            0.0300961966 -0.01875826 -0.13002712   0.0084307833
#> noise2            0.0132549190  0.15597398 -0.06424619  -0.0795840833
#>                   canopy_index correlated_proxy       noise1       noise2
#> nitrogen          0.7114019922       0.86715167  0.030096197  0.013254919
#> water             0.3312398491      -0.05223190 -0.018758263  0.155973977
#> soil_p           -0.1382224328       0.23553538 -0.130027118 -0.064246189
#> organic_matter   -0.0004851916       0.01616996  0.008430783 -0.079584083
#> canopy_index      1.0000000000       0.59882848  0.073719825 -0.080804454
#> correlated_proxy  0.5988284781       1.00000000 -0.018260341 -0.032939944
#> noise1            0.0737198251      -0.01826034  1.000000000 -0.001980609
#> noise2           -0.0808044537      -0.03293994 -0.001980609  1.000000000
```

### Why not select by posterior intervals alone?

Whether a coefficient interval overlaps zero is not a complete
variable-selection criterion. Collinearity, interactions, nonlinear
structure, shrinkage, and the predictive contribution of groups of terms
can make coefficient-by-coefficient deletion unstable.

Projection treats the reference predictive distribution as the target to
be approximated by smaller submodels.

### The reference model

- It should be rich enough to capture scientifically plausible signal.
- Its priors should regularize weakly identified directions rather than
  relying on stepwise deletion.
- Its posterior computation must pass ordinary convergence and
  predictive checks.
- Its predictions should be useful for the population and prediction
  domain of interest.
- Projection cannot rescue a poor reference model; it inherits
  information from it.

### Reference fit through bayesAgriFlow

``` r

# Requires brms and a working Stan backend.
# des <- bayes_regression(
#   dat, response = "yield",
#   predictors = c("nitrogen", "water", "soil_p", "organic_matter",
#                  "canopy_index", "correlated_proxy", "noise1", "noise2")
# )
# ref_fit <- bayes_fit(
#   des,
#   engine = "brms", backend = "cmdstanr",
#   n_cpus = 8, chains = 4, parallel_chains = 4,
#   seed = 20260824
# )
# bayes_diagnose(ref_fit)
# bayes_pp_check(ref_fit)
```

### Search versus evaluation

The same observations used to search a variable path can make the path
look better than it will generalize. Cross-validated selection evaluates
the reduction process out of sample.
[`bayes_projection_select()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_projection_select.md)
therefore defaults to cross-validation.

Current `projpred` workflows support LOO or K-fold cross-validation.
K-fold validation can be preferable when the predictive unit requires
group-wise partitioning that cannot be represented by ordinary
observation-wise LOO.

### Cross-validated forward search

``` r

# projection <- bayes_projection_select(
#   ref_fit,
#   cross_validate = TRUE,
#   cv_method = "LOO",
#   method = "forward",
#   nterms_max = 8,
#   validate_search = TRUE,
#   seed = 20260824
# )
# projection
```

### K-fold projection

``` r

# projection_k <- bayes_projection_select(
#   ref_fit,
#   cross_validate = TRUE,
#   cv_method = "kfold",
#   K = 5,
#   method = "forward",
#   seed = 20260824
# )
```

### Exploratory non-cross-validated search

A non-cross-validated path can be useful for rapid exploration or
debugging, but it should not be confused with an independently evaluated
reduction. Version 1.0.0 permits this path only when the analyst
requests it explicitly.

``` r

# exploratory <- bayes_projection_select(
#   ref_fit,
#   cross_validate = FALSE,
#   method = "L1",
#   nterms_max = 8
# )
```

### Ranking and suggested size

[`bayes_projection_summary()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_projection_summary.md)
exposes the search ranking and calls the `projpred` size-suggestion
machinery. The suggested size is a performance-based heuristic. It does
not know which predictor is expensive to measure, which term is mandated
by hierarchy, or which variable has direct scientific importance.

The final model size should therefore be supported by the predictive
path and by domain constraints.

``` r

# ps <- bayes_projection_summary(projection, stat = "elpd", pct = 0.00)
# ps$ranking
# ps$suggested_size
# ps$performance
```

### Allowing a small predictive sacrifice

A smaller deployment model may be worthwhile when sampling or laboratory
costs are high. The `pct` argument can express an accepted predictive
sacrifice in the size heuristic, but this should be treated as a
declared decision criterion rather than as a default statistical
threshold.

``` r

# bayes_projection_summary(projection, stat = "elpd", pct = 0.05)
```

### Projecting the posterior

After a submodel size is chosen, projection transfers information from
the reference posterior to the submodel parameters. The resulting
projected posterior is not obtained by simply refitting the selected
variables from scratch with the same priors.

[`bayes_projection_predict()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_projection_predict.md)
exposes projected prediction at a chosen number of terms.

``` r

# new_x <- dat[1:20, setdiff(names(dat), "yield"), drop = FALSE]
# pp <- bayes_projection_predict(
#   projection,
#   newdata = new_x,
#   nterms = 5,
#   ndraws = 1000,
#   seed = 20260824
# )
# dim(pp$draws)
```

### Hierarchical terms and interactions

- If an interaction is retained, consider whether its lower-order terms
  must remain for interpretation.
- Polynomial bases should be treated as structured groups when
  scientific hierarchy requires it.
- Factor contrasts create multiple coefficients for one scientific
  predictor; selection should respect the intended term-level meaning.
- Random-effect structures should not be reduced mechanically as if
  grouping effects were ordinary fixed predictors.

### Correlated predictors

Projection can expose groups of nearly exchangeable predictors. If two
soil indicators carry similar predictive information, the selected
member can change across resamples even when predictive performance
remains stable.

This is not necessarily a failure. It indicates that variable identity
is less stable than the predictive information represented by the group.
Report this distinction rather than declaring the selected variable
uniquely causal.

### Predictive unit and leakage

- For repeated measures, folds should usually respect subjects when
  prediction concerns new subjects.
- For multi-environment trials, leaving out individual plots is
  different from leaving out an environment.
- For spatial trials, neighboring plots can leak information across
  ordinary random folds.
- For temporal data, future prediction requires a validation scheme that
  respects ordering.

### Comparing reduced and reference predictions

After projection, inspect the actual predictive distributions, not only
a scalar score. For agronomic decisions, a small average ELPD difference
can still matter if the reduced model distorts upper-tail yield, disease
risk, or treatment contrasts in a critical region.

Posterior predictive checks should be repeated for the quantities that
matter scientifically.

### Selection is not evidence for absence

A predictor omitted from a small projected model has not been proven to
have zero effect. It may be redundant with retained predictors or
unnecessary for approximating the reference predictions at the chosen
complexity.

If the scientific question is evidence for a null effect, use a method
designed for that question and specify the relevant hypothesis and
prior.

### Projection versus shrinkage

Regularizing priors can retain all candidate predictors while shrinking
weak coefficients. Projection is useful when a physically smaller
formula is needed for measurement, deployment, explanation, or
computational reasons.

The two approaches are complementary: a regularized reference model can
be projected to a smaller model.

### Projection versus stacking

Stacking combines several predictive models. Projection instead uses a
reference model to construct one or more smaller approximating
submodels. A workflow can use either or both, but the target must be
clear.

If deployment requires one compact formula, projection is often more
directly aligned with that operational constraint than an ensemble.

| Question | Projection predictive selection | Predictive stacking |
|----|----|----|
| Primary object | Reference model and submodel path | Candidate predictive distributions |
| Goal | Compact approximation | Combined predictive performance |
| Output | Selected/projected submodel | Model weights and mixture prediction |
| Interpretation | Information retained from reference | Contribution to predictive ensemble |

### Common failure modes

- Fitting an underpowered or misspecified reference model and treating
  projection as a remedy.
- Choosing the smallest suggested size without inspecting uncertainty in
  the performance path.
- Ignoring hierarchical terms or measurement cost when interpreting the
  selected path.
- Using observation-level LOO when the true generalization unit is a
  field, environment, animal, or year.
- Reporting omission as evidence that a biological effect is absent.

### Reporting checklist

- Describe the reference model, priors, and diagnostics.
- State the selection method and whether search was cross-validated.
- Describe the predictive unit and CV scheme.
- Report the ranking/path and the rule used to choose model size.
- Report predictive degradation relative to the reference model.
- Explain scientific constraints that altered or confirmed the
  algorithmic suggestion.
- Provide projected prediction checks for the final submodel.

### Reproducible starter workflow

``` r

# 1. Build a scientifically rich, regularized reference model.
# 2. Diagnose the reference posterior and posterior predictive distribution.
# 3. Choose the generalization unit and CV scheme before variable search.
# 4. projection <- bayes_projection_select(ref_fit, cross_validate = TRUE)
# 5. inspect <- bayes_projection_summary(projection)
# 6. Inspect the entire predictive path; do not accept suggested_size mechanically.
# 7. Project the chosen size and check predictions on scientifically relevant quantities.
# 8. Record measurement cost, hierarchy, and domain reasons for the final formula.
```

### References and continuation

Piironen and Vehtari (2017) provides a broad comparison of Bayesian
predictive selection approaches. Pavone et al. (2023) develops
reference-model variable-selection reasoning and practical methodology.

Once a predictive model or ensemble has been chosen,
`v28-bayesian-decision-analysis-utility-and-regret.Rmd` shows how
posterior outcome uncertainty is converted into explicit actions without
confusing predictive quality with decision value.

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

#### One predictor enters first in most folds

Describe it as consistently useful for approximating the reference
prediction, not as uniquely causal.

#### Two correlated predictors alternate

Report information redundancy and selection instability at the
variable-identity level.

#### Performance plateaus after four terms

A four-term model may be operationally attractive if predictive
degradation is acceptably small and scientific hierarchy is respected.

#### Suggested size is zero or one

Verify that the reference model itself has meaningful predictive signal
before celebrating extreme parsimony.

#### Large gap from reference

Do not force a small model merely because a preplanned maximum number of
terms was requested.

#### K-fold and LOO disagree

Investigate whether the predictive unit, influential observations, or
dependence structure makes one validation design more appropriate.

#### Noise variable is selected

Inspect correlation, leakage, reference-model fit, and selection
stability across folds before interpreting the variable.

#### Important scientific covariate is omitted

Separate the predictive reduction objective from the scientific
estimand; a covariate may remain essential for design adjustment even
when redundant for prediction.

#### Interaction selected without lower-order term

Apply the scientific hierarchy rule declared for the analysis rather
than accepting an uninterpretable formula mechanically.

#### Final deployment model differs from suggested size

Document the operational or scientific constraint that motivated the
final choice.

### Glossary for this vignette

#### Reference model

Rich Bayesian model whose predictive information is used as the target
for projection.

#### Projection

Optimization that maps the reference predictive behavior into a reduced
submodel.

#### Forward search

Sequential addition of terms along a candidate reduction path.

#### L1 search

Alternative sparse search strategy supported by projpred.

#### Cross-validated search

Selection workflow evaluated over held-out predictive units.

#### ELPD

Expected log predictive density used to compare predictive performance.

#### Suggested size

Heuristic submodel size returned from the predictive path under a
specified tolerance.

#### Predictive degradation

Loss in predictive performance relative to the reference model.

#### Selection stability

Consistency of selected terms or predictive performance across
resampling schemes.

#### Deployment model

Final reduced model selected for operational use after statistical and
scientific constraints are considered.

### Questions to answer before publication

#### Was the reference model adequate?

Report posterior diagnostics and predictive checks before discussing
reduction.

#### Was the search cross-validated?

State the search/evaluation mode and why it matches the inferential
goal.

#### What was held out?

Describe the predictive unit and any grouped or blocked
cross-validation.

#### How was model size chosen?

Give the predictive criterion, tolerance, and any scientific
constraints.

#### Were terms grouped?

Explain treatment of factors, polynomials, interactions, splines, or
other multi-coefficient scientific terms.

#### How stable was the path?

Describe fold-to-fold or resampling variation when it affects
interpretation.

#### Was the final submodel checked?

Show projected predictive performance for quantities that matter
scientifically.

#### What does omission mean?

State that omission from a predictive submodel is not proof of a zero
scientific effect.

Pavone, Federico, Juho Piironen, Paul-Christian B "urkner, and Aki
Vehtari. 2023. “Using Reference Models in Variable Selection.”
*Computational Statistics* 38 (1): 349–71.
<https://doi.org/10.1007/s00180-022-01231-6>.

Piironen, Juho, and Aki Vehtari. 2017. “Comparison of Bayesian
Predictive Methods for Model Selection.” *Statistics and Computing* 27
(3): 711–35. <https://doi.org/10.1007/s11222-016-9649-y>.
