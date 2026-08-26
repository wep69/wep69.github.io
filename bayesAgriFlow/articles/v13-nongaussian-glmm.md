# Bayesian Generalized Linear Mixed Models for Agronomic Responses

## 1. Why the response distribution matters

Many agronomic responses are not well represented by a Gaussian
distribution. Examples include insect counts, numbers of diseased
plants, germination successes, proportions, strictly positive biomass or
concentration measures, and heavily right-skewed positive responses.

A generalized linear mixed model (GLMM) represents the response on a
likelihood appropriate to its measurement process while retaining the
hierarchical structure of blocks, sites, plots, or other grouping
factors. This is preferable to mechanically transforming every response
in an attempt to reproduce a Gaussian ANOVA.

The practical issues surrounding GLMM specification, overdispersion,
grouping structure, and interpretation are well established in applied
modeling (Bolker et al. 2009). Bayesian implementation adds priors,
posterior uncertainty, predictive checking, and direct posterior
probability statements.

## 2. Learning objectives

After completing this vignette, the reader should be able to:

1.  choose a likelihood from the measurement process rather than from a
    ritual transformation rule;
2.  distinguish Bernoulli from binomial data;
3.  distinguish Poisson from negative-binomial count models;
4.  use an exposure offset for count rates;
5.  recognize the roles of Gamma, lognormal, and Beta families;
6.  understand the link scale and the response scale;
7.  specify hierarchical random effects in a GLMM;
8.  choose priors that are meaningful on the model’s scale;
9.  fit GLMMs with `brms` and `cmdstanr`;
10. diagnose posterior computation;
11. evaluate model adequacy with posterior predictive checks;
12. obtain response-scale predictions;
13. compare models with PSIS-LOO;
14. separate predictive model comparison from Bayes-factor hypothesis
    testing;
15. report a non-Gaussian hierarchical analysis transparently.

## 3. Supported families in version 1.0.0

``` r

bayes_family_info()
#>        family default_link                           response         engine
#> 1    gaussian     identity                    continuous real native or brms
#> 2     student     identity continuous real with heavier tails           brms
#> 3   bernoulli        logit                         binary 0/1           brms
#> 4    binomial        logit            successes out of trials           brms
#> 5     poisson          log                non-negative counts           brms
#> 6 negbinomial          log  overdispersed non-negative counts           brms
#> 7       gamma          log                positive continuous           brms
#> 8   lognormal     identity                positive continuous           brms
#> 9        beta        logit     continuous proportion in (0,1)           brms
```

The package recognizes:

``` text
gaussian
student
bernoulli
binomial
poisson
negbinomial
gamma
lognormal
beta
```

