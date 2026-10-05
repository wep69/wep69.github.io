# Plot membership functions of a crop profile

Plot membership functions of a crop profile

## Usage

``` r
plot_membership(x, n = 201L)
```

## Arguments

- x:

  An \`agri_crop_requirement\` or \`agri_crop_profile\`.

- n:

  Points per curve.

## Value

A \`ggplot\` object with one panel per criterion.

## Examples

``` r
if (requireNamespace("ggplot2", quietly = TRUE)) plot_membership(crop_profile_library("sorghum")$sorghum)
```
