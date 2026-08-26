# Bayesian Mixed Models: Random Slopes, Nested Structures, and Crossed Effects

## 1. Why mixed models belong in an experimental workflow

Many agricultural experiments contain more than one source of variation
that cannot be represented adequately by a single residual term. Blocks,
plots, locations, experimental units measured under several conditions,
batches, and other grouping structures can induce dependence among
observations.

A mixed model represents this dependence through group-level effects. In
a Bayesian analysis, these effects receive probability distributions and
are estimated jointly with the fixed effects, residual variation, and
any correlation parameters.

The most important practical rule is not to begin by deciding whether a
term should be written inside parentheses. Begin by identifying the
experimental unit and the grouping mechanism that generated dependence.

This design-first perspective is consistent with the general treatment
of multilevel models in Gelman and Hill (2007) and grouped-data mixed
models in Pinheiro and Bates (2000).

## 2. Learning objectives

After completing this vignette, the reader should be able to:

1.  distinguish population-level and group-level effects;
2.  explain partial pooling in scientific terms;
3.  identify random intercepts and random slopes from the experimental
    design;
4.  distinguish nested and crossed grouping structures;
5.  construct a
    [`bayes_mixed()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_mixed.md)
    design object;
6.  audit grouping factors before sampling;
7.  inspect the compiled model plan;
8.  configure `cmdstanr` and CPU use;
9.  specify and justify priors for fixed effects and variance
    components;
10. interpret variance components without treating them as nuisance
    output;
11. diagnose MCMC before interpreting treatment effects;
12. obtain response-scale predictions;
13. compare scientifically motivated mixed models with PSIS-LOO;
14. use a frequentist mixed-model fit only as a pedagogical crosswalk;
15. report the model in a way that makes the hierarchy reproducible.

## 3. A teaching example with random slopes

The bundled dataset contains eight blocks observed at five nitrogen
levels. The simulation includes a block-specific intercept and a
block-specific slope.

``` r

d <- bayes_teaching_data("mixed")
head(d)
#>   block nitrogen     yield
#> 1    B1        0  5.552557
#> 2    B1       50  7.259461
#> 3    B1      100  7.848984
#> 4    B1      150  8.918742
#> 5    B1      200 10.406367
#> 6    B2        0  6.287808
with(d, table(block, nitrogen))
#>      nitrogen
#> block 0 50 100 150 200
#>    B1 1  1   1   1   1
#>    B2 1  1   1   1   1
#>    B3 1  1   1   1   1
#>    B4 1  1   1   1   1
#>    B5 1  1   1   1   1
#>    B6 1  1   1   1   1
#>    B7 1  1   1   1   1
#>    B8 1  1   1   1   1
```

The data are simulated and should not be interpreted as field evidence.

A useful graph during local analysis is a line plot of yield against
nitrogen, with one line per block. The scientific question is whether
the nitrogen response is similar across blocks and how much uncertainty
remains in the population-average slope.

## 4. Start with the experimental units

The block is a grouping factor. Measurements from the same block may
resemble one another because they share soil, microclimate, management
history, or other block-level conditions.

If blocks differ only in their baseline yield, a random-intercept model
may be sufficient:

``` math
y_{ij} = \beta_0 + \beta_1 x_{ij} + u_{0j} + \varepsilon_{ij}.
```

Here,

``` math
u_{0j} \sim N(0, \sigma_{u0}^2).
```

If blocks can also differ in their response to nitrogen, a random slope
is added:

``` math
y_{ij} = \beta_0 + \beta_1 x_{ij} + u_{0j} + u_{1j}x_{ij} + \varepsilon_{ij}.
```

The pair $`(u_{0j}, u_{1j})`$ is usually modeled jointly, which allows a
correlation between block-specific intercepts and slopes.

## 5. Random slopes are scientific structure, not automatic complexity

A random slope should be considered when the predictor varies within the
same grouping factor and there is a scientific reason to expect the
effect to vary among groups.

For the teaching data, nitrogen varies within block, so the model can
estimate a block-specific nitrogen slope.

``` r

des <- bayes_mixed(
  data = d,
  response = "yield",
  fixed = ~ nitrogen,
  group = "block",
  random_slopes = list(block = "nitrogen")
)

des
#> <bayes_design>
#>   Type: General mixed-effects model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 40
#>   fixed: nitrogen
#>   group: block
#>   random_slopes: block:[nitrogen]
```

The named-list syntax is deliberate. It makes the grouping factor
associated with each slope explicit.

## 6. Audit before fitting

``` r

aud <- bayes_design_audit(des)
aud
#> <bayes_design_audit>
#>   Design: General mixed-effects model
#>   Status: PASS
aud$checks$group_levels
#> block 
#>     8
aud$checks$group_observations
#> $block
#> 
#> B1 B2 B3 B4 B5 B6 B7 B8 
#>  5  5  5  5  5  5  5  5
```

The audit cannot determine whether the scientific model is correct. It
can, however, detect structural problems such as missing roles, grouping
factors with only one level, and incomplete data that would make the
intended model impossible to fit as specified.

## 7. Inspect the computational plan

``` r

bayes_plan(des)
#> <bayes_plan>
#>   Design: General mixed-effects model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
bayes_capabilities("mixed")
#>   design      engine             status estimation evidence hierarchical
#> 1  mixed      native      not_supported      FALSE    FALSE        FALSE
#> 2  mixed bayesfactor      not_supported      FALSE    FALSE        FALSE
#> 3  mixed        brms supported_optional       TRUE     TRUE         TRUE
#> 4  mixed        pymc      not_supported      FALSE    FALSE        FALSE
#>   non_gaussian flexible_response
#> 1        FALSE             FALSE
#> 2        FALSE             FALSE
#> 3        FALSE             FALSE
#> 4        FALSE             FALSE
#>                                                                         notes
#> 1                        The native engine does not represent this structure.
#> 2                           No validated BayesFactor adapter for this design.
#> 3                   Stan posterior model through brms with cmdstanr or rstan.
#> 4 PyMC is not a general replacement backend for this design in version 1.0.0.
```

Version 1.0.0 routes this model to `brms` and Stan. The native conjugate
engine is intentionally not used because a random-slope covariance
structure is not part of that teaching engine.

## 8. The compiled model

For this design, the conceptual `brms` formula is

``` text
yield ~ nitrogen + (1 + nitrogen | block)
```

The population-level coefficient for nitrogen describes the average
slope across the modeled population of blocks. The block-specific
deviations describe how individual blocks differ from that average.

The group-level SD for the intercept and the group-level SD for the
slope are scientifically different quantities. They should not be
collapsed into a single statement such as “block variability was
present.”

## 9. Partial pooling

A multilevel model does not estimate each block completely independently
and does not force every block to have the same intercept and slope.

Instead, block-specific estimates are partially pooled toward the
population distribution. Groups with less information usually show more
shrinkage than groups with more information.

This behavior is particularly useful in experiments with unbalanced
group sizes. It is also one reason why mixed models should not be
described merely as classical ANOVA with a Bayesian sampler.

## 10. Priors for mixed models

The prior structure should address at least:

- population-level coefficients;
- the intercept;
- group-level SDs;
- residual SD for Gaussian models;
- correlations among random effects when estimated.

``` r

pr <- bayes_prior("regularizing")
pr
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
```

The package-generated priors are intended as transparent starting
points. A confirmatory analysis should replace them when agronomic
information can be translated into meaningful prior distributions.

For example, a prior for a nitrogen slope can be calibrated in yield
units per kg of nitrogen rather than selected only because a default
distribution is convenient.

## 11. Prior predictive reasoning

Before fitting, ask what data the priors imply.

For a random-slope model, useful prior-predictive questions include:

- Can the prior generate negative yields when those are impossible?
- Can it generate slopes far larger than any agronomically plausible
  response?
- Does the prior allow implausibly large differences among blocks?
- Does the group-level SD prior concentrate too strongly near zero?

The fixed-design
[`bayes_prior_predictive()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_prior_predictive.md)
simulator does not pretend to represent a full random-slope model. For
the exact hierarchical prior predictive distribution, use the local
`brms` path and sample from the prior model itself.

