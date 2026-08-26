# Examine sensitivity to scientifically plausible ROPE definitions

Examine sensitivity to scientifically plausible ROPE definitions

## Usage

``` r
bayes_rope_sensitivity(fit, ranges, factor = NULL, parameter = NULL)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- ranges:

  A list of numeric length-two ROPE vectors.

- factor:

  Optional treatment factor.

- parameter:

  Optional posterior parameter.

## Value

A combined data frame.

## Examples

``` r
# Example 1: three ROPE widths for treatment contrasts
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 600)
bayes_rope_sensitivity(
  f,
  list(c(-0.10, 0.10), c(-0.20, 0.20), c(-0.30, 0.30)),
  factor = "treatment"
)
#>          contrast     median      lower       upper        pd    rope_pct
#> 1     BioA - BioB -0.4176358 -0.8067278 -0.02436034 0.9816667 0.060000000
#> 2     BioA - BioC -0.7047481 -1.0721598 -0.31371243 1.0000000 0.000000000
#> 3  BioA - Control  0.2125827 -0.1483080  0.58458453 0.8550000 0.223333333
#> 4     BioB - BioC -0.2882041 -0.6738532  0.09713441 0.9366667 0.140000000
#> 5  BioB - Control  0.6194209  0.2048038  1.01390118 0.9966667 0.006666667
#> 6  BioC - Control  0.9134202  0.4928132  1.27634095 1.0000000 0.000000000
#> 7     BioA - BioB -0.4176358 -0.8067278 -0.02436034 0.9816667 0.143333333
#> 8     BioA - BioC -0.7047481 -1.0721598 -0.31371243 1.0000000 0.008333333
#> 9  BioA - Control  0.2125827 -0.1483080  0.58458453 0.8550000 0.463333333
#> 10    BioB - BioC -0.2882041 -0.6738532  0.09713441 0.9366667 0.320000000
#> 11 BioB - Control  0.6194209  0.2048038  1.01390118 0.9966667 0.025000000
#> 12 BioC - Control  0.9134202  0.4928132  1.27634095 1.0000000 0.000000000
#> 13    BioA - BioB -0.4176358 -0.8067278 -0.02436034 0.9816667 0.251666667
#> 14    BioA - BioC -0.7047481 -1.0721598 -0.31371243 1.0000000 0.020000000
#> 15 BioA - Control  0.2125827 -0.1483080  0.58458453 0.8550000 0.681666667
#> 16    BioB - BioC -0.2882041 -0.6738532  0.09713441 0.9366667 0.535000000
#> 17 BioB - Control  0.6194209  0.2048038  1.01390118 0.9966667 0.055000000
#> 18 BioC - Control  0.9134202  0.4928132  1.27634095 1.0000000 0.003333333
#>    rope_label rope_lower rope_upper
#> 1      rope_1       -0.1        0.1
#> 2      rope_1       -0.1        0.1
#> 3      rope_1       -0.1        0.1
#> 4      rope_1       -0.1        0.1
#> 5      rope_1       -0.1        0.1
#> 6      rope_1       -0.1        0.1
#> 7      rope_2       -0.2        0.2
#> 8      rope_2       -0.2        0.2
#> 9      rope_2       -0.2        0.2
#> 10     rope_2       -0.2        0.2
#> 11     rope_2       -0.2        0.2
#> 12     rope_2       -0.2        0.2
#> 13     rope_3       -0.3        0.3
#> 14     rope_3       -0.3        0.3
#> 15     rope_3       -0.3        0.3
#> 16     rope_3       -0.3        0.3
#> 17     rope_3       -0.3        0.3
#> 18     rope_3       -0.3        0.3

# Example 2: two ROPE definitions
bayes_rope_sensitivity(
  f,
  list(narrow = c(-0.10, 0.10), wide = c(-0.25, 0.25)),
  factor = "treatment"
)
#>          contrast     median      lower       upper        pd    rope_pct
#> 1     BioA - BioB -0.4176358 -0.8067278 -0.02436034 0.9816667 0.060000000
#> 2     BioA - BioC -0.7047481 -1.0721598 -0.31371243 1.0000000 0.000000000
#> 3  BioA - Control  0.2125827 -0.1483080  0.58458453 0.8550000 0.223333333
#> 4     BioB - BioC -0.2882041 -0.6738532  0.09713441 0.9366667 0.140000000
#> 5  BioB - Control  0.6194209  0.2048038  1.01390118 0.9966667 0.006666667
#> 6  BioC - Control  0.9134202  0.4928132  1.27634095 1.0000000 0.000000000
#> 7     BioA - BioB -0.4176358 -0.8067278 -0.02436034 0.9816667 0.186666667
#> 8     BioA - BioC -0.7047481 -1.0721598 -0.31371243 1.0000000 0.015000000
#> 9  BioA - Control  0.2125827 -0.1483080  0.58458453 0.8550000 0.576666667
#> 10    BioB - BioC -0.2882041 -0.6738532  0.09713441 0.9366667 0.418333333
#> 11 BioB - Control  0.6194209  0.2048038  1.01390118 0.9966667 0.036666667
#> 12 BioC - Control  0.9134202  0.4928132  1.27634095 1.0000000 0.001666667
#>    rope_label rope_lower rope_upper
#> 1      narrow      -0.10       0.10
#> 2      narrow      -0.10       0.10
#> 3      narrow      -0.10       0.10
#> 4      narrow      -0.10       0.10
#> 5      narrow      -0.10       0.10
#> 6      narrow      -0.10       0.10
#> 7        wide      -0.25       0.25
#> 8        wide      -0.25       0.25
#> 9        wide      -0.25       0.25
#> 10       wide      -0.25       0.25
#> 11       wide      -0.25       0.25
#> 12       wide      -0.25       0.25

# Example 3: coefficient-level sensitivity
p <- setdiff(bayes_summary(f)$parameter, c("(Intercept)", "sigma"))[1]
bayes_rope_sensitivity(f, list(c(-0.1, 0.1), c(-0.2, 0.2)), parameter = p)
#>       parameter    median       q2.5     q97.5 p_below_rope p_in_rope
#> 1 treatmentBioB 0.4176358 0.02436034 0.8067278  0.001666667 0.0600000
#> 2 treatmentBioB 0.4176358 0.02436034 0.8067278  0.000000000 0.1433333
#>   p_above_rope rope_label rope_lower rope_upper
#> 1    0.9383333     rope_1       -0.1        0.1
#> 2    0.8566667     rope_2       -0.2        0.2
```
