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

## Examples

``` r
s <- suit_aggregate(cbind(rain = c(0.9, 0.6, 0.3, 0.1), temp = c(1, 0.7, 0.8, 0.5)))
cl <- suit_classify(s)
suitability_class(cl)
#> <agri_suitability_class>
#>  code label lower upper
#>     1     N  0.00  0.25
#>     2    S3  0.25  0.50
#>     3    S2  0.50  0.75
#>     4    S1  0.75  1.00
```
