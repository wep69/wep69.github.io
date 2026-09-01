# Categorical fuzzy membership

Categorical fuzzy membership

## Usage

``` r
membership_categorical(x, mapping, default = NA_real_)
```

## Arguments

- x:

  Numeric/character vector or a single-layer \`terra::SpatRaster\`.

- mapping:

  Named numeric vector. Names represent source categories/codes; values
  are suitability scores in \[0, 1\].

- default:

  Score assigned to unmatched non-missing values; default is NA.

## Value

Numeric vector or SpatRaster with membership in \[0, 1\].
