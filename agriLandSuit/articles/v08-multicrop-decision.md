# Multi-crop comparison and decision stability

## Purpose

Version 0.8.0 compares suitability analyses that have already been
completed for different crops. It does not modify crop requirements,
aggregation rules, constraints, scenarios, or management assumptions.

## Deterministic comparison

Assume `maize_suit`, `bean_suit`, `sorghum_suit`, and `cassava_suit` are
compatible `agri_suitability` objects:

``` r

cmp <- compare_crops(list(
  maize = maize_suit,
  bean = bean_suit,
  sorghum = sorghum_suit,
  cassava = cassava_suit
))

crop_rank(cmp)
best_crop(cmp)
second_best_crop(cmp)
decision_margin(cmp)
```

By default, a tie for first place is not resolved arbitrarily. The
winning crop code is left missing, the number of tied crops is retained,
and the decision margin is zero.

## Probabilistic comparison

When each crop has an aligned uncertainty ensemble:

``` r

ens <- list(
  maize = maize_mc,
  bean = bean_mc,
  sorghum = sorghum_mc
)

crop_winner_probability(ens)
crop_rank_stability(ens)
```

Winner probabilities are calculated inside every ensemble member and
only then summarized. This is not equivalent to ranking ensemble mean
suitability.

## Interpretation

A crop can have the largest mean suitability while having a low
probability of being the best crop if uncertainty is large. For spatial
planning, the combination of best crop, decision margin, probability of
being best, and winner entropy provides a more informative decision
surface than a single deterministic winner map.