The first two are also used in continuous mixed-model wrappers. The
remaining families are exposed through
[`bayes_glmm()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_glmm.md).

## 4. Family choice is part of the scientific model

A likelihood is a statement about how observations vary conditionally on
the modeled parameters. It should be chosen from the response-generating
process and checked against the data.

A useful sequence is:

``` text
What is observed?
      |
      +-- binary outcome for one unit -> Bernoulli
      +-- successes out of known trials -> Binomial
      +-- non-negative event count -> Poisson or Negative Binomial
      +-- positive continuous response -> Gamma or Lognormal
      +-- continuous proportion strictly between 0 and 1 -> Beta
```

This decision should be documented before interpreting coefficients.

## 5. Count-data teaching example

``` r

dc <- bayes_teaching_data("glmm_count")
head(dc)
#>   block treatment replicate insects exposure
#> 1    B1   Control         1      15       10
#> 2    B1   Control         2       1       10
#> 3    B1   Control         3       5       10
#> 4    B1      BioA         1       9       10
#> 5    B1      BioA         2       4       10
#> 6    B1      BioA         3       7       10
summary(dc$insects)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   0.000   4.000   7.000   7.486  10.000  26.000
with(dc, table(block, treatment))
#>      treatment
#> block BioA BioB BioC Control
#>    B1    3    3    3       3
#>    B2    3    3    3       3
#>    B3    3    3    3       3
#>    B4    3    3    3       3
#>    B5    3    3    3       3
#>    B6    3    3    3       3
```

The response is a non-negative integer count. The teaching data were
generated with extra-Poisson variation so that a negative-binomial model
is a sensible starting point.

## 6. Negative-binomial GLMM

``` r

des_nb <- bayes_glmm(
  data = dc,
  response = "insects",
  fixed = ~ treatment,
  group = "block",
  family = "negbinomial"
)

des_nb
#> <bayes_design>
#>   Type: Generalized linear mixed model
#>   Response: insects
#>   Family: Negative binomial
#>   Observations: 72
#>   fixed: treatment
#>   group: block
bayes_design_audit(des_nb)
#> <bayes_design_audit>
#>   Design: Generalized linear mixed model
#>   Status: PASS
bayes_plan(des_nb)
#> <bayes_plan>
#>   Design: Generalized linear mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

The conceptual model contains a population-level treatment effect and a
block-specific random intercept.

## 7. Why not use ordinary Gaussian ANOVA on counts?

Counts are discrete, bounded below by zero, and frequently have a
variance that changes with their mean. A Gaussian model can therefore
assign positive probability to impossible negative counts and can
misrepresent uncertainty, especially when counts are small or skewed.

A transformed Gaussian model may sometimes be useful, but transformation
is not automatically superior to directly modeling the count-generating
process.

## 8. Poisson versus negative binomial

For a Poisson model,

``` math
E(Y_i \mid \theta_i) = Var(Y_i \mid \theta_i) = \lambda_i.
```

Agronomic counts often show more variability than this conditional
mean-variance relation permits. A negative-binomial likelihood
introduces an additional dispersion parameter and can represent
extra-Poisson variation.

The choice should be evaluated using posterior predictive checks and,
when scientifically useful, predictive comparison.

## 9. Poisson model for comparison

``` r

des_p <- bayes_glmm(
  dc,
  response = "insects",
  fixed = ~ treatment,
  group = "block",
  family = "poisson"
)
bayes_plan(des_p)
#> <bayes_plan>
#>   Design: Generalized linear mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

Do not decide between Poisson and negative binomial by checking whether
one frequentist dispersion test crosses 0.05. Examine the full
predictive behavior and the scientific data-generating process.

## 10. Exposure offsets

Counts observed over unequal areas, trapping durations, plant numbers,
or other exposures should not generally be compared as raw counts.

The teaching dataset includes a positive exposure variable.

``` r

summary(dc$exposure)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>      10      10      10      10      10      10

des_rate <- bayes_glmm(
  dc,
  response = "insects",
  fixed = ~ treatment,
  group = "block",
  family = "poisson",
  exposure = "exposure"
)

bayes_plan(des_rate)
#> <bayes_plan>
#>   Design: Generalized linear mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

The compiled count model adds `offset(log(exposure))`. Thus treatment
effects are interpreted relative to the modeled event rate, conditional
on the link function.

## 11. Binomial teaching example

``` r

db <- bayes_teaching_data("glmm_binomial")
head(db)
#>   block treatment replicate infected plants
#> 1    B1   Control         1        8     20
#> 2    B1   Control         2        6     20
#> 3    B1      BioA         1        5     20
#> 4    B1      BioA         2        7     20
#> 5    B1      BioB         1        2     20
#> 6    B1      BioB         2        5     20
summary(db$infected)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>   2.000   6.000   7.500   7.729   9.000  14.000
summary(db$plants)
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>      20      20      20      20      20      20
all(db$infected <= db$plants)
#> [1] TRUE
```

Each row records infected plants out of a known number of plants.

``` r

des_bin <- bayes_glmm(
  db,
  response = "infected",
  fixed = ~ treatment,
  group = "block",
  family = "binomial",
  trials = "plants"
)

bayes_design_audit(des_bin)
#> <bayes_design_audit>
#>   Design: Generalized linear mixed model
#>   Status: PASS
bayes_plan(des_bin)
#> <bayes_plan>
#>   Design: Generalized linear mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```

## 12. Bernoulli versus binomial

Use Bernoulli when each row is one binary outcome, such as diseased
versus healthy for one plant. Use binomial when a row contains a number
of successes out of a known number of trials.

Collapsing individual Bernoulli observations into binomial counts is
only innocuous when the observations share the same modeled success
probability conditional on the predictors and grouping structure.

## 13. Link-scale interpretation

For a logit-linked binomial model,

``` math
\text{logit}(p_i) = \eta_i.
```

A coefficient is therefore additive on the log-odds scale, not directly
on the probability scale. This is one reason response-scale prediction
is central to `bayesAgriFlow`.

A coefficient of 0.5 on the logit scale should not be reported as a 0.5
increase in infection probability.

## 14. Response-scale predictions

After a local Stan fit:

``` r

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  seed = 20260824
)

fit_bin <- bayes_fit(
  des_bin,
  engine = "brms",
  prior = bayes_prior("regularizing"),
  compute = ctrl
)

nd <- data.frame(
  treatment = factor(levels(db$treatment), levels = levels(db$treatment)),
  plants = 20
)

pred <- bayes_predict(
  fit_bin,
  newdata = nd,
  type = "mean",
  re_formula = NA
)

cbind(nd, pred$summary)
```

For nonlinear links, differences on the response scale depend on the
complete linear predictor. Response-scale predictions are therefore
usually clearer for applied interpretation.

## 15. Priors on non-Gaussian models

Priors on regression coefficients generally operate on the link scale. A
Normal prior that seems numerically small may imply a wide range of
response probabilities or count-rate ratios.

The package supplies regularizing starting priors, but a research
analysis should examine what those priors imply for observable outcomes.

``` r

pr <- bayes_prior("regularizing")
pr
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
```

## 16. Prior predictive checking

For the exact GLMM prior predictive distribution, use the Stan backend
with prior-only sampling.

``` r

prior_nb <- bayes_fit(
  des_nb,
  engine = "brms",
  prior = pr,
  compute = ctrl,
  sample_prior = "only"
)

bayes_pp_check(prior_nb)
```

For counts, examine whether the prior predicts implausibly enormous
event counts or concentrates almost entirely at zero. For binomial
outcomes, examine the prior distribution of success proportions.

## 17. Fit the negative-binomial model

``` r

fit_nb <- bayes_fit(
  des_nb,
  engine = "brms",
  prior = pr,
  compute = ctrl
)

fit_nb
```

## 18. MCMC diagnostics

``` r

bayes_diagnose(fit_nb)
```

The same computational standards apply as in Gaussian multilevel models:

- R-hat close to one;
- adequate bulk and tail ESS;
- acceptable Monte Carlo error;
- no unresolved divergences;
- no systematic maximum-tree-depth problems;
- stable trace behavior.

Good convergence does not imply that the likelihood is scientifically
appropriate.

## 19. Posterior predictive checking for counts

``` r

bayes_pp_check(fit_nb)
```

Useful count checks include:

- proportion of zeros;
- mean and variance;
- upper-tail counts;
- treatment-specific distributions;
- block-level heterogeneity;
- replicated maximum count.

A Poisson model that systematically underpredicts the variance can have
perfectly good HMC diagnostics while still being inadequate.

## 20. Compare Poisson and negative binomial predictively

``` r

fit_p <- bayes_fit(des_p, engine = "brms", prior = pr, compute = ctrl)

bayes_compare_models(
  poisson = fit_p,
  negative_binomial = fit_nb
)
```

PSIS-LOO compares out-of-sample predictive performance. It does not test
a point-null treatment effect and should not be labeled a Bayes factor.

## 21. Treatment effects on the response scale

For counts, population-level posterior predictions can be converted into
expected counts or rates for each treatment. The package can then
support scientific comparisons on an interpretable scale.

``` r

ndc <- data.frame(
  treatment = factor(levels(dc$treatment), levels = levels(dc$treatment))
)

pred_nb <- bayes_predict(
  fit_nb,
  newdata = ndc,
  type = "mean",
  re_formula = NA
)

cbind(ndc, pred_nb$summary)
```

If an exposure offset is present, the prediction grid must include the
exposure value for which the expected count is being reported.

## 22. Probability of meaningful effects

On a response scale, a meaningful difference can be defined in counts,
incidence probability, relative risk, rate ratio, or another
scientifically justified metric.

Version 1.0.0 does not silently choose one metric. When the desired
metric is not available as a direct wrapper, use posterior prediction
draws to compute it explicitly and document the definition.

This is preferable to applying a generic ROPE to link-scale coefficients
whose practical meaning has not been established.

## 23. Gamma models

Gamma models are useful for positive continuous responses whose
variability increases with the mean and whose distribution is
right-skewed.

Examples may include positive time-to-event summaries, strictly positive
concentrations, or biomass measures under an appropriate
parameterization.

``` r

dg <- transform(
  bayes_teaching_data("heterogeneous"),
  positive_yield = pmax(yield, 0.01)
)

des_g <- bayes_glmm(
  dg,
  response = "positive_yield",
  fixed = ~ treatment,
  group = "block",
  family = "gamma"
)
```

The example is a syntax demonstration, not evidence that Gamma is the
correct likelihood for that teaching dataset.

## 24. Lognormal models

A lognormal model assumes that the logarithm of a positive continuous
response is Gaussian under the modeled predictor structure. This differs
from fitting a Gaussian model to `log(y)` and then interpreting
coefficients naively on the original scale, because posterior response
predictions can be generated directly with the model’s distribution.

``` r

des_ln <- bayes_glmm(
  dg,
  response = "positive_yield",
  fixed = ~ treatment,
  group = "block",
  family = "lognormal"
)
```

## 25. Beta models

Beta regression is intended for continuous proportions strictly between
0 and 1. Exact zeros and ones require a different model, transformation,
or zero/one-inflated extension.

``` r

dbeta <- data.frame(
  block = factor(rep(1:6, each = 4)),
  treatment = factor(rep(LETTERS[1:4], 6)),
  proportion = seq(0.05, 0.95, length.out = 24)
)

des_beta <- bayes_glmm(
  dbeta,
  response = "proportion",
  fixed = ~ treatment,
  group = "block",
  family = "beta"
)
```

The package validates that Beta responses lie strictly inside the unit
interval.

## 26. Random slopes in GLMMs

The same scientific rule used for Gaussian mixed models applies: a
random slope is meaningful when the predictor varies within a grouping
factor and its effect is expected to vary among groups.

``` r

bayes_glmm(
  dc,
  response = "insects",
  fixed = ~ treatment,
  group = "block",
  family = "negbinomial",
  random_slopes = list(block = "exposure")
)
```

The syntax example does not imply that an exposure slope is
scientifically appropriate for the teaching data. Random slopes must
follow the design and scientific mechanism.

## 27. Nested and crossed grouping factors

Non-Gaussian responses can occur in the same hierarchical structures as
Gaussian responses. For example, plants may be nested in plots and plots
in blocks, while years or genotypes can be crossed with locations.

Version 1.0.0 stores nested and crossed grouping information in the same
design schema used by
[`bayes_mixed()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_mixed.md)
and compiles supported structures through `brms`.

Complex hierarchy does not justify ignoring the response distribution,
and a non-Gaussian likelihood does not justify ignoring the hierarchy.

## 28. Variance components and random effects

For GLMMs, group-level SDs are expressed on the model’s latent/link
scale. They should be interpreted accordingly.

``` r

bayes_variance_components(fit_nb)
bayes_random_effects(fit_nb, group = "block")
```

Do not compare a link-scale random-effect SD numerically with a
response-scale standard deviation as though they were the same quantity.

## 29. Bayes factors for GLMMs

The package does not generate automatic `BayesFactor`-package BFs for
GLMMs. For a specific confirmatory hypothesis, the advanced `brms` path
can compare properly specified models using bridge sampling when the
required priors and saved parameters make the comparison valid.

This restriction follows an important general principle: a Bayes factor
is a relative comparison between fully specified probabilistic models,
not an omnibus score that can be attached automatically to every
coefficient (Tendeiro et al. 2025; Kruschke 2021).

## 30. Frequentist teaching crosswalk

``` r

bayes_compare_frequentist(fit_nb)
```

When `lme4` is available, the package can construct a pedagogical GLMM
comparison for several supported families. This is not used to decide
which Bayesian result is correct, and not every Bayesian distributional
structure has an exact frequentist counterpart in the crosswalk.

## 31. Common mistakes

Avoid:

1.  applying Gaussian ANOVA automatically to count or binary data;
2.  using a transformation only because it was conventional;
3.  fitting Poisson counts without checking overdispersion or tails;
4.  ignoring the number of binomial trials;
5.  comparing raw counts collected under unequal exposure;
6.  interpreting logit coefficients as probability differences;
7.  applying a generic link-scale ROPE without scientific calibration;
8.  selecting a family only by the smallest information criterion;
9.  interpreting treatment effects before posterior predictive checking;
10. confusing predictive model comparison with point-null evidence;
11. ignoring the experimental grouping structure because the likelihood
    is non-Gaussian.

## 32. Reporting checklist

Report at least:

- response definition and units;
- likelihood family and link;
- rationale for the family;
- binomial trials or count exposure where applicable;
- fixed-effects specification;
- random-effects hierarchy;
- priors on the relevant link and auxiliary-parameter scales;
- prior predictive checks;
- sampler settings and convergence diagnostics;
- posterior predictive checks targeted to the response type;
- posterior effects on an interpretable scale;
- uncertainty intervals;
- practical-effect definition;
- model-comparison method and its purpose;
- software and versions.

The BARG framework emphasizes that the likelihood, priors, computation,
posterior checks, decisions, sensitivity, and reproducibility all need
to be made visible (Kruschke 2021).

## 33. Complete count workflow

``` r

library(bayesAgriFlow)

d <- bayes_teaching_data("glmm_count")

des <- bayes_glmm(
  d,
  response = "insects",
  fixed = ~ treatment,
  group = "block",
  family = "negbinomial"
)

bayes_design_audit(des)
bayes_plan(des)

ctrl <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  seed = 20260824
)

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = bayes_prior("regularizing"),
  compute = ctrl
)

