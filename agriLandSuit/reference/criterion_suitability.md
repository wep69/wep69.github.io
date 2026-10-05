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

## Examples

``` r
req <- crop_requirement("Pseason", "climate", "mm", "range", c(400, 600, 1200, 1800),
                        source = "illustrative", source_id = "demo-rain")
criterion_suitability(c(350, 500, 900, 1600, 2000), req)$score
#> [1] 0.0000000 0.5000000 1.0000000 0.3333333 0.0000000
```
