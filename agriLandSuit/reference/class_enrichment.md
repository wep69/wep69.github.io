# Enrichment of crop area by suitability class

Enrichment of crop area by suitability class

## Usage

``` r
class_enrichment(
  suit,
  area,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  closed = "left"
)
```

## Arguments

- suit:

  Numeric scores (vector) or \`SpatRaster\`.

- area:

  Harvested area, same length or grid as \`suit\`.

- breaks, labels, closed:

  Class definition.

## Value

Data frame with class, share of crop area, share of land and enrichment
(ratio of the two).

## Examples

``` r
class_enrichment(suit = c(0.1, 0.3, 0.6, 0.8, 0.9, 0.2), area = c(5, 10, 40, 60, 80, 0))
#>   class share_area share_land enrichment
#> 1     N 0.02564103  0.3333333 0.07692308
#> 2    S3 0.05128205  0.1666667 0.30769231
#> 3    S2 0.20512821  0.1666667 1.23076923
#> 4    S1 0.71794872  0.3333333 2.15384615
```
