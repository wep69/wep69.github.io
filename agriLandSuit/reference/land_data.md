# Create an agricultural land data container

Create an agricultural land data container

## Usage

``` r
land_data(
  climate = NULL,
  soil = NULL,
  terrain = NULL,
  water = NULL,
  constraints = NULL,
  template = NULL,
  units = NULL,
  metadata = list(),
  validate = TRUE
)
```

## Arguments

- climate, soil, terrain, water, constraints:

  A \`terra::SpatRaster\`, raster file path, named list of
  rasters/paths, or NULL.

- template:

  Optional SpatRaster used as the target geometry. If NULL, the first
  available layer is used.

- units:

  Named character vector keyed by bare layer name or \`domain.layer\`.

- metadata:

  Named list of provenance or study metadata.

- validate:

  Logical; validate geometry and metadata immediately.

## Value

An object of class \`agri_land_data\`.
