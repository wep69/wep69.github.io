# Bayesian Repeated Measures and Residual Dependence

## 1. Why repeated measurements require a different model

Repeated measurements are not ordinary replications. When the same plot,
plant, pot, animal, lysimeter, or other experimental unit is observed
more than once, observations from that unit are usually dependent.
Treating all rows as independent artificially increases the apparent
amount of information and can lead to overly precise treatment
comparisons.

The first task is therefore experimental, not computational: identify
the unit that was independently randomized and the unit that is
repeatedly observed. The `subject` argument in
[`bayes_repeated()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_repeated.md)
refers to this repeated experimental-unit identifier. The word *subject*
is generic and may represent a field plot, pot, tree, animal, sampling
location, or another unit.

Repeated-measures models are naturally multilevel because repeated
observations are nested within experimental units. The general logic of
partial pooling and hierarchical variation follows standard
multilevel-model principles (Gelman and Hill 2007; Pinheiro and Bates
2000).

## 2. Learning objectives

After completing this vignette, the reader should be able to:

1.  identify the repeated experimental unit;
2.  distinguish replication from repeated observation;
3.  specify treatment-by-time effects without treating time as a
    nuisance;
4.  distinguish subject-specific random slopes from residual
    autocorrelation;
5.  understand why these two dependence mechanisms can coexist;
6.  specify an optional AR(1) residual structure;
7.  request heterogeneous residual variances when scientifically
    justified;
8.  configure `brms` with `cmdstanr` and an explicit CPU budget;
9.  interpret population-level and unit-level variation separately;
10. inspect posterior predictive adequacy and MCMC diagnostics;
11. obtain predictions on the response scale;
12. compare alternative repeated-measures models with PSIS-LOO;
13. use the frequentist model only as a pedagogical crosswalk;
14. report repeated-measures analyses transparently and reproducibly.

## 3. Teaching data

The bundled dataset contains 24 experimental plots measured at five
times. Plots belong to three treatment groups. The data were simulated
with plot-specific intercepts and slopes and with serially dependent
residual variation.

``` r

d <- bayes_teaching_data("repeated")
head(d)
#>   plot_id block treatment time   height
#> 1     P01    B1   Control    0 20.52970
#> 2     P01    B1   Control    7 23.93143
#> 3     P01    B1   Control   14 27.89306
#> 4     P01    B1   Control   21 32.76466
#> 5     P01    B1   Control   28 37.18323
#> 6     P02    B2   Control    0 17.55035
dim(d)
#> [1] 120   5
with(d, table(treatment, time))
#>               time
#> treatment      0 7 14 21 28
#>   Biostimulant 8 8  8  8  8
#>   Control      8 8  8  8  8
#>   Silicon      8 8  8  8  8
length(unique(d$plot_id))
#> [1] 24
```

These data are frozen teaching data. They are not empirical agronomic
evidence.

## 4. Draw the design before writing a model

For a simple longitudinal plot experiment, the hierarchy can be
represented as:

``` text
Treatment assignment
      |
      v
 Experimental plot
      |
      +---- time 1
      +---- time 2
      +---- time 3
      +---- time 4
      +---- time 5
```

The five measurements within one plot do not represent five
independently randomized plots. The plot remains the experimental unit
for treatment.

A useful audit question is:

> If all repeated observations from one plot disappeared, how many
> independent treatment assignments would be lost?

The answer is one. That fact should be reflected in the model.

## 5. Default repeated-measures specification

The convenience wrapper constructs a treatment-by-time fixed structure
and a plot-specific random intercept plus plot-specific time slope by
default.

``` r

des <- bayes_repeated(
  data = d,
  response = "height",
  subject = "plot_id",
  time = "time",
  treatment = "treatment"
)

des
#> <bayes_design>
#>   Type: Repeated-measures mixed model
#>   Response: height
#>   Family: Gaussian
#>   Observations: 120
#>   treatment: treatment
#>   fixed: treatment, time
#>   group: plot_id
#>   subject: plot_id
#>   time: time
#>   random_slopes: plot_id:[time]
```

Conceptually, the population-level part is

``` math
E(y_{ijt}) = \beta_0 + \beta_{T_i} + \beta_1 t +
             \beta_{T_i\times t}t + \ldots,
```

while the plot-level component allows plots to have their own baseline
and time response.

## 6. Audit the repeated structure

``` r

aud <- bayes_design_audit(des)
aud
#> <bayes_design_audit>
#>   Design: Repeated-measures mixed model
#>   Status: PASS
aud$checks$subject_time_unique
#> [1] TRUE
aud$checks$observations_per_subject
#> 
#> P01 P02 P03 P04 P05 P06 P07 P08 P09 P10 P11 P12 P13 P14 P15 P16 P17 P18 P19 P20 
#>   5   5   5   5   5   5   5   5   5   5   5   5   5   5   5   5   5   5   5   5 
#> P21 P22 P23 P24 
#>   5   5   5   5
```

The design audit checks structural features that can be evaluated from
the recorded data. In particular, a subject-time combination should
identify at most one measurement for this simple repeated-measures
grammar.

An audit cannot determine whether every scientific source of dependence
was recorded. If, for example, several plots share a greenhouse bench
and bench was omitted from the data, no statistical function can
reconstruct that randomization level reliably.

## 7. Random intercepts and random slopes answer different questions

A random intercept allows plots to differ in baseline response:

``` math
b_{0j} \sim N(0, \sigma_{b0}^2).
```

A random time slope allows the rate of temporal change to vary among
plots:

``` math
b_{1j} \sim N(0, \sigma_{b1}^2).
```

The model can estimate a correlation between these two plot-level
effects. For example, plots that start taller may systematically grow
more slowly or more rapidly.

To request only a plot-specific random intercept:

``` r

des_ri <- bayes_repeated(
  d, "height", "plot_id", "time", "treatment",
  random_slope = FALSE
)
bayes_plan(des_ri)
#> <bayes_plan>
#>   Design: Repeated-measures mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

To request the default random slope:

``` r

des_rs <- bayes_repeated(
  d, "height", "plot_id", "time", "treatment",
  random_slope = TRUE
)
bayes_plan(des_rs)
#> <bayes_plan>
#>   Design: Repeated-measures mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

Whether a random slope is useful is a scientific and predictive
question, not a ritual requirement.

## 8. Random slopes are not residual autocorrelation

This distinction is central.

A random slope describes **persistent between-unit heterogeneity** in
the trajectory. Residual autocorrelation describes **within-unit
dependence that remains after the modeled trajectory and group-level
effects have been taken into account**.

The two structures can therefore coexist.

``` text
Observed repeated response
        |
        +-- population treatment/time trajectory
        |
        +-- plot-specific baseline and slope
        |
        +-- residual serial dependence
        |
        +-- remaining measurement noise
```

Confusing these levels can lead to unnecessarily complicated models or
to models that fail to represent the actual dependence.

## 9. AR(1) residual dependence

Version 1.0.0 offers an AR(1) convenience structure for Gaussian
repeated models.

``` r

des_ar1 <- bayes_repeated(
  d,
  response = "height",
  subject = "plot_id",
  time = "time",
  treatment = "treatment",
  correlation = "ar1"
)

bayes_plan(des_ar1)
#> <bayes_plan>
#>   Design: Repeated-measures mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

An AR(1) process assumes that residual dependence decays geometrically
with time separation. If $`\rho`$ is the lag-one residual correlation,
then the correlation at lag $`k`$ is approximately

``` math
\rho^k.
```

This is not a universal law of repeated measurements. It is a particular
model that should be checked against the sampling schedule and the
biology of the process.

## 10. Time spacing matters

The current convenience AR(1) interface expects a numeric time variable
and a regular interpretation of lag. If observations occur at strongly
irregular intervals, an AR(1) lag based only on observation order may be
scientifically misleading.

For irregular time, a later continuous-time residual-correlation
extension is more appropriate. Until that extension is implemented, the
expert user should fit an explicitly justified `brms` or Stan model
rather than force the convenience AR(1) option.

## 11. Treatment-by-time interaction

A treatment main effect by itself does not answer whether treatments
change differently over time. The default fixed structure therefore
includes `treatment * time` when a treatment variable is supplied.

The interaction represents treatment-specific changes in the temporal
slope within the chosen linear-time representation.

A scientifically useful interpretation asks:

- Are treatment trajectories separated at the beginning?
- Do slopes differ?
- At which observed times are differences practically important?
- Are apparent differences within the uncertainty supported by the
  posterior?

A single global interaction label should not replace these questions.

## 12. Custom fixed-effects structure

The user may supply a one-sided fixed formula explicitly.

``` r

des_custom <- bayes_repeated(
  d,
  response = "height",
  subject = "plot_id",
  time = "time",
  treatment = "treatment",
  fixed = ~ treatment + time + treatment:time,
  random_slope = TRUE
)

des_custom
#> <bayes_design>
#>   Type: Repeated-measures mixed model
#>   Response: height
#>   Family: Gaussian
#>   Observations: 120
#>   treatment: treatment
#>   fixed: treatment, time
#>   group: plot_id
#>   subject: plot_id
#>   time: time
#>   random_slopes: plot_id:[time]
```

This becomes especially useful when the time trajectory is transformed
or when additional baseline covariates are incorporated.

## 13. Heterogeneous residual variances

Repeated-measures data may exhibit residual variability that changes
across biologically meaningful groups. Version 1.0.0 can request
group-specific residual SDs for Gaussian and Student-t models.

For example, if residual variability differs by treatment:

``` r

des_het <- bayes_repeated(
  d,
  response = "height",
  subject = "plot_id",
  time = "time",
  treatment = "treatment",
  residual_group = "treatment"
)

bayes_plan(des_het)
```

The `brms` backend compiles this as a distributional model for `sigma`.
Heterogeneity should be modeled because the data-generating process
requires it, not because a variance test crossed a conventional
threshold.

## 14. Student-t repeated model

A Student-t likelihood can be useful when continuous responses have
heavier tails than a Gaussian model can represent adequately.

``` r

des_t <- bayes_repeated(
  d,
  response = "height",
  subject = "plot_id",
  time = "time",
  treatment = "treatment",
  family = "student",
  correlation = "none"
)
bayes_plan(des_t)
#> <bayes_plan>
#>   Design: Repeated-measures mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

Version 1.0.0 restricts the AR(1) convenience path to Gaussian repeated
models. This is an explicit implementation boundary, not a claim that
other combinations are mathematically impossible.

## 15. Priors should respect the measurement scale

A repeated model requires priors for several parameter classes:

- intercept and fixed effects;
- subject-level SDs;
- random-effect correlations;
- residual SD;
- AR coefficient when present;
- Student-t degrees of freedom when applicable.

``` r

pr <- bayes_prior("regularizing")
pr
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
```

The default object is a transparent starting point. A substantive
analysis should ask whether prior slopes imply plausible change per unit
time and whether prior group-level variability allows biologically
impossible trajectories.

## 16. Prior predictive checking

The exact hierarchical prior predictive distribution should be checked
with the Stan path.

``` r

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  seed = 20260824
)