## 12. CmdStan configuration

``` r

cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  threads_per_chain = 1,
  iter_warmup = 1000,
  iter_sampling = 1500,
  adapt_delta = 0.95,
  seed = 20260824
)
cmp
#> <bayes_compute>
#>   Backend: cmdstanr
#>   CPU budget: 8
#>   Chains: 4
#>   Parallel chains: 4
#>   Threads per chain: 1
#>   Maximum requested workers: 4
#>   Reproducibility: standard
```

Parallel chains are the conservative first use of multiple CPUs.
Within-chain threading should be activated only after a model has been
shown to benefit from it.

## 13. Fit locally

The following chunk is not evaluated during vignette construction
because it requires a local Stan installation.

``` r

fit <- bayes_fit(
  des,
  engine = "brms",
  prior = pr,
  compute = cmp
)
fit
```

## 14. Diagnostics are a gate

``` r

diag <- bayes_diagnose(fit)
diag
```

Review at least:

- R-hat;
- bulk ESS;
- tail ESS;
- Monte Carlo standard error;
- divergent transitions;
- maximum tree-depth hits;
- E-BFMI when available.

A random-slope model with a difficult covariance structure can reveal
sampling problems that are absent from a simpler random-intercept model.
The response to such problems is not to ignore the warnings. Reconsider
parameterization, priors, scaling, and whether the proposed structure is
supported by the design.

