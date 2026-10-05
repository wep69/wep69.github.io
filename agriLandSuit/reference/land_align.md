# Align land layers to a common template

Align land layers to a common template

## Usage

``` r
land_align(
  x,
  template = NULL,
  categorical = NULL,
  method_continuous = "bilinear",
  method_categorical = "near"
)
```

## Arguments

- x:

  An \`agri_land_data\` object.

- template:

  Optional target SpatRaster; defaults to \`x\$template\`.

- categorical:

  Character vector of \`domain.layer\` keys to resample using nearest
  neighbour.

- method_continuous:

  Continuous resampling method passed to
  \`terra::resample\`/\`project\`.

- method_categorical:

  Categorical resampling method.

## Value

An aligned \`agri_land_data\` object.

## Examples

``` r
r <- terra::rast(ncols = 4, nrows = 3, xmin = 30, xmax = 34, ymin = -20, ymax = -17, crs = "EPSG:4326")
lay <- function(v, n) { x <- r; terra::values(x) <- v; names(x) <- n; x }
fine <- terra::rast(ncols = 8, nrows = 6, xmin = 30, xmax = 34, ymin = -20, ymax = -17, crs = "EPSG:4326")
terra::values(fine) <- runif(48, 5, 7); names(fine) <- "pH"
land <- land_data(climate = lay(seq(400, 1500, length.out = 12), "Pseason"), soil = fine, validate = FALSE)
land_validate(land)$ok
#> [1] FALSE
land_validate(land_align(land))$ok
#> [1] TRUE
```
