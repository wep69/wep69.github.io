# OmniPhenoR Deep-Learning Foundations for Plant Phenotyping

## 1. Purpose

OmniPhenoR 0.2.0 adds a learning-enabled layer without replacing the
transparent image-processing core introduced in 0.1.0. The aim is not to
make every plant image problem a deep-learning problem. The aim is to
let a researcher move from classical segmentation to a trained model
while preserving the same experimental identities, validation logic,
output objects, uncertainty records, and downstream phenotyping
functions.

The guiding sequence is:

**biological identity -\> grouped data split -\> augmentation policy -\>
model -\> probability -\> decision -\> phenotype -\> independent
validation -\> audit.**

The package uses the R `torch` ecosystem when a neural model is
required. `torch` and `torchvision` are optional dependencies. Classical
RGB, morphology, disease, texture, spatial, QC, and reporting functions
remain available without them.

## 2. Why a model is not only a weights file

A file of learned weights cannot, by itself, establish what a model
means. A scientific model record should identify the task, architecture,
class labels, preprocessing, training population, split strategy, random
seed, software version, validation metrics, license, citation, and exact
weights whenever possible.

``` r

pheno_model_registry()
#> # A tibble: 0 × 6
#> # ℹ 6 variables: model_id <chr>, task <chr>, architecture <chr>, engine <chr>,
#> #   engine_version <chr>, weights_hash <chr>
pheno_model_capabilities()
#> # A tibble: 6 × 4
#>   capability     engine      available status       
#>   <chr>          <chr>       <lgl>     <chr>        
#> 1 torch          torch       TRUE      available    
#> 2 torchvision    torchvision TRUE      available    
#> 3 cuda           torch       FALSE     not available
#> 4 unet_small     torch       TRUE      available    
#> 5 cnn_small      torch       TRUE      available    
#> 6 function_model native      TRUE      available
```

The second call distinguishes installation from scientific validation.
If `torch` is installed, the capability is available. That does not
imply that an untrained U-Net or CNN is useful for a crop, disease,
camera, or field condition.

## 3. Device resolution

``` r

pheno_device("auto")
#> [1] "cpu"
pheno_device("cpu")
#> [1] "cpu"
```

`pheno_device("auto")` chooses CUDA only when the R `torch` runtime
reports that CUDA is available. Otherwise it returns `"cpu"`. An
explicit request for CUDA fails rather than silently switching to CPU.
This makes compute provenance visible.

## 4. Create an image-level dataset before splitting

Repeated pixels from the same image are not independent training
samples. Repeated images from the same plant may also be correlated.
OmniPhenoR therefore creates a dataset at the image level and stores a
biological grouping variable before any split.

``` r

img <- pheno_data("leaf_rgb")
mask <- pheno_data("leaf_mask")

images <- replicate(8, img, simplify=FALSE)
masks <- replicate(8, mask, simplify=FALSE)
plant_id <- rep(paste0("plant_", 1:4), each=2)

ds <- pheno_dl_dataset(
  images = images,
  targets = masks,
  ids = paste0("image_", seq_along(images)),
  groups = plant_id,
  task = "semantic_segmentation",
  metadata = data.frame(
    treatment = rep(c("control", "stress"), each=4),
    acquisition = rep(c("day_1", "day_2"), 4)
  )
)

ds
#> <pheno_dl_dataset>
#>   task: semantic_segmentation 
#>   images: 8 
#>   groups: 4 
#>   targets: yes
```

The important part is not that the teaching images are duplicated. They
are synthetic examples used to expose the data structure. In a real
study, `groups` should identify the unit whose information must not leak
between training, tuning, and final evaluation.

## 5. Group-aware splitting

