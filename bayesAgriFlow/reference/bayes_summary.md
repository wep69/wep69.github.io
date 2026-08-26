# Summarize a fitted Bayesian model

Summarize a fitted Bayesian model

## Usage

``` r
bayes_summary(fit, level = 0.95)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- level:

  Credible interval mass.

## Value

A data frame. For \`BayesFactor\` fits, returns a model-evidence table.

## Examples

``` r
# Example 1: CRD coefficients
f1 <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                draws = 500)
bayes_summary(f1)
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

# Example 2: RCBD coefficients
f2 <- bayes_fit(bayes_rcbd(bayes_teaching_data("rcbd"), "yield",
                "cultivar", "block"), draws = 500)
head(bayes_summary(f2))
#>     parameter       mean     median        sd        lower     upper p_positive
#> 1 (Intercept)  7.2363928  7.2299270 0.1887625  6.842764514 7.6221618      1.000
#> 2  cultivarC2  0.3889787  0.3937641 0.1993975 -0.000296443 0.7702519      0.974
#> 3  cultivarC3  0.8244276  0.8092957 0.2036435  0.424192943 1.2278945      1.000
#> 4  cultivarC4  0.7992985  0.8086839 0.1997582  0.420212962 1.1864164      1.000
#> 5  cultivarC5  0.4111486  0.4057163 0.2015959  0.031305303 0.8348273      0.984
#> 6     blockB2 -0.1700385 -0.1714666 0.1915398 -0.537141124 0.2293403      0.166
#>   p_negative    pd
#> 1      0.000 1.000
#> 2      0.026 0.974
#> 3      0.000 1.000
#> 4      0.000 1.000
#> 5      0.016 0.984
#> 6      0.834 0.834

# Example 3: custom credible mass
bayes_summary(f1, level = 0.90)
#>          parameter       mean     median         sd       lower     upper
#> 1      (Intercept)  6.2536592  6.2535141 0.13436956  6.02207131 6.4817519
#> 2    treatmentBioB  0.4167659  0.4144670 0.19996139  0.08545379 0.7357231
#> 3    treatmentBioC  0.6968681  0.7044202 0.18753142  0.38244765 0.9816465
#> 4 treatmentControl -0.2019843 -0.1898076 0.19876820 -0.53048800 0.1257552
#> 5            sigma  0.3996778  0.3972213 0.04846598  0.33185265 0.4859251
#>   p_positive p_negative    pd
#> 1      1.000      0.000 1.000
#> 2      0.990      0.010 0.990
#> 3      0.996      0.004 0.996
#> 4      0.142      0.858 0.858
#> 5      1.000      0.000 1.000
```
