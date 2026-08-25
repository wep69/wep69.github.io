# Calibrate physical image scale

Calibrate physical image scale

## Usage

``` r
pheno_calibrate_scale(pixel_distance, known_distance, unit = "mm")
```

## Arguments

- pixel_distance:

  Distance in pixels between two reference points.

- known_distance:

  Known physical distance between those points.

- unit:

  Character unit label, for example `"mm"`.

## Value

A `pheno_scale` object containing units per pixel and pixels per unit.

## Examples

``` r
pheno_calibrate_scale(100, 50, "mm")
#> $units_per_pixel
#> [1] 0.5
#> 
#> $pixels_per_unit
#> [1] 2
#> 
#> $unit
#> [1] "mm"
#> 
#> $pixel_distance
#> [1] 100
#> 
#> $known_distance
#> [1] 50
#> 
#> attr(,"class")
#> [1] "pheno_scale"
pheno_calibrate_scale(250, 10, "cm")
#> $units_per_pixel
#> [1] 0.04
#> 
#> $pixels_per_unit
#> [1] 25
#> 
#> $unit
#> [1] "cm"
#> 
#> $pixel_distance
#> [1] 250
#> 
#> $known_distance
#> [1] 10
#> 
#> attr(,"class")
#> [1] "pheno_scale"
pheno_calibrate_scale(sqrt(100^2 + 50^2), 20, "mm")
#> $units_per_pixel
#> [1] 0.1788854
#> 
#> $pixels_per_unit
#> [1] 5.59017
#> 
#> $unit
#> [1] "mm"
#> 
#> $pixel_distance
#> [1] 111.8034
#> 
#> $known_distance
#> [1] 20
#> 
#> attr(,"class")
#> [1] "pheno_scale"
```
