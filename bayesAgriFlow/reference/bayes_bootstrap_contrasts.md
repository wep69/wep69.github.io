# Posterior contrasts from a grouped Bayesian bootstrap

Compute all pairwise posterior contrasts and optional ROPE and
meaningful-effect probabilities.

## Usage

``` r
bayes_bootstrap_contrasts(x, rope = NULL, meaningful = NULL, level = 0.95)
```

## Arguments

- x:

  A grouped bayes_bootstrap object.

- rope:

  Optional practical-equivalence interval.

- meaningful:

  Optional absolute meaningful-effect threshold.

- level:

  Credible interval mass.

## Value

An object or summary described in the function documentation.

## Details

This interface is part of bayesAgriFlow 1.0.0. Optional brms, Stan, and
Python backends are never installed or invoked silently. Scientific
interpretation should be based on posterior uncertainty, model
diagnostics, and the stated estimand rather than on a single index.

## Examples

``` r
# Example 1
bb <- bayes_bootstrap(bayes_teaching_data("crd"), "yield", "treatment", draws = 500)
bayes_bootstrap_contrasts(bb)
#>         contrast     median      lower      upper    pd rope_pct
#> 1    BioA - BioB -0.4475904 -0.7051358 -0.1334033 1.000       NA
#> 2    BioA - BioC -0.7418118 -1.1307408 -0.2765075 1.000       NA
#> 3 BioA - Control  0.1841114 -0.1163130  0.4852844 0.884       NA
#> 4    BioB - BioC -0.2999773 -0.6557027  0.1276081 0.900       NA
#> 5 BioB - Control  0.6373539  0.3450475  0.8813757 1.000       NA
#> 6 BioC - Control  0.9376206  0.4485299  1.2920729 1.000       NA
#>   p_abs_gt_meaningful
#> 1                  NA
#> 2                  NA
#> 3                  NA
#> 4                  NA
#> 5                  NA
#> 6                  NA

# Example 2
bayes_bootstrap_contrasts(bb, rope = c(-0.2, 0.2))
#>         contrast     median      lower      upper    pd rope_pct
#> 1    BioA - BioB -0.4475904 -0.7051358 -0.1334033 1.000    0.044
#> 2    BioA - BioC -0.7418118 -1.1307408 -0.2765075 1.000    0.008
#> 3 BioA - Control  0.1841114 -0.1163130  0.4852844 0.884    0.536
#> 4    BioB - BioC -0.2999773 -0.6557027  0.1276081 0.900    0.324
#> 5 BioB - Control  0.6373539  0.3450475  0.8813757 1.000    0.002
#> 6 BioC - Control  0.9376206  0.4485299  1.2920729 1.000    0.000
#>   p_abs_gt_meaningful
#> 1                  NA
#> 2                  NA
#> 3                  NA
#> 4                  NA
#> 5                  NA
#> 6                  NA

# Example 3
bayes_bootstrap_contrasts(bb, meaningful = 0.5)
#>         contrast     median      lower      upper    pd rope_pct
#> 1    BioA - BioB -0.4475904 -0.7051358 -0.1334033 1.000       NA
#> 2    BioA - BioC -0.7418118 -1.1307408 -0.2765075 1.000       NA
#> 3 BioA - Control  0.1841114 -0.1163130  0.4852844 0.884       NA
#> 4    BioB - BioC -0.2999773 -0.6557027  0.1276081 0.900       NA
#> 5 BioB - Control  0.6373539  0.3450475  0.8813757 1.000       NA
#> 6 BioC - Control  0.9376206  0.4485299  1.2920729 1.000       NA
#>   p_abs_gt_meaningful
#> 1               0.380
#> 2               0.844
#> 3               0.016
#> 4               0.162
#> 5               0.818
#> 6               0.960
```
