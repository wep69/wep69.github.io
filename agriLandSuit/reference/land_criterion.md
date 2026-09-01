# Score a crop requirement against its matching land layer

Score a crop requirement against its matching land layer

## Usage

``` r
land_criterion(
  land,
  requirement,
  engine = c("r", "python", "auto"),
  strict_units = TRUE
)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- requirement:

  An \`agri_crop_requirement\`.

- engine:

  \`"r"\`, \`"python"\`, or \`"auto"\`.

- strict_units:

  If TRUE, missing or non-identical unit metadata cause an error.

## Value

An \`agri_criterion_suit\` object.
