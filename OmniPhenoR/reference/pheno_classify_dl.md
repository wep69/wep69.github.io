# Neural image classification

Neural image classification

## Usage

``` r
pheno_classify_dl(
  x,
  model,
  classes = NULL,
  device = c("auto", "cpu", "cuda"),
  image_id = NULL,
  qc = TRUE
)
```

## Arguments

- x:

  RGB array or gray matrix.

- model:

  Torch model, registered record/fit, model id, or function.

- classes:

  Optional class labels.

- device:

  `"auto"`, `"cpu"`, or `"cuda"`.

- image_id:

  Optional image identifier.

- qc:

  Include image QC.

## Value

A `pheno_prediction`.

## Examples

``` r
img <- pheno_data("leaf_rgb")
fun <- function(z) c(healthy = .25, diseased = .75)
c1 <- pheno_classify_dl(img, fun); c1$prediction
#> [1] "diseased"
c2 <- pheno_classify_dl(img, fun, classes = c("healthy", "diseased"), qc = FALSE)
if (requireNamespace("torch", quietly = TRUE)) {
  net <- pheno_cnn(3, 2, base_channels = 4)
  c3 <- pheno_classify_dl(img, net, classes = c("healthy", "diseased"), device = "cpu")
}
```
