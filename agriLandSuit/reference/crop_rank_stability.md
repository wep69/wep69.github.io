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
