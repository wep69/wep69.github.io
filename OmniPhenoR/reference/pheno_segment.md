# Segment plant tissue from an image

Provides a unified segmentation entry point. Classical `"native"`
execution calculates an RGB index followed by Otsu or user-defined
thresholding. `engine = "torch"` delegates to
[`pheno_segment_dl()`](https://wep69.github.io/OmniPhenoR/reference/pheno_segment_dl.md)
and requires a model. `engine = "auto"` selects torch only when a model
is explicitly supplied; otherwise it preserves the transparent native
workflow. The chosen reason is recorded as an attribute on native masks
and in prediction provenance.

## Usage

``` r
pheno_segment(
  x,
  index = "ExG",
  threshold = "otsu",
  direction = c("auto", "above", "below"),
  min_size = 0L,
  connectivity = c(8L, 4L),
  invert = FALSE,
  engine = c("native", "auto", "torch"),
  model = NULL,
  return = c("mask", "prediction"),
  ...
)
```

## Arguments

- x:

  RGB image accepted by
  [`pheno_rgb_indices()`](https://wep69.github.io/OmniPhenoR/reference/pheno_rgb_indices.md),
  or a numeric matrix when `index = NULL`.

- index:

  RGB index name, usually `"ExG"`, `"GLI"`, or `"NGRDI"`.

- threshold:

  Numeric threshold or `"otsu"` for native segmentation. For torch
  segmentation, a numeric value is used as the binary probability
  threshold and `"otsu"` maps to 0.5.

- direction:

  `"above"`, `"below"`, or `"auto"` for native segmentation.

- min_size:

  Minimum connected-component size in pixels for native segmentation and
  optional post-processing of binary neural masks.

- connectivity:

  Four- or eight-neighbour connectivity.

- invert:

  Logical; invert the final native/binary mask.

- engine:

  `"native"`, `"auto"`, or `"torch"`.

- model:

  Optional model accepted by
  [`pheno_segment_dl()`](https://wep69.github.io/OmniPhenoR/reference/pheno_segment_dl.md).

- return:

  `"mask"` for compatibility with 0.1.0 or `"prediction"` to retain
  probabilities, uncertainty, and model provenance.

- ...:

  Additional arguments passed to
  [`pheno_segment_dl()`](https://wep69.github.io/OmniPhenoR/reference/pheno_segment_dl.md)
  for torch execution.

## Value

A `pheno_mask`, or a `pheno_prediction` when `return = "prediction"`.

## Examples

``` r
img <- pheno_data("leaf_rgb"); m1 <- pheno_segment(img, "ExG"); mean(m1)
#> [1] 0.233724
img2 <- pheno_data("canopy_rgb"); m2 <- pheno_segment(img2, "NGRDI", min_size = 30); sum(m2)
#> [1] 5910
mock <- function(z) matrix(.8, dim(z)[1], dim(z)[2])
p <- pheno_segment(img, engine = "auto", model = mock, return = "prediction"); p$engine
#> [1] "function"
```
