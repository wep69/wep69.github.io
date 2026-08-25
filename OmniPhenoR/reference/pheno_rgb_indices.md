# Compute an expanded set of RGB vegetation and color indices

Computes indices through a common interface for RGB arrays, pixel
tables, named RGB lists, or
[`terra::SpatRaster`](https://rspatial.github.io/terra/reference/SpatRaster-class.html)
objects. The engine uses canonical internal unit and byte scales so
formulas with additive constants are not silently changed when input
data are stored as 0–1 versus 0–255.

## Usage

``` r
pheno_rgb_indices(
  x,
  indices = "all",
  scale = c("auto", "unit", "byte", "native"),
  channels = c("R", "G", "B"),
  eps = 1e-12,
  strict = TRUE
)
```

## Arguments

- x:

  RGB input. Supported forms are an H x W x 3 numeric array, a data
  frame/matrix with RGB columns, a named list with `R`, `G`, and `B`, or
  a `SpatRaster` with at least three layers.

- indices:

  Character vector of index names, or `"all"`.

- scale:

  Input scale: `"auto"`, `"unit"` (0–1), `"byte"` (0–255), or
  `"native"`. `"auto"` recognizes common 0–1 and 0–255 representations.

- channels:

  Column names used for table/list inputs.

- eps:

  Denominator tolerance.

- strict:

  If `TRUE`, unknown index names are an error.

## Value

The same broad spatial/tabular representation as `x`, with one layer or
column per requested index.

## Details

`NGRDI` is defined canonically here as `(G-R)/(G+R)`. Some software uses
the same label for a three-channel denominator. OmniPhenoR exposes that
alternative explicitly as `NGRDI3` rather than silently changing
definitions. `GRVI` is retained as an alias of canonical `NGRDI`.

The registry deliberately includes several historical color indices
whose numerical magnitude depends on image scaling. Use the catalog's
`scaling` field when comparing studies.

## Examples

``` r
img <- pheno_data("leaf_rgb")
x1 <- pheno_rgb_indices(img, c("ExG","NGRDI","GLI","VARI","TGI")); dim(x1)
#> [1]  96 128   5
x2 <- pheno_rgb_indices(
  data.frame(R = c(40, 90), G = c(120, 100), B = c(30, 60)),
  c("ExG", "CIVE", "RGBVI"),
  scale = "byte"
)
x2
#> # A tibble: 2 × 3
#>     ExG    CIVE RGBVI
#>   <dbl>   <dbl> <dbl>
#> 1 0.895 -49.3   0.846
#> 2 0.2     0.477 0.299
x3 <- pheno_rgb_indices(list(R=c(.2,.4),G=c(.6,.5),B=c(.1,.2)), c("r","g","b","GRRI")); x3
#> # A tibble: 2 × 4
#>       r     g     b  GRRI
#>   <dbl> <dbl> <dbl> <dbl>
#> 1 0.222 0.667 0.111  3   
#> 2 0.364 0.455 0.182  1.25
```
