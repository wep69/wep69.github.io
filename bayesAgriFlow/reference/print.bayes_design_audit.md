# Print a bayes_design_audit object

Compact console display for \`bayes_design_audit\` objects: audit status
and any detected randomization or structural issues.

## Usage

``` r
# S3 method for class 'bayes_design_audit'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_design_audit\`: design audit report.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
d2 <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
audit <- bayes_design_audit(d2)
audit
#> <bayes_design_audit>
#>   Design: Completely randomized design
#>   Status: PASS
```
