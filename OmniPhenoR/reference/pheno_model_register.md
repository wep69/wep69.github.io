# Register a phenotyping model

Adds a model record to OmniPhenoR's in-session registry. The registry
stores scientific provenance together with architecture and task
metadata. A path to model weights is hashed with SHA-256 when supplied.
Registration does not download software or weights.

## Usage

``` r
pheno_model_register(
  model_id,
  task,
  architecture,
  classes = NULL,
  input_size = NULL,
  normalization = NULL,
  training_dataset = NULL,
  training_date = NULL,
  engine = "native",
  engine_version = NULL,
  weights_path = NULL,
  seed = NULL,
  device = NULL,
  loss = NULL,
  optimizer = NULL,
  augmentation = NULL,
  validation_metrics = NULL,
  license = NULL,
  citation = NULL,
  provenance = NULL,
  model = NULL,
  overwrite = FALSE
)
```

## Arguments

- model_id:

  Unique model identifier.

- task:

  Model task, for example `"semantic_segmentation"` or
  `"classification"`.

- architecture:

  Architecture label.

- classes:

  Character class labels.

- input_size:

  Expected input size, usually `c(height, width)`.

- normalization:

  Named normalization specification.

- training_dataset:

  Dataset name or provenance note.

- training_date:

  Training date or timestamp.

- engine:

  Execution engine, usually `"torch"` or `"native"`.

- engine_version:

  Engine version.

- weights_path:

  Optional existing weights file.

- seed:

  Training seed.

- device:

  Training device.

- loss:

  Loss description.

- optimizer:

  Optimizer description.

- augmentation:

  Augmentation specification.

- validation_metrics:

  Named metrics or table.

- license:

  Model/weights license.

- citation:

  Citation or persistent identifier.

- provenance:

  Additional named provenance list.

- model:

  Optional in-memory model object.

- overwrite:

  Replace an existing model identifier.

## Value

A `pheno_model_record` invisibly.

## Examples

``` r
pheno_model_register("leaf_demo", "semantic_segmentation", "unet_small",
  classes = c("background", "leaf"), seed = 123)
pheno_model_register("disease_demo", "classification", "cnn_small",
  classes = c("healthy", "diseased"), engine = "torch", overwrite = TRUE)
rec <- pheno_model_register("canopy_demo", "semantic_segmentation", "external",
  classes = c("soil", "vegetation"), provenance = list(study = "teaching"))
```
