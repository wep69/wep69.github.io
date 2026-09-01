# Classify continuous suitability

Classify continuous suitability

## Usage

``` r
suit_classify(
  x,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1")
)
```

## Arguments

- x:

  An \`agri_suitability\`, single-layer \`SpatRaster\`, or numeric
  vector.

- breaks:

  Strictly increasing cut points covering 0 through 1.

- labels:

  Class labels from lowest to highest suitability.

## Value

An \`agri_suitability_class\` object. Raster classes are integer-coded
and accompanied by an explicit code/label key.
