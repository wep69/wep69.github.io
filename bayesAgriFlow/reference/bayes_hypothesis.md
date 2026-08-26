# Evaluate a directional posterior hypothesis for a model parameter

Evaluate a directional posterior hypothesis for a model parameter

## Usage

``` r
bayes_hypothesis(fit, parameter, value = 0,
                             direction = c("greater", "less"))
```

## Arguments

- fit:

  A \`bayes_fit\`.

- parameter:

  Exact posterior parameter name.

- value:

  Reference value.

- direction:

  \`"greater"\` or \`"less"\`.

## Value

A one-row data frame.

## Examples

``` r
# Example 1: inspect names first
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 500)
names1 <- bayes_summary(f)$parameter
names1
#> [1] "(Intercept)"      "treatmentBioB"    "treatmentBioC"    "treatmentControl"
#> [5] "sigma"           

# Example 2: probability that a treatment coefficient is positive
p <- setdiff(names1, c("(Intercept)", "sigma"))[1]
bayes_hypothesis(f, p, value = 0, direction = "greater")
#>       parameter reference direction posterior_probability
#> 1 treatmentBioB         0   greater                  0.99

# Example 3: probability that the same coefficient is below 0.5
bayes_hypothesis(f, p, value = 0.5, direction = "less")
#>       parameter reference direction posterior_probability
#> 1 treatmentBioB       0.5      less                 0.656
```
