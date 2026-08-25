# Neural semantic segmentation

Runs semantic segmentation using either an in-memory/registered R
`torch` model or a user-supplied function that returns pixel
probabilities. Function models are useful for validation and for
adapters to external engines.

## Usage

``` r
pheno_segment_dl(
  x,
  model,
  classes = NULL,
  threshold = 0.5,
  positive_class = NULL,
  device = c("auto", "cpu", "cuda"),
  image_id = NULL,
  qc = TRUE
)
```

## Arguments

- x:

  RGB array or gray matrix.

- model:

  Torch model, `pheno_model_record`, `pheno_model_fit`, registered model
  id, or function.

- classes:

  Optional class labels. For a single-logit model, the default is
  `c("background", "foreground")`.

- threshold:

  Probability threshold for binary segmentation.

- positive_class:

  Positive class for binary segmentation.

- device:

  `"auto"`, `"cpu"`, or `"cuda"`.

- image_id:

  Optional image identifier.

- qc:

  Include `pheno_qc(x)` in the prediction object.

## Value

A `pheno_prediction` containing probabilities and mask.

## Examples

``` r
img <- pheno_data("leaf_rgb")
mock <- function(z) {
  s <- pheno_rgb_indices(z, "ExG")[, , 1]
  plogis(20 * (s - median(s)))
}
p1 <- pheno_segment_dl(img, mock); mean(p1$mask)
#> [1] 1
p2 <- pheno_segment_dl(img, mock, classes = c("background", "leaf"), threshold = .7)
if (requireNamespace("torch", quietly = TRUE)) {
  net <- pheno_unet(3, 2, base_channels = 4)
  p3 <- pheno_segment_dl(img, net, classes = c("background", "leaf"), device = "cpu")
}
```
