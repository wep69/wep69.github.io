# Train a compact R torch phenotyping model

Provides a deliberately transparent reference trainer for small and
medium phenotyping studies. The function uses group-aware splitting, one
image per optimization step, explicit seeds, and recorded metadata. For
large-scale training, users can build custom torch data loaders while
preserving the model-record and prediction interfaces.

## Usage

``` r
pheno_train(
  data,
  split = NULL,
  architecture = c("unet_small", "cnn_small"),
  model = NULL,
  classes = NULL,
  epochs = 10L,
  learning_rate = 0.001,
  base_channels = 16L,
  dropout = 0.1,
  augmentation = NULL,
  device = c("auto", "cpu", "cuda"),
  seed = 123,
  model_id = NULL,
  register = TRUE,
  verbose = interactive()
)
```

## Arguments

- data:

  A `pheno_dl_dataset` with targets.

- split:

  Optional `pheno_split`. When omitted, `pheno_dl_split(data)` is used.

- architecture:

  `"unet_small"` for semantic segmentation or `"cnn_small"` for
  classification.

- model:

  Optional preconstructed torch module.

- classes:

  Optional class labels.

- epochs:

  Number of epochs.

- learning_rate:

  Adam learning rate.

- base_channels:

  Reference architecture width.

- dropout:

  Dropout probability.

- augmentation:

  Optional character operations or named arguments passed to
  [`pheno_dl_augment()`](https://wep69.github.io/OmniPhenoR/reference/pheno_dl_augment.md);
  photometric augmentation must explicitly set
  `phenotype_preserving = FALSE`.

- device:

  `"auto"`, `"cpu"`, or `"cuda"`.

- seed:

  Reproducibility seed.

- model_id:

  Identifier for the trained fit.

- register:

  Register the fit and metadata in the session registry.

- verbose:

  Print epoch summaries.

## Value

A `pheno_model_fit` containing the torch model, learning history, split,
classes, and provenance.

## Examples

``` r
if (requireNamespace("torch", quietly = TRUE)) {
  imgs <- replicate(4, pheno_data("leaf_rgb"), simplify = FALSE)
  masks <- replicate(4, pheno_data("leaf_mask"), simplify = FALSE)
  ds <- pheno_dl_dataset(imgs, masks, groups = paste0("plant", 1:4))
  sp <- pheno_dl_split(ds, proportions = c(.5,.25,.25), seed = 1)
  # A one-epoch example is intentionally not run during package checks.
}
if (FALSE) { # \dontrun{
fit <- pheno_train(ds, sp, epochs = 1, base_channels = 4, device = "cpu")
fit$history
} # }
```
