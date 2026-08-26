# Print a bayes_fit object

Compact console display for \`bayes_fit\` objects: engine, design type,
posterior dimensions, and pointers to extractor functions.

## Usage

``` r
# S3 method for class 'bayes_fit'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_fit\`: fitted Bayesian model.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
des <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
fit <- bayes_fit(des, engine = "native", draws = 500)
fit
#> <bayes_fit>
#>   Design: Completely randomized design
#>   Family: Gaussian
#>   Engine: native
#>   Formula: yield ~ treatment
#>   Posterior draws: 500
```
