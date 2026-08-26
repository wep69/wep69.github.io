# Assess prior-scale sensitivity for the native engine

Assess prior-scale sensitivity for the native engine

## Usage

``` r
bayes_prior_sensitivity(design, coefficient_scales = c(1, 2.5, 5),
                                    draws = 1000, seed = 20260824)
```

## Arguments

- design:

  A \`bayes_design\` supported by the native engine.

- coefficient_scales:

  Numeric vector of prior scales.

- draws:

  Posterior draws per fit.

- seed:

  Random seed.

## Value

A data frame of posterior coefficient summaries across prior scales.

## Examples

``` r
# Example 1: treatment experiment
des <- bayes_crd(bayes_teaching_data("crd"), "yield", "treatment")
bayes_prior_sensitivity(des, c(1, 2.5, 5), draws = 300)
#>           parameter       mean     median         sd        lower      upper
#> 1       (Intercept)  6.3311456  6.3260872 0.12915349  6.065112616 6.57365279
#> 2     treatmentBioB  0.3072037  0.3011862 0.17604461  0.005022043 0.69795775
#> 3     treatmentBioC  0.5678774  0.5753021 0.17970428  0.204518554 0.88386527
#> 4  treatmentControl -0.2506647 -0.2464401 0.17567587 -0.589378508 0.09670404
#> 5             sigma  0.4170884  0.4142343 0.05035960  0.332950345 0.53560730
#> 6       (Intercept)  6.2624188  6.2590057 0.15115858  5.967296779 6.56429697
#> 7     treatmentBioB  0.4087899  0.4034741 0.20525239  0.038034548 0.80581708
#> 8     treatmentBioC  0.6945252  0.6917872 0.19938112  0.311717066 1.09217958
#> 9  treatmentControl -0.2059910 -0.1974497 0.20004429 -0.609511606 0.15984082
#> 10            sigma  0.4000146  0.3926739 0.04845017  0.317322260 0.50962152
#> 11      (Intercept)  6.2455936  6.2405285 0.13498089  5.960866712 6.53025172
#> 12    treatmentBioB  0.4378118  0.4364678 0.19073932  0.077481554 0.79498612
#> 13    treatmentBioC  0.7129365  0.7022443 0.19090101  0.321200978 1.11095239
#> 14 treatmentControl -0.2012516 -0.1886824 0.19449899 -0.569395105 0.18915184
#> 15            sigma  0.3890672  0.3841577 0.04633115  0.312758750 0.48470270
#>    p_positive p_negative        pd coefficient_scale
#> 1   1.0000000 0.00000000 1.0000000               1.0
#> 2   0.9766667 0.02333333 0.9766667               1.0
#> 3   1.0000000 0.00000000 1.0000000               1.0
#> 4   0.0700000 0.93000000 0.9300000               1.0
#> 5   1.0000000 0.00000000 1.0000000               1.0
#> 6   1.0000000 0.00000000 1.0000000               2.5
#> 7   0.9833333 0.01666667 0.9833333               2.5
#> 8   1.0000000 0.00000000 1.0000000               2.5
#> 9   0.1533333 0.84666667 0.8466667               2.5
#> 10  1.0000000 0.00000000 1.0000000               2.5
#> 11  1.0000000 0.00000000 1.0000000               5.0
#> 12  0.9900000 0.01000000 0.9900000               5.0
#> 13  1.0000000 0.00000000 1.0000000               5.0
#> 14  0.1366667 0.86333333 0.8633333               5.0
#> 15  1.0000000 0.00000000 1.0000000               5.0

# Example 2: blocked experiment
des2 <- bayes_rcbd(bayes_teaching_data("rcbd"), "yield", "cultivar", "block")
head(bayes_prior_sensitivity(des2, c(1, 2.5), draws = 200))
#>     parameter        mean      median        sd       lower     upper
#> 1 (Intercept)  7.39534029  7.39187150 0.1836677  7.08137221 7.7438227
#> 2  cultivarC2  0.16439851  0.16801190 0.1964738 -0.18896042 0.5072752
#> 3  cultivarC3  0.48867491  0.49365716 0.2004680  0.09501362 0.8411213
#> 4  cultivarC4  0.50818853  0.51443175 0.1997395  0.08923511 0.8801155
#> 5  cultivarC5  0.15270075  0.16196615 0.1958594 -0.22818198 0.5032111
#> 6     blockB2 -0.07476103 -0.07423238 0.1906406 -0.51360033 0.2675085
#>   p_positive p_negative    pd coefficient_scale
#> 1      1.000      0.000 1.000                 1
#> 2      0.810      0.190 0.810                 1
#> 3      0.980      0.020 0.980                 1
#> 4      0.985      0.015 0.985                 1
#> 5      0.785      0.215 0.785                 1
#> 6      0.350      0.650 0.650                 1

# Example 3: factorial experiment
des3 <- bayes_factorial(bayes_teaching_data("factorial"), "yield",
                        c("cultivar", "fertilizer"), "block")
head(bayes_prior_sensitivity(des3, c(1, 3), draws = 200))
#>      parameter       mean     median        sd       lower     upper p_positive
#> 1  (Intercept) 6.90829818 6.90718681 0.1762123  6.58725490 7.2957458      1.000
#> 2    cultivarB 0.41991491 0.41633527 0.1751880  0.08651595 0.7507923      0.990
#> 3 fertilizerF1 0.35864914 0.36071405 0.1934197  0.01012905 0.6975924      0.975
#> 4 fertilizerF2 0.59030119 0.60826600 0.1944201  0.19924177 0.9126160      1.000
#> 5      blockB2 0.01328867 0.01423730 0.1791557 -0.29776231 0.3456733      0.545
#> 6      blockB3 0.07170991 0.08724017 0.1763699 -0.31541594 0.4183365      0.680
#>   p_negative    pd coefficient_scale
#> 1      0.000 1.000                 1
#> 2      0.010 0.990                 1
#> 3      0.025 0.975                 1
#> 4      0.000 1.000                 1
#> 5      0.455 0.545                 1
#> 6      0.320 0.680                 1
```
