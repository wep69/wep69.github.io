# Frozen synthetic teaching data

Returns deterministic image and mask objects used in examples and tests.
They are pedagogical simulations, not field evidence.

## Usage

``` r
pheno_data(
  name = c("leaf_rgb", "leaf_gray", "leaf_mask", "lesion_mask", "canopy_rgb",
    "growth_series", "disease_series", "drought_recovery", "weather_series")
)
```

## Arguments

- name:

  One of `"leaf_rgb"`, `"leaf_gray"`, `"leaf_mask"`, `"lesion_mask"`,
  `"canopy_rgb"`, `"growth_series"`, `"disease_series"`,
  `"drought_recovery"`, or `"weather_series"`.

## Value

A numeric array, logical matrix, or deterministic teaching data frame,
depending on `name`.

## Examples

``` r
dim(pheno_data("leaf_rgb"))
#> [1]  96 128   3
mean(pheno_data("leaf_mask"))
#> [1] 0.233724
range(pheno_data("leaf_gray"))
#> [1] 0.322372 0.669716
```
