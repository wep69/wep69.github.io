# Score crop requirements from one environmental domain

This function keeps criterion scores separate. It does not aggregate
them into a composite suitability index.

## Usage

``` r
domain_criteria(
  land,
  crop,
  domain = c("climate", "soil", "terrain", "water"),
  engine = c("r", "python", "auto"),
  strict = TRUE,
  strict_units = TRUE
)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- crop:

  An \`agri_crop_profile\` object.

- domain:

  One of climate, soil, terrain, or water.

- engine:

  \`"r"\`, \`"python"\`, or \`"auto"\`.

- strict:

  If TRUE, every requirement in the selected domain must have a matching
  land layer.

- strict_units:

  If TRUE, source-layer units must match the crop profile.

## Value

An \`agri_domain_criteria\` object, which also inherits from
\`agri_criterion_set\`.
