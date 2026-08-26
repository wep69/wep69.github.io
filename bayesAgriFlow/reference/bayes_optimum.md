# Posterior optimum for a fitted quantitative response curve

Posterior optimum for a fitted quantitative response curve

## Usage

``` r
bayes_optimum(fit, domain, grid = 401L, factor_level = NULL,
                          level = 0.95)
```

## Arguments

- fit:

  A fitted polynomial or qualitative-by-quantitative \`bayes_fit\`.

- domain:

  Numeric length-two optimization domain.

- grid:

  Number of grid points.

- factor_level:

  Optional qualitative-factor level for a \`qual_quant\` model.

- level:

  Credible interval mass.

## Value

A \`bayes_optimum\` object with draw-level optima and summaries.

## Examples

``` r
# Example 1: quadratic biological optimum
f <- bayes_fit(bayes_polynomial(bayes_teaching_data("dose"), "yield", "nitrogen", 2), draws = 600)
bayes_optimum(f, domain = c(0, 200))
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper response_median response_lower
#>           152.5         139.5      178.0375         9.86717       9.547368
#>  response_upper p_lower_boundary p_upper_boundary p_interior factor_level
#>        10.15936                0      0.003333333  0.9966667         <NA>
# Example 2: a finer optimization grid
bayes_optimum(f, domain = c(0, 200), grid = 501)
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper response_median response_lower
#>           152.8         139.6        178.03        9.867168        9.54737
#>  response_upper p_lower_boundary p_upper_boundary p_interior factor_level
#>        10.15936                0      0.003333333  0.9966667         <NA>
# Example 3: 90 percent interval
bayes_optimum(f, domain = c(0, 200), level = 0.90)
#> <bayes_optimum>
#>  optimum_median optimum_lower optimum_upper response_median response_lower
#>           152.5       141.475           171         9.86717       9.599233
#>  response_upper p_lower_boundary p_upper_boundary p_interior factor_level
#>          10.103                0      0.003333333  0.9966667         <NA>
```
