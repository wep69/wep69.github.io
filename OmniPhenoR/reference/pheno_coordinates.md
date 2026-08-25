# Normalize image-coordinate metadata

Normalize image-coordinate metadata

## Usage

``` r
pheno_coordinates(x, width, height, to = c("pixel_xy", "normalized_xy"))
```

## Arguments

- x:

  A `pheno_detection` or bounding-box table.

- width:

  Image width.

- height:

  Image height.

- to:

  `pixel_xy` or `normalized_xy`.

## Value

Converted detection/table.

## Examples

``` r
d <- pheno_detection(
  data.frame(class="leaf",confidence=.9,xmin=10,ymin=10,xmax=40,ymax=50),
  image_size=c(100,100))
pheno_coordinates(d, 100, 100, "normalized_xy")
#> <pheno_detection>
#>   objects: 1 
#>   engine: native 
#>   classes: leaf 
pheno_coordinates(d, 100, 100, "pixel_xy")
#> <pheno_detection>
#>   objects: 1 
#>   engine: native 
#>   classes: leaf 
```