bayes_diagnose(fit)
bayes_pp_check(fit)
bayes_summary(fit)
bayes_variance_components(fit)

nd <- data.frame(
  treatment = factor(levels(d$treatment), levels = levels(d$treatment))
)

bayes_predict(fit, newdata = nd, type = "mean", re_formula = NA)
```

## 34. Complete binomial workflow

``` r

d <- bayes_teaching_data("glmm_binomial")

des <- bayes_glmm(
  d,
  response = "infected",
  fixed = ~ treatment,
  group = "block",
  family = "binomial",
  trials = "plants"
)

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = bayes_prior("regularizing"),
  compute = ctrl
)

bayes_diagnose(fit)
bayes_pp_check(fit)
```

## 35. Scope of version 1.0.0

Version 1.0.0 establishes the common GLMM grammar for Bernoulli,
binomial, Poisson, negative-binomial, Gamma, lognormal, and Beta
responses with hierarchical grouping factors and optional count
exposure.

Future extensions can add zero-inflated and hurdle models, ordinal
responses, mixtures, distributional regression beyond heterogeneous
Gaussian sigma, non-Gaussian repeated measures, and multivariate
response families while preserving the same separation among design,
likelihood, priors, computation, posterior interpretation, and evidence.

Bolker, Benjamin M., Mollie E. Brooks, Connie J. Clark, et al. 2009.
“Generalized Linear Mixed Models: A Practical Guide for Ecology and
Evolution.” *Trends in Ecology & Evolution* 24 (3): 127–35.
<https://doi.org/10.1016/j.tree.2008.10.008>.

Kruschke, John K. 2021. “Bayesian Analysis Reporting Guidelines.”
*Nature Human Behaviour* 5: 1282–91.
<https://doi.org/10.1038/s41562-021-01177-7>.

Tendeiro, Jorge N., Rink Hoekstra, Tsz Keung Wong, and Henk A. L. Kiers.
2025. “Introduction to the Bayes Factor: A Shiny/r App.” *Teaching
Statistics* 47 (1): 5–16. <https://doi.org/10.1111/test.12380>.