prior_fit <- bayes_fit(
  des_ar1,
  engine = "brms",
  prior = pr,
  compute = ctrl,
  sample_prior = "only"
)

bayes_pp_check(prior_fit)
```

For repeated data, prior predictive assessment should examine entire
trajectories, not only marginal response ranges.

## 17. CmdStanR fitting

The full repeated-measures model requires the optional `brms`/Stan path.

``` r

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  seed = 20260824
)

fit <- bayes_fit(
  des_ar1,
  engine = "brms",
  prior = pr,
  compute = ctrl
)
```

The CPU budget remains explicit. Four chains can run in parallel without
requiring within-chain threading.

## 18. Diagnostics before interpretation

``` r

bayes_diagnose(fit)
```

At minimum, inspect:

- R-hat;
- bulk and tail effective sample sizes;
- Monte Carlo standard errors;
- divergent transitions;
- maximum-tree-depth warnings;
- energy diagnostics when available;
- trace plots for difficult parameters.

A visually attractive treatment-by-time graph does not compensate for an
unreliable sampler.

## 19. Variance components

``` r

bayes_variance_components(fit)
```

Variance components describe different sources of variability. For a
repeated model, distinguish subject-level intercept variation,
subject-level slope variation, their correlation, and residual
variation.

These components are not merely nuisance quantities. They help explain
how much heterogeneity exists among experimental units and how strongly
units vary in their temporal response.

## 20. Unit-specific random effects

``` r

