# Print a bayes_optimum object

Compact console display for \`bayes_optimum\` objects: biological
optimum estimate with credible interval and domain guardrails.

## Usage

``` r
# S3 method for class 'bayes_optimum'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_optimum\`: posterior optimum.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
dp <- bayes_polynomial(bayes_teaching_data("dose"), "yield", "nitrogen", degree = 2)
fp <- bayes_fit(dp, engine = "native", draws = 2000)
op <- bayes_optimum(fp, domain = range(dp$data$nitrogen))
op
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper response_median response_lower
#>             154         139.5         177.5         9.86624        9.56995
#>  response_upper p_lower_boundary p_upper_boundary p_interior factor_level
#>        10.15748                0            5e-04     0.9995         <NA>
```
