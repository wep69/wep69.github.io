# Tile a large image with optional overlap

Tile a large image with optional overlap

## Usage

``` r
pheno_tile(
  x,
  tile_size = c(256L, 256L),
  overlap = 32L,
  pad = TRUE,
  pad_value = 0
)
```

## Arguments

- x:

  Numeric matrix or H x W x C array.

- tile_size:

  Tile height and width.

- overlap:

  Overlap in pixels, scalar or length two.

- pad:

  If `TRUE`, edge tiles are padded to `tile_size`; otherwise edge tiles
  may be smaller.

- pad_value:

  Value used for padding.

## Value

A `pheno_tiles` object containing tiles and their source coordinates.

## Examples

``` r
t1 <- pheno_tile(pheno_data("leaf_rgb"), tile_size = c(48,64), overlap = 8)
length(t1$tiles); head(t1$index)
#> [1] 9
#> # A tibble: 6 × 7
#>    tile row_start row_end col_start col_end valid_height valid_width
#>   <int>     <int>   <int>     <int>   <int>        <int>       <int>
#> 1     1         1      48         1      64           48          64
#> 2     2         1      48        57     120           48          64
#> 3     3         1      48        65     128           48          64
#> 4     4        41      88         1      64           48          64
#> 5     5        41      88        57     120           48          64
#> 6     6        41      88        65     128           48          64
t2 <- pheno_tile(pheno_data("leaf_gray"), tile_size = 32, overlap = 0, pad = FALSE)
t3 <- pheno_tile(pheno_data("canopy_rgb"), tile_size = c(50,50), overlap = c(10,10))
```
