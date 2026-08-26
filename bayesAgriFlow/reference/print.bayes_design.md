# Print a bayes_design object

Compact console display for \`bayes_design\` objects: design type,
response family, factor roles, grouping structure, and routed engine.

## Usage

``` r
# S3 method for class 'bayes_design'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_design\`: experimental design
  specification.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
d1 <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
d1
#> <bayes_design>
#>   Type: Completely randomized design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 32
#>   treatment: treatment
```
