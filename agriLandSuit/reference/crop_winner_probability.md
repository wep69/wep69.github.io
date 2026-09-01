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