bayes_random_effects(fit, group = "plot_id")
```

Unit-specific estimates are partially pooled. They should not be ranked
as if every difference among plots were a fixed treatment contrast.

## 21. Population-average response-scale predictions

Use
[`bayes_predict()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_predict.md)
to work directly with expected responses rather than interpreting every
coefficient in isolation.

``` r

nd <- expand.grid(
  treatment = levels(d$treatment),
  time = sort(unique(d$time))
)

pred <- bayes_predict(
  fit,
  newdata = nd,
  type = "mean",
  re_formula = NA
)

cbind(nd, pred$summary)
```

`re_formula = NA` requests population-level predictions from the `brms`
backend. Conditional predictions for existing units answer a different
question and should be labeled accordingly.

## 22. Predictive rather than mean uncertainty

`type = "mean"` describes uncertainty in the expected response.
`type = "predictive"` additionally includes observation-level variation.

``` r

pred_y <- bayes_predict(
  fit,
  newdata = nd,
  type = "predictive",
  re_formula = NA
)
```

These intervals should not be interchanged in reporting.

## 23. Treatment comparisons at scientifically relevant times

Version 1.0.0 does not provide a specialized `contrast_at_time()`
wrapper. Instead, construct a prediction grid and compare posterior
prediction draws for the exact times of scientific interest.

