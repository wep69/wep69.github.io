# Probability that each crop is the best option

Probability that each crop is the best option

## Usage

``` r
crop_winner_probability(
  x,
  ties = c("split", "first"),
  na_policy = c("propagate", "available")
)
```

## Arguments

- x:

  Named list of aligned \`agri_uncertainty_ensemble\` objects, one per
  crop.

- ties:

  \`split\` divides a member's probability mass among tied winners;
  \`first\` assigns it to the first crop in stable list order.

- na_policy:

  \`propagate\` uses an ensemble member only where every crop is
  observed; \`available\` permits competition among available crops.

## Value

An \`agri_crop_winner_probability\` object.

## Examples

``` r
set.seed(2)
mk <- function(shift) { s <- matrix(pmin(1, pmax(0, runif(12, 0.2, 0.9) + shift)), nrow = 3)
  ensemble_from_scores(s, member_ids = paste0("m", 1:4)) }
x <- list(maize = mk(0), sorghum = mk(0.05), cassava = mk(-0.05))
crop_winner_probability(x)$probability
#>      P_best_maize P_best_sorghum P_best_cassava
#> [1,]          0.0           0.75           0.25
#> [2,]          0.5           0.50           0.00
#> [3,]          0.5           0.00           0.50
```
