# Rank crops by suitability

Rank crops by suitability

## Usage

``` r
crop_rank(
  x,
  ties_method = c("average", "min", "max"),
  na_policy = c("propagate", "available")
)
```

## Arguments

- x:

  An \`agri_multi_crop_comparison\` or a named list accepted by
  \`compare_crops()\`.

- ties_method:

  Ranking method for equal scores: \`average\`, \`min\`, or \`max\`.

- na_policy:

  \`propagate\` makes the complete local ranking missing when any crop
  is missing; \`available\` ranks only observed crops.

## Value

An \`agri_crop_rank\` object with rank 1 representing highest
suitability.