Data leakage is a major source of overoptimistic machine-learning
results ([Kapoor and Narayanan 2023](#ref-Kapoor2023_Leakage)). For
plant phenotyping, leakage can occur when images from the same plant,
plot, genotype, pot, acquisition session, or highly overlapping field
region are placed in both training and evaluation sets.

``` r

sp <- pheno_dl_split(
  ds,
  proportions = c(train=.50, validation=.25, test=.25),
  seed = 2026
)
sp
#> <pheno_split> group-aware
#>   train: 4 rows; 2 groups
#>   validation: 2 rows; 1 groups
#>   test: 2 rows; 1 groups

intersect(sp$groups$train, sp$groups$validation)
#> character(0)
intersect(sp$groups$train, sp$groups$test)
#> character(0)
intersect(sp$groups$validation, sp$groups$test)
#> character(0)
```

All three intersections should be empty. The user should choose the
grouping level from the scientific deployment question. If the intended
model will be used on new plants of the same genotype, `plant_id` may be
suitable. If it will be used on unseen genotypes, genotype-level
grouping is more informative. If it must generalize to another date or
environment, the split should test that domain shift explicitly.

## 6. Augmentation is part of the measurement model

Image augmentation can reduce overfitting and increase invariance, but a
transformation can also destroy the phenotype of interest ([Shorten and
Khoshgoftaar 2019](#ref-Shorten2019_Augmentation)). A rotation may be
acceptable for organ detection but inappropriate if leaf orientation
itself is the trait. Strong color changes may be useful for structural
recognition but scientifically unacceptable when the target is chlorosis
or visible color.

``` r

aug <- pheno_dl_augment(
  img,
  target = mask,
  operations = c("flip_h", "rotate90"),
  probability = 1,
  seed = 13
)

aug$log
#>   operation applied     detail
#> 1    flip_h    TRUE           
#> 2  rotate90    TRUE 90 degrees
identical(dim(aug$image)[1:2], dim(aug$target))
#> [1] TRUE
```

Photometric augmentation requires an explicit acknowledgement that the
transformation may change a color phenotype.

``` r

aug_color <- pheno_dl_augment(
  img,
  operations = "brightness",
  probability = 1,
  phenotype_preserving = FALSE,
  seed = 14
)
aug_color$log
#>    operation applied       detail
#> 1 brightness    TRUE 1.027565....
```

This deliberate friction is useful. It prevents a color-changing
augmentation from being added accidentally to a pipeline whose outcome
is itself based on color.

## 7. Reference architectures

U-Net introduced the contracting-path/expanding-path architecture that
became a major reference for dense image segmentation ([Ronneberger et
al. 2015](#ref-Ronneberger2015_UNet)). OmniPhenoR supplies a compact
U-Net-like model for teaching, testing, and small phenotyping studies.

``` r

net_seg <- pheno_unet(
  in_channels = 3,
  out_channels = 2,
  base_channels = 8,
  dropout = 0.1
)
net_seg
```

The package also provides a compact CNN classifier.

``` r

net_cls <- pheno_cnn(
  in_channels = 3,
  classes = 2,
  base_channels = 8,
  dropout = 0.2
)
net_cls
```

These are reference architectures, not claims of best performance. A
scientifically mature study should compare them with transparent
classical baselines and, when justified, with stronger external
architectures.

## 8. Prediction can be tested without a neural runtime

The prediction API accepts functions that return probabilities. This is
useful for unit testing, for demonstrating the interface, and later for
adapters to external backends.

``` r

mock_leaf_probability <- function(z) {
  exg <- pheno_rgb_indices(z, "ExG")[, , 1]
  plogis(20 * (exg - median(exg)))
}

pred <- pheno_segment_dl(
  img,
  model = mock_leaf_probability,
  classes = c("background", "leaf"),
  threshold = 0.5
)

pred
#> <pheno_prediction>
#>   engine: function  device: cpu 
#>   classes: background, leaf 
#>   mask dimensions: 96 x 128 
#>   runtime: 0.02 s
range(pred$probability)
#> [1] 1.28253e-12 1.00000e+00
summary(as.vector(pred$uncertainty))
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>  0.0000  1.0000  1.0000  0.7725  1.0000  1.0000
```

The result retains the probability field, not only a hard mask. This
distinction becomes important when the model is uncertain at lesion
borders, overlapping organs, soil-vegetation transitions, or unusual
symptoms.

## 9. Unified prediction grammar

``` r

p1 <- pheno_predict(
  img,
  model = function(z) c(healthy=.3, diseased=.7),
  task = "classification",
  classes = c("healthy", "diseased")
)
p1$prediction
#> [1] "diseased"
p1$probability
#>  healthy diseased 
#>      0.3      0.7

p2 <- pheno_predict(
  img,
  model = mock_leaf_probability,
  task = "semantic_segmentation",
  classes = c("background", "leaf")
)
p2$engine
#> [1] "function"
```

This grammar allows a future model backend to change without forcing
downstream morphology, disease, uncertainty, or reporting code to be
rewritten.

## 10. Register a model with scientific metadata

``` r

rec <- pheno_model_register(
  model_id = "soybean_leaf_demo_v1",
  task = "semantic_segmentation",
  architecture = "unet_small",
  classes = c("background", "leaf"),
  input_size = c(256, 256),
  normalization = list(scale="unit_rgb"),
  training_dataset = "synthetic teaching example",
  engine = "torch",
  seed = 2026,
  device = "cpu",
  loss = "cross_entropy",
  optimizer = "Adam",
  augmentation = list(operations=c("flip_h", "rotate90")),
  validation_metrics = list(dice=NA_real_),
  license = "teaching example only",
  citation = "not applicable",
  provenance = list(split_by="plant_id"),
  overwrite = TRUE
)
rec
#> <pheno_model_record> soybean_leaf_demo_v1 
#>   task: semantic_segmentation 
#>   architecture: unet_small 
#>   engine: torch  0.17.0 
#>   classes: background, leaf
pheno_model_registry("segment")
#> # A tibble: 1 × 6
#>   model_id             task      architecture engine engine_version weights_hash
#>   <chr>                <chr>     <chr>        <chr>  <chr>          <chr>       
#> 1 soybean_leaf_demo_v1 semantic… unet_small   torch  0.17.0         NA
```

A real trained model should additionally record or hash the exact
weights and document the population on which validation was performed.

## 11. Save and reload metadata

``` r

f <- tempfile(fileext=".rds")
pheno_model_save(rec, f, include_model=FALSE)
loaded <- pheno_model_load(f, register=FALSE)
loaded$model_id
#> [1] "soybean_leaf_demo_v1"
loaded$provenance
#> $split_by
#> [1] "plant_id"
#> 
#> $registered
#> [1] "2026-08-25 01:06:16 UTC"
#> 
#> $OmniPhenoR
#> [1] "1.0.0"
unlink(f)
```

For long-term scientific archives, store the model record together with
the weights, release tarball, training code, data manifest, and
validation report.

## 12. Reference training loop

[`pheno_train()`](https://wep69.github.io/OmniPhenoR/reference/pheno_train.md)
implements a transparent R `torch` reference trainer. It uses the
group-aware split, fixed seeds, cross-entropy loss, Adam optimization,
and one image per optimization step. It is intentionally readable rather
than a replacement for all high-throughput training systems.

``` r

fit <- pheno_train(
  ds,
  split = sp,
  architecture = "unet_small",
  epochs = 20,
  learning_rate = 1e-3,
  base_channels = 8,
  dropout = 0.1,
  augmentation = list(
    operations = c("flip_h", "flip_v", "rotate90"),
    probability = 0.5
  ),
  device = "auto",
  seed = 2026,
  model_id = "soybean_leaf_unet_2026"
)
fit$history
```

The final test set should not be used to select epochs, architecture,
augmentation, or thresholds. Those decisions belong to training and
validation data. The clean test set is for final evaluation.

## 13. Three distinct use cases

### 13.1 Leaf-background segmentation

Use semantic segmentation when each pixel should be assigned to leaf or
background. Evaluate with overlap metrics and phenotype error.

### 13.2 Disease classification

Use image classification only when the scientific question is genuinely
categorical, such as whether an organ is symptomatic. Do not replace
quantitative lesion area with an arbitrary class unless the study
question supports that abstraction.

### 13.3 Disease segmentation

For severity, a multiclass segmentation model can distinguish
background, healthy tissue, and lesion. This allows severity to be
computed from explicit areas and retains spatial information.

## 14. Common mistakes

1.  Randomly splitting pixels from the same image into training and test
    sets.
2.  Splitting repeated images from one plant across data partitions.
3.  Applying strong color jitter while claiming to quantify color stress
    responses.
4.  Reporting a model file without class order, preprocessing, or
    software version.
5.  Treating an available GPU as evidence that a neural model is
    scientifically necessary.
6.  Reporting only accuracy for strongly imbalanced classes.
7.  Optimizing on the final test set.
8.  Converting probabilities to masks without retaining the threshold.
9.  Comparing a tuned neural model with an untuned or inappropriate
    classical baseline.
10. Calling a model generalizable after testing only images from the
    same acquisition session.

## 15. Reporting checklist

For a learning-enabled phenotyping method, report the biological
grouping used for splitting, number of independent groups in each
partition, class definitions, preprocessing, augmentation, architecture,
seed, optimizer/loss, stopping or epoch rule, threshold rule,
probability calibration diagnostics when probabilities are interpreted,
final held-out metrics, phenotype-level error, software/runtime
versions, device, model/weights hash, and limits of the validation
population.

## 16. Final perspective

Deep learning enters OmniPhenoR as one measurement engine within a
larger phenotyping chain. The model is useful only when its data split
matches the intended biological generalization, its transformations do
not erase the target phenotype, its probabilities and decisions are
auditable, and its outputs improve or stabilize the final phenotype on
independent evidence.

## References

Kapoor, Sayash, and Arvind Narayanan. 2023. “Leakage and the
Reproducibility Crisis in Machine-Learning-Based Science.” *Patterns* 4
(9): 100804. <https://doi.org/10.1016/j.patter.2023.100804>.

Ronneberger, Olaf, Philipp Fischer, and Thomas Brox. 2015. “U-Net:
Convolutional Networks for Biomedical Image Segmentation.” *Medical
Image Computing and Computer-Assisted Intervention – MICCAI 2015*,
Lecture notes in computer science, vol. 9351: 234–41.
<https://doi.org/10.1007/978-3-319-24574-4_28>.

Shorten, Connor, and Taghi M. Khoshgoftaar. 2019. “A Survey on Image
Data Augmentation for Deep Learning.” *Journal of Big Data* 6 (1): 60.
<https://doi.org/10.1186/s40537-019-0197-0>.
