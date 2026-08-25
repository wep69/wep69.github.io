# Merge tiled numeric predictions

Overlapping numeric predictions are averaged. Logical masks are
converted to numeric votes and thresholded at 0.5 when
`logical_output = TRUE`.

## Usage

``` r
pheno_merge_tiles(tiles, logical_output = FALSE, threshold = 0.5)
```

## Arguments

- tiles:

  A `pheno_tiles` object whose `tiles` elements contain numeric or
  logical predictions aligned to the original tile index.

- logical_output:

  Return a logical matrix after averaging.

- threshold:

  Threshold used for logical output.

## Value

Reconstructed matrix or array.

## Examples

``` r
img <- pheno_data("leaf_gray")
tt <- pheno_tile(img, 32, overlap = 8)
z <- pheno_merge_tiles(tt); dim(z)
#> [1]  96 128
mm <- pheno_tile(pheno_data("leaf_mask"), 32, overlap = 8)
all(pheno_merge_tiles(mm, logical_output = TRUE) == pheno_data("leaf_mask"))
#> [1] TRUE
rgb <- pheno_tile(pheno_data("leaf_rgb"), c(48,64), overlap = 8); dim(pheno_merge_tiles(rgb))
#> [1]  96 128   3
```
