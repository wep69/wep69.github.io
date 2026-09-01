# Score terrain criteria from a crop profile

Score terrain criteria from a crop profile

## Usage

``` r
terrain_criteria(
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

  An \`agri_crop_profile\` object.

- engine:

  \`"r"\`, \`"python"\`, or \`"auto"\`.

- strict:

  If TRUE, every requirement in the selected domain must have a matching
  land layer.

- strict_units:

  If TRUE, source-layer units must match the crop profile.

## Value

An \`agri_domain_criteria\` object for the terrain domain.
