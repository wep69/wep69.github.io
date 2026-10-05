# Sensitivity of suitability to the aggregation method

Sensitivity of suitability to the aggregation method

## Usage

``` r
method_sensitivity(
  x,
  methods = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  reference = "limiting",
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  closed = "left"
)
```

## Arguments

- x:

  Criterion scores accepted by \`suit_aggregate()\`.

- methods:

  Aggregation methods compared.

- weights:

  Optional criterion weights for weighted methods.

- reference:

  Method used as reference.

- breaks, labels, closed:

  Class definition.

## Value

List with \`scores\` (matrix, one column per method) and \`summary\`
(Spearman correlation with the reference, share of units in the same
class, mean absolute difference).

## Examples

``` r
p <- crop_profile_library("maize", domains = c("climate", "water"))$maize
z <- profile_scores(p, list(LGP = c(80, 120, 160), Pseason = c(450, 800, 1500), CV = c(0.4, 0.3, 0.2),
                            Tseason = c(27, 25, 22), Twarm = c(33, 31, 28)))
method_sensitivity(z)$summary
#>                method  spearman same_class mean_abs_diff
#> 1            limiting 1.0000000  1.0000000     0.0000000
#> 2 weighted_arithmetic 0.8660254  0.0000000     0.3366667
#> 3  weighted_geometric 0.8660254  0.6666667     0.2042021
```
