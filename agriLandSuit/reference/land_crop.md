# Crop all land layers consistently

Crop all land layers consistently

## Usage

``` r
land_crop(x, y, mask = FALSE)
```

## Arguments

- x:

  An \`agri_land_data\` object.

- y:

  A SpatVector, SpatRaster, SpatExtent, or object accepted by
  \`terra::crop\`.

- mask:

  Logical; mask to \`y\` after cropping when supported.

## Value

Cropped \`agri_land_data\`.
