# Print a bayes_plan object

Compact console display for \`bayes_plan\` objects: chosen engine, goal,
capability status, and reasons for routing decisions.

## Usage

``` r
# S3 method for class 'bayes_plan'
print(x, ...)
```

## Arguments

- x:

  An object of class \`bayes_plan\`: auditable analysis plan.

- ...:

  Additional arguments passed to or from other methods.

## Value

The object \`x\`, invisibly.

## Examples

``` r
p <- bayes_plan(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"))
p
#> <bayes_plan>
#>   Design: Completely randomized design
#>   Audit: PASS
#>   Engine: native
#>   Reason: A Gaussian fixed-design teaching model can use the dependency-light conjugate engine.
#>   Goals: estimation, prediction
```
