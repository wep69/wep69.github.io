# Create an auditable analysis plan

Create an auditable analysis plan

## Usage

``` r
bayes_plan(design, engine = c("auto", "native", "bayesfactor", "brms",
           "pymc"), goal = c("estimation", "prediction"))
```

## Arguments

- design:

  A \`bayes_design\`.

- engine:

  Requested engine: \`"auto"\`, \`"native"\`, \`"bayesfactor"\`,
  \`"brms"\`, or \`"pymc"\`.

- goal:

  Character vector containing any of \`"estimation"\`, \`"evidence"\`,
  \`"prediction"\`, and \`"decision"\`.

## Value

A \`bayes_plan\` object.

## Examples

``` r
# Example 1: default CRD plan
bayes_plan(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"))
#> <bayes_plan>
#>   Design: Completely randomized design
#>   Audit: PASS
#>   Engine: native
#>   Reason: A Gaussian fixed-design teaching model can use the dependency-light conjugate engine.
#>   Goals: estimation, prediction

# Example 2: repeated measures route to brms
bayes_plan(bayes_repeated(
  bayes_teaching_data("repeated"), "height", "plot_id", "time", "treatment"))
#> <bayes_plan>
#>   Design: Repeated-measures mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction

# Example 3: non-Gaussian GLMM route
bayes_plan(bayes_glmm(
  bayes_teaching_data("glmm_count"), "insects", ~ treatment, "block",
  family = "negbinomial"))
#> <bayes_plan>
#>   Design: Generalized linear mixed model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
