# Fit a Bayesian experimental model

Fit a Bayesian experimental model

## Usage

``` r
bayes_fit(design, engine = c("auto", "native", "bayesfactor", "brms",
          "pymc"), prior = bayes_prior(), compute = NULL,
          draws = 4000, seed = 20260824, sample_prior = FALSE, ...)
```

## Arguments

- design:

  A \`bayes_design\`.

- engine:

  \`"auto"\`, \`"native"\`, \`"bayesfactor"\`, \`"brms"\`, or
  \`"pymc"\`.

- prior:

  A \`bayes_prior\`.

- compute:

  A \`bayes_compute\` object for Stan-based fits; \`NULL\` uses package
  defaults.

- draws:

  Posterior draws for the native engine.

- seed:

  Random seed for the native engine.

- sample_prior:

  Prior-sampling mode passed to the brms path.

- ...:

  Additional arguments passed to \`brms::brm()\` when applicable.

## Value

A \`bayes_fit\`.

## Examples

``` r
# Example 1: CRD with the native teaching engine
des <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
fit <- bayes_fit(des, engine = "native", draws = 500)
bayes_summary(fit)
#>          parameter       mean     median         sd       lower     upper
#> 1      (Intercept)  6.2536592  6.2535141 0.13436956  5.98130205 6.5202029
#> 2    treatmentBioB  0.4167659  0.4144670 0.19996139  0.03486477 0.8185824
#> 3    treatmentBioC  0.6968681  0.7044202 0.18753142  0.32424700 1.0451695
#> 4 treatmentControl -0.2019843 -0.1898076 0.19876820 -0.57583244 0.1753878
#> 5            sigma  0.3996778  0.3972213 0.04846598  0.31645131 0.5122348
#>   p_positive p_negative    pd
#> 1      1.000      0.000 1.000
#> 2      0.990      0.010 0.990
#> 3      0.996      0.004 0.996
#> 4      0.142      0.858 0.858
#> 5      1.000      0.000 1.000

# Example 2: mixed model is routed to brms
des2 <- bayes_mixed(bayes_teaching_data("mixed"), "yield", ~ nitrogen,
                    group = "block", random_slopes = list(block = "nitrogen"))
bayes_plan(des2)
#> <bayes_plan>
#>   Design: General mixed-effects model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction

# Example 3: negative-binomial GLMM plan
des3 <- bayes_glmm(bayes_teaching_data("glmm_count"), "insects",
                   ~ treatment, "block", family = "negbinomial")
bayes_plan(des3)
#> <bayes_plan>
#>   Design: Generalized linear mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
