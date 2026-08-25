# OmniPhenoR Model Registry, Serialization, and Provenance

## 1. Purpose

A scientific model is more than architecture plus weights. Reproducing
an image prediction requires the class order, preprocessing, input size,
training population, split rule, seed, software engine, model version,
and often the exact weights file. OmniPhenoR 0.2.0 formalizes that
information through an in-session registry and serializable model
records.

## 2. Register a semantic-segmentation model

``` r

seg <- pheno_model_register(
  model_id="maize_canopy_unet_v1",
  task="semantic_segmentation",
  architecture="unet_small",
  classes=c("soil", "vegetation"),
  input_size=c(512,512),
  normalization=list(scale="unit_rgb"),
  training_dataset="2026 teaching metadata example",
  engine="torch",
  seed=901,
  device="cpu",
  loss="cross_entropy",
  optimizer="Adam",
  augmentation=list(operations=c("flip_h","rotate90")),
  validation_metrics=list(dice=.91, iou=.84),
  license="example only",
  citation="example only",
  provenance=list(split_by="plot_id", external_test="field_B"),
  overwrite=TRUE
)
seg
#> <pheno_model_record> maize_canopy_unet_v1 
#>   task: semantic_segmentation 
#>   architecture: unet_small 
#>   engine: torch  0.17.0 
#>   classes: soil, vegetation
```

## 3. Inspect the registry

``` r

pheno_model_registry()
#> # A tibble: 1 × 6
#>   model_id             task      architecture engine engine_version weights_hash
#>   <chr>                <chr>     <chr>        <chr>  <chr>          <chr>       
#> 1 maize_canopy_unet_v1 semantic… unet_small   torch  0.17.0         NA
pheno_model_registry("segment")
#> # A tibble: 1 × 6
#>   model_id             task      architecture engine engine_version weights_hash
#>   <chr>                <chr>     <chr>        <chr>  <chr>          <chr>       
#> 1 maize_canopy_unet_v1 semantic… unet_small   torch  0.17.0         NA
pheno_model_info("maize_canopy_unet_v1")
#> <pheno_model_record> maize_canopy_unet_v1 
#>   task: semantic_segmentation 
#>   architecture: unet_small 
#>   engine: torch  0.17.0 
#>   classes: soil, vegetation
```

The registry is session-local by design. Permanent records should be
saved with the study archive or reconstructed from versioned metadata
files.

## 4. Classification record

``` r

pheno_model_register(
  "bean_symptom_cnn_v2",
  task="classification",
  architecture="cnn_small",
  classes=c("healthy", "chlorotic", "necrotic"),
  input_size=c(256,256),
  engine="torch",
  seed=902,
  loss="cross_entropy",
  optimizer="Adam",
  provenance=list(split_by="plant_id"),
  overwrite=TRUE
)
pheno_model_registry("class")
#> # A tibble: 1 × 6
#>   model_id            task       architecture engine engine_version weights_hash
#>   <chr>               <chr>      <chr>        <chr>  <chr>          <chr>       
#> 1 bean_symptom_cnn_v2 classific… cnn_small    torch  0.17.0         NA
```

## 5. Weights hashes

