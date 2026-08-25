# Read an image or raster for phenotyping

Reads common photographic formats through `magick` and geospatial
rasters through `terra`. The function performs no hidden downloads.

## Usage

``` r
pheno_read(path, engine = c("auto", "magick", "terra"))
```

## Arguments

- path:

  Existing file path.

- engine:

  `"auto"`, `"magick"`, or `"terra"`.

## Value

An RGB array for photographic files or a `SpatRaster` for spatial
rasters.

## Examples

``` r
f <- tempfile(fileext = ".rds")
saveRDS(pheno_data("leaf_rgb"), f)
identical(readRDS(f), pheno_data("leaf_rgb"))
#> [1] TRUE
if (requireNamespace("magick", quietly = TRUE)) {
  p <- system.file("logo", "Rlogo.png", package = "png")
  if (nzchar(p)) p
}
pheno_capabilities()[c("capability","available")]
#> # A tibble: 40 × 2
#>    capability     available
#>    <chr>          <lgl>    
#>  1 rgb_indices    TRUE     
#>  2 segmentation   TRUE     
#>  3 morphology     TRUE     
#>  4 texture        TRUE     
#>  5 plant_image    TRUE     
#>  6 orthomosaic    TRUE     
#>  7 spatial_vector TRUE     
#>  8 gabor          TRUE     
#>  9 wavelet        TRUE     
#> 10 deep_learning  TRUE     
#> # ℹ 30 more rows
```
