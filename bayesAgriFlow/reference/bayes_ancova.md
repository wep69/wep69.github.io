# Specify a Bayesian ANCOVA design

Specify a Bayesian ANCOVA design

## Usage

``` r
bayes_ancova(data, response, treatment, covariate, block = NULL,
                         interaction = FALSE)
```

## Arguments

- data:

  A data frame.

- response:

  Numeric response column.

- treatment:

  Qualitative treatment factor.

- covariate:

  Numeric covariate.

- block:

  Optional block factor.

- interaction:

  Include treatment-by-covariate interaction.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: common-slope ANCOVA
bayes_ancova(bayes_teaching_data("ancova"), "yield", "treatment", "initial_p")
#> <bayes_design>
#>   Type: Analysis of covariance
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 36
#>   treatment: treatment
#>   covariate: initial_p
# Example 2: treatment-specific slopes
bayes_ancova(bayes_teaching_data("ancova"), "yield", "treatment", "initial_p", interaction = TRUE)
#> <bayes_design>
#>   Type: Analysis of covariance
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 36
#>   treatment: treatment
#>   covariate: initial_p
# Example 3: posterior fit
bayes_fit(bayes_ancova(bayes_teaching_data("ancova"), "yield",
          "treatment", "initial_p"), draws = 500)
#> <bayes_fit>
#>   Design: Analysis of covariance
#>   Family: Gaussian
#>   Engine: native
#>   Formula: yield ~ treatment + initial_p
#>   Posterior draws: 500
```
