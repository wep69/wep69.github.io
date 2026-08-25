# Extract raster traits by spatial geometry

Thin, explicit wrapper around
[`terra::extract()`](https://rspatial.github.io/terra/reference/extract.html)
for phenotyping rasters. The original geometry identifier is preserved
so extracted image traits can be joined back to experimental units.

## Usage

``` r
pheno_spatial_extract(raster, geometry, fun = mean, na.rm = TRUE, ...)
```

## Arguments

- raster:

  A
  [`terra::SpatRaster`](https://rspatial.github.io/terra/reference/SpatRaster-class.html).

- geometry:

  A `SpatVector` or an object coercible through
  [`terra::vect()`](https://rspatial.github.io/terra/reference/vect.html).

- fun:

  Summary function, such as `mean`, `median`, or `sum`.

- na.rm:

  Passed to
  [`terra::extract()`](https://rspatial.github.io/terra/reference/extract.html).

- ...:

  Additional arguments passed to
  [`terra::extract()`](https://rspatial.github.io/terra/reference/extract.html).

## Value

A tibble.

## Examples

``` r
if (requireNamespace("terra", quietly = TRUE)) {
  r <- terra::rast(nrows = 10, ncols = 10)
  terra::values(r) <- 1:100
  p <- terra::as.polygons(terra::ext(r))
  pheno_spatial_extract(r, p)
}
#> # A tibble: 1 × 2
#>      ID lyr.1
#>   <int> <dbl>
#> 1     1  50.5
if (requireNamespace("terra", quietly = TRUE)) {
  r <- terra::rast(nrows = 4, ncols = 4, nlyrs = 2)
  terra::values(r) <- matrix(1:32, 16, 2)
  p <- terra::as.polygons(terra::ext(r))
  pheno_spatial_extract(r, p, fun = stats::median)
}
#> # A tibble: 1 × 3
#>      ID lyr.1 lyr.2
#>   <int> <dbl> <dbl>
#> 1     1   8.5  24.5
if (requireNamespace("terra", quietly = TRUE)) {
  r <- terra::rast(nrows = 5, ncols = 5)
  terra::values(r) <- stats::runif(25)
  p <- terra::as.polygons(terra::ext(r))
  pheno_spatial_extract(r, p, fun = sum)
}
#> # A tibble: 1 × 2
#>      ID lyr.1
#>   <int> <dbl>
#> 1     1  15.2
```
