# Identify the best crop at each unit or raster cell

Identify the best crop at each unit or raster cell

## Usage

``` r
best_crop(
  x,
  tie_policy = c("NA", "first"),
  na_policy = c("propagate", "available")
)
```

## Arguments

- x:

  Multi-crop comparison input.

- tie_policy:

  \`NA\` (default) leaves the winning crop undefined when two or more
  crops share the maximum score; \`first\` uses stable crop order but
  still reports the number tied.

- na_policy:

  Missing-value policy.

## Value

An \`agri_crop_selection\` object with best crop code, score, and number
of crops tied for first.