This keeps the estimand explicit and avoids pretending that a single
main effect answers a time-specific treatment question.

For a publication analysis, report the exact times or time range used in
each comparison and define any ROPE or meaningful difference on the
response scale.

## 24. Posterior predictive checks

``` r

bayes_pp_check(fit)
```

Useful repeated-measures checks include:

- marginal response distribution;
- treatment-specific trajectories;
- within-unit variation;
- extreme values;
- temporal residual pattern;
- variance by time or treatment;
- replicated versus observed trajectory shapes.

A model can have excellent MCMC diagnostics and still fail to mimic
important features of the data.

## 25. Model comparison with PSIS-LOO

A scientifically useful comparison might ask whether the random time
slope is predictively useful.

``` r

fit_ri <- bayes_fit(des_ri, engine = "brms", prior = pr, compute = ctrl)
fit_rs <- bayes_fit(des_rs, engine = "brms", prior = pr, compute = ctrl)

bayes_compare_models(
  random_intercept = fit_ri,
  random_slope = fit_rs
)
```

PSIS-LOO addresses predictive adequacy. It is not a Bayes factor and
should not be described as evidence for a point-null hypothesis.

## 26. AR(1) model comparison

The same principle can compare serial-dependence structures.

``` r

fit_none <- bayes_fit(
  bayes_repeated(d, "height", "plot_id", "time", "treatment",
                 correlation = "none"),
  engine = "brms", prior = pr, compute = ctrl
)

fit_ar <- bayes_fit(
  bayes_repeated(d, "height", "plot_id", "time", "treatment",
                 correlation = "ar1"),
  engine = "brms", prior = pr, compute = ctrl
)

bayes_compare_models(no_ar = fit_none, ar1 = fit_ar)
```

Model comparison should be accompanied by posterior predictive checks
and scientific interpretation of the added structure.

## 27. Frequentist crosswalk

``` r

bayes_compare_frequentist(fit_rs)
```

The crosswalk is deliberately pedagogical. In particular, the current
frequentist mixed-model comparator does not reproduce the Bayesian AR(1)
or heterogeneous-sigma structure exactly. The function warns about this
limitation rather than implying equivalence.

## 28. Missing repeated observations

