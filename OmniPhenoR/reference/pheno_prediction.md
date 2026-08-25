# Create a phenotyping prediction object

Create a phenotyping prediction object

## Usage

``` r
pheno_prediction(
  prediction,
  probability = NULL,
  uncertainty = NULL,
  model_id = NULL,
  model_hash = NULL,
  classes = NULL,
  threshold = NULL,
  engine = "native",
  device = "cpu",
  preprocessing = NULL,
  image_id = NULL,
  acquisition_id = NULL,
  qc = NULL,
  runtime_sec = NULL,
  provenance = NULL
)
```

## Arguments

- prediction:

  Predicted class, mask, or quantitative output.

- probability:

  Probability vector, matrix, or H x W x class array.

- uncertainty:

  Optional uncertainty result.

- model_id:

  Model identifier.

- model_hash:

  Optional model/weights SHA-256 hash.

- classes:

  Class labels.

- threshold:

  Decision threshold.

- engine:

  Inference engine.

- device:

  Compute device.

- preprocessing:

  Named preprocessing specification.

- image_id:

  Optional image identifier.

- acquisition_id:

  Optional acquisition identifier.

- qc:

  Optional quality-control result.

- runtime_sec:

  Optional inference runtime.

- provenance:

  Optional named provenance list.

## Value

A `pheno_prediction` object.

## Examples

``` r
p1 <- pheno_prediction(matrix(TRUE, 3, 3), probability = matrix(.9, 3, 3),
  model_id = "demo", classes = c("background", "leaf"), threshold = .5)
p2 <- pheno_prediction("healthy", probability = c(healthy = .8, diseased = .2),
  model_id = "classifier")
p3 <- pheno_prediction(c("leaf", "lesion"), engine = "native", provenance = list(seed = 1))
```