When `weights_path` points to an existing file,
[`pheno_model_register()`](https://wep69.github.io/OmniPhenoR/reference/pheno_model_register.md)
stores a SHA-256 hash. The hash identifies the exact bytes, not merely a
file name.

``` r

f <- tempfile(fileext=".bin")
writeBin(as.raw(1:20), f)
with_hash <- pheno_model_register(
  "hash_demo",
  task="classification",
  architecture="external",
  classes=c("A","B"),
  weights_path=f,
  provenance=list(note="synthetic bytes for documentation"),
  overwrite=TRUE
)
with_hash$weights_hash
#> [1] "e12f08743344c0eab7afaa22bb71e2725f7e13dece6ac2d23711054ee787b6c2"
unlink(f)
```

Changing a weights file invalidates its old hash. A release manuscript
or supplementary record should therefore report the hash of the
distributed weights, not a hash generated before final training.

## 6. Save a lightweight record

``` r

f <- tempfile(fileext=".rds")
pheno_model_save(seg, f, include_model=FALSE)
loaded <- pheno_model_load(f, register=FALSE)
loaded
#> <pheno_model_record> maize_canopy_unet_v1 
#>   task: semantic_segmentation 
#>   architecture: unet_small 
#>   engine: torch  0.17.0 
#>   classes: soil, vegetation
unlink(f)
```

Metadata-only records are useful for reports, registries, and archives
where the actual weights are stored separately.

## 7. Save an in-memory model

``` r

net <- pheno_unet(3,2,base_channels=8)
rec <- pheno_model_register(
  "leaf_unet_live",
  "semantic_segmentation",
  "unet_small",
  classes=c("background","leaf"),
  engine="torch",
  model=net,
  overwrite=TRUE
)
pheno_model_save(rec, "leaf_unet_live.rds", include_model=TRUE)
```

Long-term neural-weight storage can be engine-specific. The scientific
archive should therefore include both a model metadata record and the
engine-native or RDS model artifact used for inference.

## 8. Provenance fields

The model record can preserve:

- `model_id`
- task and architecture
- class order
- input size and normalization
- training dataset description and date
- engine and version
- weights hash
- seed and device
- loss and optimizer
- augmentation policy
- validation metrics
- license and citation
- additional named provenance

This information is intentionally redundant with a good Methods section.
Redundancy is useful because the model object can outlive the
manuscript.

## 9. Model info sheets and leakage

Kapoor and Narayanan emphasize that leakage mitigation requires explicit
justification of model-development decisions ([Kapoor and Narayanan
2023](#ref-Kapoor2023_Leakage)). A registry cannot prove absence of
leakage, but it can make fields such as `split_by`, training population,
and validation set explicit.

``` r

seg$provenance
#> $split_by
#> [1] "plot_id"
#> 
#> $external_test
#> [1] "field_B"
#> 
#> $registered
#> [1] "2026-08-25 01:06:34 UTC"
#> 
#> $OmniPhenoR
#> [1] "1.0.0"
seg$validation_metrics
#> $dice
#> [1] 0.91
#> 
#> $iou
#> [1] 0.84
```

## 10. Model prediction through an identifier

A record can store a function or an R `torch` module during a session.

``` r

pheno_model_register(
  "function_leaf_v1",
  task="semantic_segmentation",
  architecture="function_adapter",
  classes=c("background","leaf"),
  engine="native",
  model=function(z) matrix(.9, dim(z)[1], dim(z)[2]),
  provenance=list(purpose="API demonstration"),
  overwrite=TRUE
)

p <- pheno_predict(
  pheno_data("leaf_rgb"),
  "function_leaf_v1"
)
p$model_id
#> [1] "function_leaf_v1"
p$classes
#> [1] "background" "leaf"
```

This pattern allows the downstream code to refer to a named model rather
than repeatedly reconstructing it.

## 11. Versioning strategy

Do not overwrite a published model identifier when weights or
preprocessing change. Prefer identifiers such as:

``` text
soybean_leaf_unet_v1
soybean_leaf_unet_v2
soybean_leaf_unet_v2_external2027
```

The model ID is human-readable; the weights hash provides byte-level
identity.

## 12. Training provenance

[`pheno_train()`](https://wep69.github.io/OmniPhenoR/reference/pheno_train.md)
returns a `pheno_model_fit` with history, split, classes,
engine/version, device, seed, optimizer/loss, augmentation, and training
timestamp.

``` r

fit <- pheno_train(
  data,
  split=split,
  epochs=30,
  seed=42,
  model_id="leaf_unet_v3"
)
fit$history
fit$provenance
```

If `register=TRUE`, the fit is also represented in the session registry.

## 13. Audit the broader workflow

``` r

a <- pheno_audit()
a$R
#> [1] "R version 4.6.0 (2026-04-24 ucrt)"
a$package
#> [1] "1.0.0"
subset(a$capabilities, grepl("learning|torch|class", capability))
#> # A tibble: 3 × 5
#>   capability           engine      package     available status   
#>   <chr>                <chr>       <chr>       <lgl>     <chr>    
#> 1 deep_learning        torch       torch       TRUE      available
#> 2 image_classification torch       torch       TRUE      available
#> 3 torchvision          torchvision torchvision TRUE      available
```

Model provenance and package provenance are complementary. A weights
hash without the software environment can still be insufficient for
exact reproduction.

## 14. Model cards versus scientific records

A model record should not be confused with a complete model card. A
publication-grade model card can also describe intended use,
out-of-scope use, known failure modes, demographic or biological
population coverage, hardware, environmental limitations, licensing,
ethical constraints, and external validations.

For plant phenotyping, useful biological coverage fields include
crop/species, genotype range, developmental stage, stress/disease range,
acquisition geometry, background, illumination, camera/sensor,
environment, and geography.

## 15. Three examples of provenance failure

### 15.1 Same file name, different bytes

A collaborator retrains `model.pt` and replaces the file. The manuscript
still refers to `model.pt`, but the results can no longer be tied to
exact weights. A SHA-256 hash prevents this ambiguity.

### 15.2 Same weights, different class order

A two-class model outputs channel 1 and channel 2. One script interprets
them as `background, leaf`; another as `leaf, background`. The numeric
weights are identical and the scientific interpretation is inverted.
Store class order.

### 15.3 Same model, different normalization

A CNN trained on unit-scale RGB can give different predictions if fed
byte values. Store input scaling and preprocessing.

## 16. Release checklist for a model

Before distributing a trained model, verify the exact weights file;
calculate SHA-256; freeze preprocessing and class order; record
architecture and engine versions; record training/validation/test
grouping; store seeds; retain training history; report final held-out
metrics; record intended and unsupported use; state license; provide
citation; and test inference from a clean installation.

## 17. Final perspective

The purpose of the registry is traceability. A prediction should be
reconstructable from a named model whose training context, exact
weights, class semantics, and software environment are explicit. This is
especially important when a model is reused months or years after the
original phenotyping study.

## References

Kapoor, Sayash, and Arvind Narayanan. 2023. “Leakage and the
Reproducibility Crisis in Machine-Learning-Based Science.” *Patterns* 4
(9): 100804. <https://doi.org/10.1016/j.patter.2023.100804>.
