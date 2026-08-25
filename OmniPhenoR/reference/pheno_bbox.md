# Convert or validate bounding boxes

Convert or validate bounding boxes

## Usage

``` r
pheno_bbox(
  boxes,
  from = c("pixel", "normalized"),
  to = c("pixel", "normalized"),
  width,
  height,
  clip = TRUE
)
```

## Arguments

- boxes:

  Data frame/matrix with `xmin`, `ymin`, `xmax`, `ymax`.

- from:

  `pixel` or `normalized` coordinates.

- to:

  `pixel` or `normalized` coordinates.

- width:

  Image width in pixels.

- height:

  Image height in pixels.

- clip:

  Clip coordinates to valid image bounds.

## Value

A tibble with converted boxes.

## Examples

``` r
b <- data.frame(xmin=10,ymin=20,xmax=50,ymax=60)
pheno_bbox(b, "pixel", "normalized", 100, 100)
#> # A tibble: 1 × 4
#>    xmin  ymin  xmax  ymax
#>   <dbl> <dbl> <dbl> <dbl>
#> 1   0.1   0.2   0.5   0.6
pheno_bbox(pheno_bbox(b,"pixel","normalized",100,100), "normalized","pixel",100,100)
#> # A tibble: 1 × 4
#>    xmin  ymin  xmax  ymax
#>   <dbl> <dbl> <dbl> <dbl>
#> 1    10    20    50    60
pheno_bbox(data.frame(xmin=-2,ymin=0,xmax=110,ymax=90), width=100,height=100,clip=TRUE)
#> # A tibble: 1 × 4
#>    xmin  ymin  xmax  ymax
#>   <dbl> <dbl> <dbl> <dbl>
#> 1     0     0   100    90
```