## 15. Variance components

After a satisfactory fit:

``` r

bayes_variance_components(fit)
```

For this example, important parameters include the SD of block
intercepts, the SD of block nitrogen slopes, their correlation, and the
residual SD.

A large slope SD means that nitrogen response differs substantially
among blocks. It does not automatically imply that the average nitrogen
slope is large.

## 16. Inspect group-specific posterior effects

``` r

bayes_random_effects(fit, "block")
```

Group-specific effects are posterior distributions. Avoid ranking blocks
based only on posterior means without displaying uncertainty.

## 17. Population-level effects

``` r

bayes_summary(fit)
bayes_effects(fit)
```

The nitrogen coefficient is on the response scale because this is a
Gaussian identity-link model.

The posterior direction probability answers whether the coefficient is
probably positive or negative. It does not answer whether the magnitude
is agronomically important.

For practical importance, define a scientifically meaningful slope
threshold or evaluate predicted yield differences across an actual
nitrogen interval.

## 18. Response-scale prediction

[`bayes_predict()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_predict.md)
provides a common prediction interface.

``` r

newd <- data.frame(nitrogen = c(0, 50, 100, 150, 200))
pred <- bayes_predict(fit, newdata = newd, type = "mean")
pred$summary
```

By default, `re_formula = NA` produces population-level predictions for
a `brms` model. Group-specific predictions should be requested only when
the scientific question concerns specific observed groups.

## 19. Random intercept versus random slope

A common modeling sequence is to compare:

``` r

des_i <- bayes_mixed(d, "yield", ~ nitrogen, group = "block")
des_s <- bayes_mixed(d, "yield", ~ nitrogen, group = "block",
                     random_slopes = list(block = "nitrogen"))

fit_i <- bayes_fit(des_i, engine = "brms", compute = cmp)
fit_s <- bayes_fit(des_s, engine = "brms", compute = cmp)
```

Model comparison should be tied to a scientific question. The question
is not simply which model has the smallest information criterion. It is
whether allowing the nitrogen effect to vary among blocks improves
predictive adequacy enough to justify the additional hierarchy.

## 20. PSIS-LOO

``` r

bayes_loo(fit_i)
bayes_loo(fit_s)
bayes_compare_models(intercept = fit_i, slope = fit_s)
```

Inspect Pareto-k diagnostics rather than reading only an ELPD
difference. Model comparison is not a substitute for checking the
posterior or for understanding the experimental design.

## 21. Bayes factors for mixed models

A Bayes factor can be computed for carefully specified `brms` model
comparisons when priors are proper and the bridge-sampling calculation
is numerically stable.

``` r

bayes_bf(fit_s, fit_i)
```

This is not automatically performed by
[`bayes_mixed()`](https://wep69.github.io/bayesAgriFlow/reference/bayes_mixed.md).
The package does not create a model-comparison hypothesis merely because
two nested formulas can be written.

## 22. Nested grouping structures

Suppose sub-blocks are nested within sites. A generic design can express
that structure explicitly:

``` r

des_nested <- bayes_mixed(
  data = field_data,
  response = "yield",
  fixed = ~ treatment,
  nested = list(c("site", "block"))
)
```

The hierarchical term is conceptually

``` text
(1 | site/block)
```

which expands into site-level and block-within-site effects.

The names should describe real experimental units. Do not manufacture a
nested structure simply because the data frame contains hierarchical
labels.

## 23. Crossed grouping structures

Crossed factors occur when levels of one grouping factor are observed
across levels of another rather than nested within them.

A common example that is now handled explicitly by the version 1.0.0 G
by E module is a set of observers evaluating several samples, where
samples and observers are crossed.

``` r

des_crossed <- bayes_mixed(
  data = rating_data,
  response = "score",
  fixed = ~ treatment,
  crossed = c("sample", "observer")
)
```

The compiler produces separate random intercepts for the crossed groups.

## 24. Nested and crossed are not interchangeable

If every block belongs to exactly one site, blocks are nested within
site. If every genotype occurs in several environments and each
environment contains several genotypes, genotype and environment are
crossed.

The difference is part of the design. It should not be inferred from a
model comparison after the data have been analyzed.

## 25. Heterogeneous residual variance

Version 1.0.0 can model residual SD as a function of a grouping factor
in Gaussian and Student-t models.

``` r

dh <- bayes_teaching_data("heterogeneous")
des_h <- bayes_mixed(
  dh,
  response = "yield",
  fixed = ~ treatment,
  group = "block",
  residual_group = "treatment"
)
des_h
#> <bayes_design>
#>   Type: General mixed-effects model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   fixed: treatment
#>   group: block
#>   residual_group: treatment
```

This produces a distributional `brms` model in which `sigma` is modeled
by `treatment`.

The scientific interpretation is that treatment groups can differ not
only in expected yield but also in residual variability.

## 26. Why residual heterogeneity matters

When one treatment has substantially more variable outcomes than
another, forcing a common residual SD can distort uncertainty and
predictions.

Heterogeneous variance can arise from biological instability,
multiplicative measurement processes, treatment-dependent dispersion, or
omitted structure. It should not be added automatically whenever a
residual plot looks imperfect.

## 27. Student-t mixed models

A Student-t likelihood can be useful when continuous observations show
heavier tails than a Gaussian likelihood can represent adequately.

``` r

des_t <- bayes_mixed(
  d,
  response = "yield",
  fixed = ~ nitrogen,
  group = "block",
  random_slopes = list(block = "nitrogen"),
  family = "student"
)
des_t
#> <bayes_design>
#>   Type: General mixed-effects model
#>   Response: yield
#>   Family: Student-t
#>   Observations: 40
#>   fixed: nitrogen
#>   group: block
#>   random_slopes: block:[nitrogen]
```

The Student-t family introduces a degrees-of-freedom parameter. It
should not be described as a generic cure for outliers. Posterior
predictive checking is still required.

## 28. Frequentist crosswalk

``` r

bayes_compare_frequentist(fit)
```

When `lme4` is installed, the crosswalk fits a corresponding linear
mixed model where possible.

The comparison is useful for teaching differences between:

- point estimates and posterior distributions;
- confidence intervals and credible intervals;
- variance-component estimation under different inferential frameworks;
- likelihood-based model comparison and posterior predictive comparison.

It is not used to decide whether the Bayesian analysis is correct.

## 29. Parameter recovery

Before relying on a new mixed-model configuration, simulation-based
recovery should be part of package validation.

A useful developer workflow is:

1.  generate data with known fixed effects;
2.  generate known group-level SDs;
3.  generate a known intercept-slope correlation;
4.  fit the model with frozen seeds;
5.  assess posterior recovery and credible-interval coverage;
6.  record divergence rates and ESS;
7.  repeat across several sample sizes and variance ratios.

This principle follows the Stan-oriented validation logic illustrated by
Annis et al. (2017).

## 30. Common mistakes

### 30.1 Treating every factor as random

A factor does not become a random effect merely because it has many
levels. The grouping structure and target population must justify the
model.

### 30.2 Random slope without within-group variation

A slope cannot be learned within a grouping factor if the predictor does
not vary within that group.

### 30.3 Ignoring the intercept-slope correlation

A difficult estimated correlation may be scientifically meaningful or
may signal weak information. It requires diagnosis, not automatic
deletion.

### 30.4 Selecting random effects only by a threshold

Do not retain or remove group-level terms only because a p-value, BF
label, or information-criterion difference crosses an arbitrary
boundary.

### 30.5 Reporting only fixed effects

If the scientific model is hierarchical, the report should explain the
hierarchy and summarize its important variance components.

## 31. Reporting template

A mixed-model report should state:

- experimental units and grouping factors;
- fixed-effect formula;
- random-intercept and random-slope structure;
- nesting or crossing relationships;
- likelihood and link;
- prior distributions for fixed and group-level parameters;
- sampler configuration;
- convergence and sampling diagnostics;
- posterior estimates with intervals;
- important variance components;
- posterior predictive checks;
- sensitivity analysis when conclusions depend on prior choices;
- model-comparison criterion when alternatives are compared.

This structure is consistent with the reporting priorities in Kruschke
(2021).

## 32. Reproducible starter workflow

``` r

library(bayesAgriFlow)

d <- bayes_teaching_data("mixed")

des <- bayes_mixed(
  d,
  response = "yield",
  fixed = ~ nitrogen,
  group = "block",
  random_slopes = list(block = "nitrogen")
)

bayes_design_audit(des)

pr <- bayes_prior("regularizing")
cmp <- bayes_compute(
  backend = "cmdstanr",
  n_cpus = 8,
  chains = 4,
  parallel_chains = 4,
  iter_warmup = 1000,
  iter_sampling = 1500,
  seed = 20260824
)

fit <- bayes_fit(des, engine = "brms", prior = pr, compute = cmp)

bayes_diagnose(fit)
bayes_summary(fit)
bayes_variance_components(fit)
bayes_pp_check(fit)
bayes_predict(fit)
bayes_compare_frequentist(fit)
```

## 33. Where the workflow goes next

Version 1.0.0 uses this hierarchical grammar directly in the G by E,
spatial, multivariate, measurement-error, censoring, ordinal, mixture,
and distributional modules while retaining the same design-first
interface. The same design-first structure now extends to spatial field
models in v18, while future work may add richer spatial covariance
structures, genotype-by-environment models, multivariate responses,
measurement error, and nonlinear hierarchical models without replacing
the public design-first API.

## References

Annis, Jeffrey, Brent J. Miller, and Thomas J. Palmeri. 2017. “Bayesian
Inference with Stan: A Tutorial on Adding Custom Distributions.”
*Behavior Research Methods* 49: 863–86.
<https://doi.org/10.3758/s13428-016-0746-9>.

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
