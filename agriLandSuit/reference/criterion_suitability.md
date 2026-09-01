# Score one criterion from an explicit crop requirement

Score one criterion from an explicit crop requirement

## Usage

``` r
criterion_suitability(x, requirement, engine = c("r", "python", "auto"))
```

## Arguments

- x:

  Numeric values or one \`terra::SpatRaster\` layer.

- requirement:

  An \`agri_crop_requirement\`.

- engine:

  \`"r"\`, \`"python"\`, or \`"auto"\`.

## Value

An \`agri_criterion_suit\` object.
