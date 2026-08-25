# Predict a large image tile-by-tile

Predict a large image tile-by-tile

## Usage

``` r
pheno_predict_tiles(
  x,
  model,
  tile_size = c(256L, 256L),
  overlap = 32L,
  classes = NULL,
  threshold = 0.5,
  device = c("auto", "cpu", "cuda"),
  ...
)
```

## Arguments

- x:

  Matrix/RGB array or a precomputed `pheno_tiles` object.

- model:

  Model accepted by
  [`pheno_segment_dl()`](https://wep69.github.io/OmniPhenoR/reference/pheno_segment_dl.md).

- tile_size:

  Tile dimensions used when `x` is an image.

- overlap:

  Tile overlap.

- classes:

  Optional segmentation classes.

- threshold:

  Binary threshold after merging probabilities.

- device:

  Compute device.

- ...:

  Additional arguments passed to
  [`pheno_segment_dl()`](https://wep69.github.io/OmniPhenoR/reference/pheno_segment_dl.md).

## Value

A `pheno_prediction` reconstructed from tile probabilities.

## Examples

``` r
img <- pheno_data("leaf_rgb")
mock <- function(z) matrix(.8, dim(z)[1], dim(z)[2])
p1 <- pheno_predict_tiles(img, mock, tile_size = c(48,64), overlap = 8)
mean(p1$mask)
#> [1] 1
p2 <- pheno_predict_tiles(pheno_tile(img, c(48,64), 8), mock)
dim(p2$probability)
#> [1]  96 128   2
```
