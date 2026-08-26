# Pairwise posterior treatment contrasts

Contrasts are computed from posterior draws of standardized
population-level treatment means. In version 0.3.0 the population is the
observed covariate distribution. For multilevel \`brms\` fits, grouping
effects are omitted from the marginal mean (\`re_formula = NA\`).

## Usage

``` r
bayes_contrasts(fit, factor, rope = NULL, meaningful = NULL,
                            level = 0.95, population = "observed",
                            keep_draws = FALSE)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- factor:

  Factor whose levels are compared.

- rope:

  Optional numeric length-two ROPE in contrast units.

- meaningful:

  Optional non-negative absolute meaningful-effect threshold.

- level:

  Credible interval mass.

- population:

  Currently \`"observed"\`; \`"balanced"\` and \`"reference"\` are
  reserved and produce an explicit error in version 0.3.0.

- keep_draws:

  Store contrast draws as an attribute.

## Value

A data frame of pairwise posterior contrasts.

## Examples

``` r
# Example 1: CRD treatment contrasts
f1 <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                draws = 800)
bayes_contrasts(f1, "treatment")
#>         contrast level_a level_b       mean     median        sd      lower
#> 1    BioA - BioB    BioA    BioB -0.4055259 -0.4137717 0.1995434 -0.7941509
#> 2    BioA - BioC    BioA    BioC -0.7003793 -0.7094940 0.2044462 -1.1024956
#> 3 BioA - Control    BioA Control  0.2084100  0.2010917 0.1907749 -0.1682911
#> 4    BioB - BioC    BioB    BioC -0.2948535 -0.2905560 0.1884025 -0.6756467
#> 5 BioB - Control    BioB Control  0.6139359  0.6105357 0.1955926  0.2251577
#> 6 BioC - Control    BioC Control  0.9087893  0.9064270 0.1966649  0.5380200
#>          upper p_a_gt_b p_a_lt_b      pd rope_pct p_abs_gt_meaningful
#> 1  0.007868319  0.03000  0.97000 0.97000       NA                  NA
#> 2 -0.300925574  0.00125  0.99875 0.99875       NA                  NA
#> 3  0.563676375  0.86750  0.13250 0.86750       NA                  NA
#> 4  0.059672652  0.05375  0.94625 0.94625       NA                  NA
#> 5  1.006307482  0.99750  0.00250 0.99750       NA                  NA
#> 6  1.297460339  1.00000  0.00000 1.00000       NA                  NA
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1              NA                    NA
#> 2              NA                    NA
#> 3              NA                    NA
#> 4              NA                    NA
#> 5              NA                    NA
#> 6              NA                    NA

# Example 2: contrasts with a ROPE
bayes_contrasts(f1, "treatment", rope = c(-0.20, 0.20))
#>         contrast level_a level_b       mean     median        sd      lower
#> 1    BioA - BioB    BioA    BioB -0.4055259 -0.4137717 0.1995434 -0.7941509
#> 2    BioA - BioC    BioA    BioC -0.7003793 -0.7094940 0.2044462 -1.1024956
#> 3 BioA - Control    BioA Control  0.2084100  0.2010917 0.1907749 -0.1682911
#> 4    BioB - BioC    BioB    BioC -0.2948535 -0.2905560 0.1884025 -0.6756467
#> 5 BioB - Control    BioB Control  0.6139359  0.6105357 0.1955926  0.2251577
#> 6 BioC - Control    BioC Control  0.9087893  0.9064270 0.1966649  0.5380200
#>          upper p_a_gt_b p_a_lt_b      pd rope_pct p_abs_gt_meaningful
#> 1  0.007868319  0.03000  0.97000 0.97000  0.14375                  NA
#> 2 -0.300925574  0.00125  0.99875 0.99875  0.01125                  NA
#> 3  0.563676375  0.86750  0.13250 0.86750  0.48125                  NA
#> 4  0.059672652  0.05375  0.94625 0.94625  0.31125                  NA
#> 5  1.006307482  0.99750  0.00250 0.99750  0.02000                  NA
#> 6  1.297460339  1.00000  0.00000 1.00000  0.00000                  NA
#>   p_gt_meaningful p_lt_minus_meaningful
#> 1              NA                    NA
#> 2              NA                    NA
#> 3              NA                    NA
#> 4              NA                    NA
#> 5              NA                    NA
#> 6              NA                    NA

# Example 3: meaningful-effect probability in an RCBD
f2 <- bayes_fit(bayes_rcbd(bayes_teaching_data("rcbd"), "yield",
                "cultivar", "block"), draws = 800)
bayes_contrasts(f2, "cultivar", meaningful = 0.30)
#>    contrast level_a level_b         mean       median        sd        lower
#> 1   C1 - C2      C1      C2 -0.389292343 -0.384868323 0.2052071 -0.791186734
#> 2   C1 - C3      C1      C3 -0.810277734 -0.810589549 0.2072532 -1.225427094
#> 3   C1 - C4      C1      C4 -0.784043014 -0.784620837 0.2131381 -1.207363319
#> 4   C1 - C5      C1      C5 -0.390771379 -0.394882279 0.2060222 -0.792597024
#> 5   C2 - C3      C2      C3 -0.420985391 -0.417204529 0.2096793 -0.844069788
#> 6   C2 - C4      C2      C4 -0.394750671 -0.394478812 0.2124148 -0.810004322
#> 7   C2 - C5      C2      C5 -0.001479036  0.002455417 0.2185541 -0.440808100
#> 8   C3 - C4      C3      C4  0.026234720  0.032687470 0.2184978 -0.406918931
#> 9   C3 - C5      C3      C5  0.419506355  0.421104689 0.2191697 -0.006725293
#> 10  C4 - C5      C4      C5  0.393271635  0.386503822 0.2248329 -0.010771748
#>           upper p_a_gt_b p_a_lt_b      pd rope_pct p_abs_gt_meaningful
#> 1   0.003606751  0.02625  0.97375 0.97375       NA             0.68000
#> 2  -0.395566317  0.00000  1.00000 1.00000       NA             0.99000
#> 3  -0.361256624  0.00000  1.00000 1.00000       NA             0.98625
#> 4  -0.006986029  0.02375  0.97625 0.97625       NA             0.64250
#> 5  -0.013771983  0.02375  0.97625 0.97625       NA             0.71250
#> 6  -0.012520766  0.02250  0.97750 0.97750       NA             0.67750
#> 7   0.435628076  0.50250  0.49750 0.50250       NA             0.16625
#> 8   0.452346212  0.55000  0.45000 0.55000       NA             0.17000
#> 9   0.822120913  0.97250  0.02750 0.97250       NA             0.70500
#> 10  0.842779771  0.97250  0.02750 0.97250       NA             0.65875
#>    p_gt_meaningful p_lt_minus_meaningful
#> 1          0.00250               0.67750
#> 2          0.00000               0.99000
#> 3          0.00000               0.98625
#> 4          0.00000               0.64250
#> 5          0.00000               0.71250
#> 6          0.00000               0.67750
#> 7          0.08375               0.08250
#> 8          0.10375               0.06625
#> 9          0.70375               0.00125
#> 10         0.65625               0.00250
```
