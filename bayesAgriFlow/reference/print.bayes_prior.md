# Print a bayes_prior object

Compact console display for \`bayes_prior\` objects: prior family, scale
policy, and target parameters.

## Usage

``` r
# S3 method for class 'bayes_prior'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_prior\`: prior specification.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
pr <- bayes_prior("regularizing")
pr
#> <bayes_prior>
#>   Strategy: regularizing
#>   Coefficient scale: 2.5
#>   Intercept scale: 5
#>   Residual-variance prior: Inv-Gamma(shape=2, scale=1)
```
