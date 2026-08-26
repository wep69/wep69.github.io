# Convert a fit to a compact table

Convert a fit to a compact table

## Usage

``` r
bayes_table(fit, type = c("summary", "effects", "diagnostics", "evidence",
            "variance"), ...)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- type:

  \`"summary"\`, \`"effects"\`, \`"diagnostics"\`, \`"evidence"\`, or
  \`"variance"\`.

- ...:

  Passed to the selected function.

## Value

A data frame when the selected result is tabular.

## Examples

``` r
# Example 1: posterior summary table
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 500)
bayes_table(f, "summary")
#>          parameter       mean     median         sd       lower     upper
#> 1      (Intercept)  6.2536592  6.2535141 0.13436956  5.98130205 6.5202029
#> 2    treatmentBioB  0.4167659  0.4144670 0.19996139  0.03486477 0.8185824
#> 3    treatmentBioC  0.6968681  0.7044202 0.18753142  0.32424700 1.0451695
#> 4 treatmentControl -0.2019843 -0.1898076 0.19876820 -0.57583244 0.1753878
#> 5            sigma  0.3996778  0.3972213 0.04846598  0.31645131 0.5122348
#>   p_positive p_negative    pd
#> 1      1.000      0.000 1.000
#> 2      0.990      0.010 0.990
#> 3      0.996      0.004 0.996
#> 4      0.142      0.858 0.858
#> 5      1.000      0.000 1.000

# Example 2: effect-index table
bayes_table(f, "effects")
#>          parameter       mean     median         sd       lower     upper
#> 1      (Intercept)  6.2536592  6.2535141 0.13436956  5.98130205 6.5202029
#> 2    treatmentBioB  0.4167659  0.4144670 0.19996139  0.03486477 0.8185824
#> 3    treatmentBioC  0.6968681  0.7044202 0.18753142  0.32424700 1.0451695
#> 4 treatmentControl -0.2019843 -0.1898076 0.19876820 -0.57583244 0.1753878
#> 5            sigma  0.3996778  0.3972213 0.04846598  0.31645131 0.5122348
#>   p_positive p_negative    pd standardized_median
#> 1      1.000      0.000 1.000          11.6759260
#> 2      0.990      0.010 0.990           0.7738506
#> 3      0.996      0.004 0.996           1.3152218
#> 4      0.142      0.858 0.858          -0.3543895
#> 5      1.000      0.000 1.000                  NA
#>                                                                              effect_index_note
#> 1 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 2 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 3 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 4 Median divided by observed response SD; use scientific thresholds for substantive decisions.
#> 5                                                   Scale, dispersion, or dependence parameter

# Example 3: diagnostic table
bayes_table(f, "diagnostics")
#>          parameter mcse_independent
#> 1      (Intercept)      0.006009189
#> 2    treatmentBioB      0.008942545
#> 3    treatmentBioC      0.008386660
#> 4 treatmentControl      0.008889184
#> 5            sigma      0.002167465
```
