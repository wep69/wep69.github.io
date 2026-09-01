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
