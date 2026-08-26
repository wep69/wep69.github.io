# Print a bayes_analysis object

Compact console display for \`bayes_analysis\` objects returned by
\`bayes_analyze()\`: engine, design type, estimation summary, and
available components.

## Usage

``` r
# S3 method for class 'bayes_analysis'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_analysis\`: integrated Bayesian workflow
  result.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
a <- bayes_analyze(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                draws = 500)
a
#> <bayes_analysis>
#>   Design: Completely randomized design
#>   Family: Gaussian
#>   Engine: native
#>   Design audit: PASS
#>   Diagnostic status: PASS
```
