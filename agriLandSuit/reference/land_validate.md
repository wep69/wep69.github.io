# Validate a land data container

Validate a land data container

## Usage

``` r
land_validate(x, strict = TRUE, require_units = FALSE)
```

## Arguments

- x:

  An \`agri_land_data\` object.

- strict:

  If TRUE, geometry mismatches are errors; otherwise warnings.

- require_units:

  Require units for every environmental layer.

## Value

An \`agri_validation\` object.
