# Summarize stability of multi-crop ranking under uncertainty

Summarize stability of multi-crop ranking under uncertainty

## Usage

``` r
crop_rank_stability(
  x,
  ties_method = c("average", "min", "max"),
  na_policy = c("propagate", "available")
)
```

## Arguments

- x:

  Named list of aligned \`agri_uncertainty_ensemble\` objects.

- ties_method:

  Ranking method used within each ensemble member.

- na_policy:

  Missing-value policy.

## Value

An \`agri_crop_rank_stability\` containing weighted mean rank, weighted
rank SD, probability of being best for each crop, winner entropy, and
the probability margin between the two most likely winners.

## Examples

``` r
set.seed(2)
mk <- function(shift) { s <- matrix(pmin(1, pmax(0, runif(12, 0.2, 0.9) + shift)), nrow = 3)
  ensemble_from_scores(s, member_ids = paste0("m", 1:4)) }
x <- list(maize = mk(0), sorghum = mk(0.05), cassava = mk(-0.05))
crop_rank_stability(x)$statistics
#>      mean_rank_maize mean_rank_sorghum mean_rank_cassava sd_rank_maize
#> [1,]            2.50              1.50              2.00     0.5000000
#> [2,]            1.50              1.75              2.75     0.5000000
#> [3,]            1.75              2.25              2.00     0.8291562
#>      sd_rank_sorghum sd_rank_cassava P_best_maize P_best_sorghum P_best_cassava
#> [1,]       0.8660254       0.7071068          0.0           0.75           0.25
#> [2,]       0.8291562       0.4330127          0.5           0.50           0.00
#> [3,]       0.4330127       1.0000000          0.5           0.00           0.50
#>      winner_entropy winner_margin
#> [1,]      0.5118595           0.5
#> [2,]      0.6309298           0.0
#> [3,]      0.6309298           0.0
```
