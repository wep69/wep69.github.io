# Gaussian fuzzy membership

Gaussian fuzzy membership

## Usage

``` r
membership_gaussian(x, center, sigma)
```

## Arguments

- x:

  Numeric values or a \`terra::SpatRaster\`.

- center:

  Center with membership one.

- sigma:

  Positive Gaussian width parameter. The definition matches
  \`skfuzzy::gaussmf\`, namely exp(-((x-center)/sigma)^2).

## Value

Object of the same data type as \`x\`, with membership in (0, 1\].
