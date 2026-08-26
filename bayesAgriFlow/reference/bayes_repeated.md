# Specify a repeated-measures Bayesian mixed model

Specify a repeated-measures Bayesian mixed model

## Usage

``` r
bayes_repeated(data, response, subject, time, treatment = NULL,
                           fixed = NULL, block = NULL, random_slope = TRUE,
                           residual_group = NULL,
                           correlation = c("none", "ar1"),
                           family = c("gaussian", "student"))
```

## Arguments

- data:

  A data frame.

- response:

  Response column.

- subject:

  Repeated experimental-unit identifier.

- time:

  Numeric time variable.

- treatment:

  Optional between- or within-unit treatment factor.

- fixed:

  Optional one-sided fixed-effects formula. When omitted, the wrapper
  uses \`time\` alone or \`treatment \* time\`.

- block:

  Optional block factor added as a random intercept.

- random_slope:

  Include a subject-specific time slope.

- residual_group:

  Optional factor defining heterogeneous residual SDs.

- correlation:

  \`"none"\` or \`"ar1"\`.

- family:

  \`"gaussian"\` or \`"student"\`. AR(1) is restricted to Gaussian
  models in version 0.3.0.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: random-intercept repeated-measures model
d <- bayes_teaching_data("repeated")
bayes_repeated(d, "height", "plot_id", "time", "treatment",
               random_slope = FALSE)
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

# Example 2: subject-specific time slopes
bayes_repeated(d, "height", "plot_id", "time", "treatment",
               random_slope = TRUE)
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

# Example 3: AR(1) residual dependence
bayes_repeated(d, "height", "plot_id", "time", "treatment",
               correlation = "ar1")
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
