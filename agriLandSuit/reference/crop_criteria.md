# Score all available requirements in a crop profile

Score all available requirements in a crop profile

## Usage

``` r
crop_criteria(
  land,
  crop,
  engine = c("r", "python", "auto"),
  strict = TRUE,
  strict_units = TRUE
)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- crop:

  An \`agri_crop_profile\`.

- engine:

  \`"r"\`, \`"python"\`, or \`"auto"\`.

- strict:

  If TRUE, every crop requirement must have a matching land layer.

- strict_units:

  If TRUE, unit metadata must match exactly.

## Value

An \`agri_criterion_set\` object. No cross-criterion aggregation is
performed by this function. Use \`suit_aggregate()\` or
\`land_suitability()\` for composition.