An unbalanced number of observations per unit is not automatically fatal
to a multilevel model. Version 1.0.0 also provides a dedicated Bayesian
missing-predictor workflow; nevertheless, the repeated-measures
convenience path does not silently impute missing responses. Joint
continuous missing-data workflows are available through
[`bayes_missing()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_missing.md)
and are developed in v21; the repeated-measures wrapper itself does not
infer a missingness mechanism.

Before fitting, distinguish:

- a planned absence of a measurement occasion;
- an unrecorded response;
- dropout related to previous outcomes;
- a missing covariate;
- a genuinely absent experimental unit.

These mechanisms have different inferential implications.

## 29. Unequal time intervals

If measurement times are 0, 1, 2, 3, and 4 days, an AR(1) lag is easy to
interpret. If times are 0, 1, 3, 10, and 30 days, the same discrete-lag
model requires much stronger justification.

For irregular sampling, consider a custom continuous-time model in a
future extension or an expert Stan specification.

## 30. Common mistakes

Avoid the following:

1.  treating repeated rows as independent replicates;
2.  using `plot_id` as an ordinary fixed treatment factor merely to
    absorb dependence;
3.  adding random slopes when time does not vary within the grouping
    factor;
4.  assuming a random slope automatically solves residual
    autocorrelation;
5.  assuming AR(1) is appropriate for every time series;
6.  choosing covariance structure solely from a single information
    criterion;
7.  interpreting coefficients before checking posterior computation;
8.  using a point-null BF as a measure of effect magnitude;
9.  reporting only a treatment main effect when the interaction with
    time is scientifically important;
10. ignoring the scale on which a practical difference is defined.

## 31. Reporting checklist

A transparent report should state:

- the experimental unit and repeated unit identifier;
- number and timing of measurements;
- treatment allocation level;
- fixed-effects formula;
- random intercepts and slopes;
- residual-correlation structure;
- residual-variance structure;
- response likelihood;
- priors and their justification;
- prior predictive assessment;
- sampler, chains, iterations, warmup, and seed;
- R-hat and ESS diagnostics;
- posterior predictive checks;
- posterior treatment/time estimates and intervals;
- response-scale contrasts and practical thresholds;
- sensitivity or model-comparison results where relevant.

This reporting logic follows the broader transparency principles in the
BARG framework (Kruschke 2021).

## 32. Complete starter workflow

``` r

library(bayesAgriFlow)

d <- bayes_teaching_data("repeated")

des <- bayes_repeated(
  d,
  response = "height",
  subject = "plot_id",
  time = "time",
  treatment = "treatment",
  random_slope = TRUE,
  correlation = "ar1"
)

bayes_design_audit(des)
bayes_plan(des)

pr <- bayes_prior("regularizing")

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  seed = 20260824
)

fit <- bayes_fit(des, engine = "brms", prior = pr, compute = ctrl)

bayes_diagnose(fit)
bayes_pp_check(fit)
bayes_summary(fit)
bayes_variance_components(fit)

nd <- expand.grid(
  treatment = levels(d$treatment),
  time = sort(unique(d$time))
)

bayes_predict(fit, newdata = nd, type = "mean", re_formula = NA)
```

## 33. Scope of version 1.0.0

Version 1.0.0 establishes the common repeated-measures grammar for
continuous Gaussian and Student-t responses, optional subject-specific
time slopes, optional heterogeneous residual SD, and a Gaussian AR(1)
convenience structure.

Future versions may add continuous-time correlation, richer residual
covariance structures, nonlinear time curves, non-Gaussian longitudinal
models, joint missing-data models, multivariate repeated responses, and
spatiotemporal dependence. Those additions should reuse the same
explicit roles for subject, time, grouping, likelihood, and
computational backend.

Gelman, Andrew, and Jennifer Hill. 2007. *Data Analysis Using Regression
and Multilevel/Hierarchical Models*. Analytical Methods for Social
Research. Cambridge University Press.
<https://doi.org/10.1017/CBO9780511790942>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.

Pinheiro, José C., and Douglas M. Bates. 2000. *Mixed-Effects Models in
s and s-PLUS*. Statistics and Computing. Springer.
<https://doi.org/10.1007/b98882>.
