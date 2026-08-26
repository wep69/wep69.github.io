# Compare brms-backed models by PSIS-LOO

Compare brms-backed models by PSIS-LOO

## Usage

``` r
bayes_compare_models(...)
```

## Arguments

- ...:

  Two or more \`bayes_fit\` objects.

## Value

A \`loo_compare\` table.

## Examples

``` r
# Example 1: compare two locally fitted models
if (FALSE) bayes_compare_models(fit_random_intercept, fit_random_slope)
# Example 2: compare Gaussian and Student-t models
if (FALSE) bayes_compare_models(fit_gaussian, fit_student)
# Example 3: assign model names before comparison
if (FALSE) {
  x <- list(simple = fit1, complex = fit2)
  do.call(bayes_compare_models, x)
}
```
