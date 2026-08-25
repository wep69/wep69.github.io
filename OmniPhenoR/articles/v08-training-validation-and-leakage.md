# OmniPhenoR Training, Validation, and Leakage Control

## 1. Purpose

The most important machine-learning decision in a phenotyping study is
often not the architecture. It is the definition of an independent
evaluation unit. A model can appear exceptionally accurate when images,
plants, plots, dates, or locations that share information are allowed to
cross data partitions.

Data leakage has been documented as a widespread problem in
machine-learning-based science ([Kapoor and Narayanan
2023](#ref-Kapoor2023_Leakage)). Version 0.2.0 therefore treats
biological grouping as part of the dataset object and exposes a
group-aware splitting function before training.

## 2. Start from the deployment question

A split should mimic the kind of generalization claimed by the study.

| Intended claim                   | Candidate grouping or holdout         |
|:---------------------------------|:--------------------------------------|
| New image of the same individual | acquisition/image group, with caution |
| New plants under same protocol   | plant ID                              |
| New plots                        | plot ID                               |
| New genotype                     | genotype                              |
| New environment                  | environment/location                  |
| New season/date                  | acquisition date/season               |
| New sensor                       | sensor or acquisition protocol        |

A random image split is valid only when image-level independence is
scientifically defensible.

## 3. Teaching dataset with repeated observations

``` r

img <- pheno_data("leaf_rgb")
images <- replicate(24, img, simplify=FALSE)

meta <- data.frame(
  plant_id = rep(paste0("P", sprintf("%02d", 1:12)), each=2),
  genotype = rep(rep(c("G1", "G2", "G3"), each=4), each=2),
  environment = rep(c("E1", "E2"), each=12),
  date = rep(c("D1", "D2"), 12)
)
labels <- rep(c("healthy", "diseased"), 12)

ds <- pheno_dl_dataset(
  images=images,
  targets=labels,
  groups=meta$plant_id,
  task="classification",
  metadata=meta
)
ds
#> <pheno_dl_dataset>
#>   task: classification 
#>   images: 24 
#>   groups: 12 
#>   targets: yes
```

The duplicated teaching image is not a biological dataset. It is used
only to make the grouping logic inspectable.

## 4. Plant-level split

``` r

sp_plant <- pheno_dl_split(
  ds,
  proportions=c(train=.60, validation=.20, test=.20),
  seed=120
)
sp_plant
#> <pheno_split> group-aware
#>   train: 14 rows; 7 groups
#>   validation: 6 rows; 3 groups
#>   test: 4 rows; 2 groups

length(intersect(sp_plant$groups$train, sp_plant$groups$test))
#> [1] 0
```

A zero intersection verifies the software rule. It does not prove that
plant-level splitting is sufficient for the scientific claim.

## 5. Genotype-level split

To test transfer to unseen genotypes, supply genotype as the grouping
vector.

``` r

sp_genotype <- pheno_dl_split(
  ds,
  group=meta$genotype,
  proportions=c(train=.50, validation=.25, test=.25),
  seed=121
)
sp_genotype$groups
#> $train
#> [1] "G3"
#> 
#> $validation
#> [1] "G2"
#> 
#> $test
#> [1] "G1"
```

With only three genotypes, a split is necessarily coarse. This
illustrates a practical limitation: a study cannot claim broad genotype
generalization when only a tiny number of independent genotypes exist.

## 6. Environment-level holdout

A two-environment dataset is better handled by a deliberate external
holdout than by a three-way random split. For example, train/tune in E1
and reserve E2 for a final domain-shift test. The exact strategy depends
on whether the intended use is within-environment or cross-environment
prediction.

``` r

table(meta$environment, meta$plant_id)
#>     
#>      P01 P02 P03 P04 P05 P06 P07 P08 P09 P10 P11 P12
#>   E1   2   2   2   2   2   2   0   0   0   0   0   0
#>   E2   0   0   0   0   0   0   2   2   2   2   2   2
```

## 7. Three partitions have different jobs

### Training set

Used to estimate model parameters.

### Validation set

Used to choose architecture, epoch rule, augmentation, probability
threshold, feature settings, or other tuning decisions.

### Test set

Used once the pipeline is frozen to estimate final performance.
Repeatedly looking at the test set while changing the model converts it
into another validation set.

## 8. Augmentation belongs only to training

Augmentation should be applied to training observations, not used to
manufacture additional independent test samples. The original biological
unit still determines independence.

``` r

a <- pheno_dl_augment(
  img,
  operations=c("flip_h", "flip_v", "rotate90"),
  probability=.7,
  seed=2026
)
a$log
#>   operation applied      detail
#> 1    flip_h    TRUE            
#> 2    flip_v    TRUE            
#> 3  rotate90    TRUE 180 degrees
```

Data augmentation can improve model training ([Shorten and Khoshgoftaar
2019](#ref-Shorten2019_Augmentation)), but it does not increase the
number of independent plants or plots in an inferential sense.

## 9. Color augmentation requires a scientific decision

``` r

err <- try(
  pheno_dl_augment(img, operations="brightness", probability=1),
  silent=TRUE
)
class(err)
#> [1] "try-error"
```

The operation is rejected because `phenotype_preserving=TRUE` by
default. A structural task may explicitly relax that rule.

``` r

a2 <- pheno_dl_augment(
  img,
  operations=c("brightness", "noise"),
  probability=.5,
  phenotype_preserving=FALSE,
  seed=2027
)
a2$log
#>    operation applied       detail
#> 1 brightness    TRUE 0.979901....
#> 2      noise    TRUE 0.014051....
```

For chlorosis, pigmentation, senescence, and disease-color phenotypes,
photometric augmentation can remove or distort the target signal. The
augmentation policy should therefore be reported as part of the
measurement method.

## 10. Segmentation training example

``` r

mask <- pheno_data("leaf_mask")
seg_ds <- pheno_dl_dataset(
  replicate(8, img, simplify=FALSE),
  replicate(8, mask, simplify=FALSE),
  groups=paste0("plant_", 1:8),
  task="semantic_segmentation"
)
seg_sp <- pheno_dl_split(seg_ds, seed=88)
seg_sp
#> <pheno_split> group-aware
#>   train: 6 rows; 6 groups
#>   validation: 1 rows; 1 groups
#>   test: 1 rows; 1 groups
```

A compact training run is shown but not executed during vignette
building because `torch` is optional.

``` r

fit <- pheno_train(
  seg_ds,
  split=seg_sp,
  architecture="unet_small",
  epochs=25,
  base_channels=8,
  learning_rate=1e-3,
  augmentation=list(
    operations=c("flip_h", "flip_v", "rotate90"),
    probability=.5
  ),
  device="auto",
  seed=88,
  model_id="leaf_segmentation_v1"
)
```

## 11. Classification training example

``` r

cls_fit <- pheno_train(
  ds,
  split=sp_plant,
  architecture="cnn_small",
  classes=c("healthy", "diseased"),
  epochs=25,
  base_channels=8,
  dropout=.2,
  device="auto",
  seed=120,
  model_id="leaf_classifier_v1"
)
```

## 12. Seeds are necessary but not sufficient

A fixed seed makes stochastic operations more reproducible. It does not
solve sampling bias, leakage, domain shift, mislabeled images, or
underpowered evaluation.

The fit object records the seed and the split object. A complete archive
should also retain package versions, model weights, optimizer settings,
augmentation policy, data manifest, and exact source code.

## 13. Stratification and grouping

[`pheno_dl_split()`](https://wep69.github.io/OmniPhenoR/reference/pheno_dl_split.md)
accepts optional stratification, but grouping takes priority. The goal
is to keep whole biological groups together. If group-level class
composition is highly unbalanced, the analyst may need a study-specific
allocation strategy rather than forcing observation-level stratification
that breaks independence.

``` r

sp_strat <- pheno_dl_split(
  ds,
  proportions=c(.6,.2,.2),
  seed=77,
  stratify=labels
)
sp_strat
#> <pheno_split> group-aware
#>   train: 14 rows; 7 groups
#>   validation: 6 rows; 3 groups
#>   test: 4 rows; 2 groups
```

Always inspect the actual group and class counts after splitting.

## 14. Leakage can enter before model fitting

Common pathways include:

1.  Normalizing all images with statistics computed from train plus test
    data.
2.  Selecting RGB indices or texture parameters using the full dataset.
3.  Creating augmented copies before splitting, then sending related
    copies to different partitions.
4.  Cropping several patches from one image and splitting patches
    independently.
5.  Using treatment labels indirectly in preprocessing decisions.
6.  Manually removing difficult test images after seeing predictions.
7.  Tuning a probability threshold on the final test set.
8.  Selecting the best model after repeated test-set comparison.

The correct separation must cover preprocessing, feature selection,
tuning, and evaluation, not only the neural-network fit call.

## 15. Patch and tile leakage

Large images are often divided into tiles. Neighboring tiles overlap
spatially and may share nearly identical soil, canopy, illumination, and
plants. Random tile splitting can therefore be severely optimistic.

``` r

tt <- pheno_tile(img, tile_size=c(48,64), overlap=8)
head(tt$index)
#> # A tibble: 6 × 7
#>    tile row_start row_end col_start col_end valid_height valid_width
#>   <int>     <int>   <int>     <int>   <int>        <int>       <int>
#> 1     1         1      48         1      64           48          64
#> 2     2         1      48        57     120           48          64
#> 3     3         1      48        65     128           48          64
#> 4     4        41      88         1      64           48          64
#> 5     5        41      88        57     120           48          64
#> 6     6        41      88        65     128           48          64
```

For field imagery, split by plot, field, flight, or another appropriate
spatial/acquisition unit before deriving tiles for model training.

## 16. Model selection and the final phenotype

Segmentation models should not be selected only on pixel overlap. If the
final scientific outcome is leaf area or disease severity, compare
models on phenotype-level error as well.

``` r

truth <- pheno_data("leaf_mask")
classical <- pheno_segment(img, "ExG")
perfect <- pheno_prediction(truth)
pheno_compare_segmentation(
  truth,
  classical=classical,
  ideal=perfect,
  phenotype_fun=mean
)
#> # A tibble: 2 × 17
#>   method     tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 classi…  2872  9416     0     0     1     1         1      1           1     1
#> 2 ideal    2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 6 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>, phenotype_estimate <dbl>,
#> #   phenotype_truth <dbl>, phenotype_bias <dbl>
```

A model with slightly better Dice but worse systematic area bias may be
less useful for the intended study.

## 17. Cross-validation is not always the answer

Cross-validation can improve efficiency when independent groups are
limited, but folds must be constructed at the same biological level.
Grouped cross-validation does not replace an external test when the
scientific claim concerns new environments, sensors, seasons, or
populations not represented in development data.

## 18. Repeated images and longitudinal data

When the same plant is photographed over time, a random image split can
teach the model plant-specific appearance. Depending on the claim, hold
out entire plants or entire temporal sequences. If prediction to future
dates is the goal, a forward-in-time evaluation can be more meaningful
than random folds.

## 19. Quality control before splitting

Acquisition QC should be defined without seeing outcome-dependent model
performance. If images are excluded for blur, saturation, missing
tissue, or file corruption, specify objective criteria and record
exclusions.

``` r

pheno_qc(img)
#> # A tibble: 1 × 12
#>   height width finite_fraction missing_fraction    min   max dynamic_range  mean
#>    <int> <int>           <dbl>            <dbl>  <dbl> <dbl>         <dbl> <dbl>
#> 1     96   128               1                0 0.0769  0.86         0.783 0.649
#> # ℹ 4 more variables: sd <dbl>, low_saturation_fraction <dbl>,
#> #   high_saturation_fraction <dbl>, gradient_energy <dbl>
```

## 20. A leakage audit worksheet

For each workflow, answer:

- What is the independent biological unit?
- Which observations share plants, plots, dates, sensors, or spatial
  neighborhoods?
- Which steps estimate parameters from data?
- Were those parameters estimated from training data only?
- When were augmentations generated?
- How were thresholds selected?
- How many times was the test set inspected?
- Does the external evaluation match the claimed deployment domain?

These questions align with the broader motivation for model information
sheets in leakage-aware scientific ML ([Kapoor and Narayanan
2023](#ref-Kapoor2023_Leakage)).

## 21. Frozen software validation

OmniPhenoR includes an implementation-level leakage check.

``` r

subset(pheno_validate("ml"), grepl("group split", check))
#> # A tibble: 1 × 6
#>   domain check                        estimate target tolerance pass 
#>   <chr>  <chr>                           <dbl>  <dbl>     <dbl> <lgl>
#> 1 ml     group split has zero leakage        0      0         0 TRUE
```

This confirms that the package’s grouped split does not place the same
stored group in more than one partition. It cannot decide whether the
user selected the correct grouping variable.

## 22. Reporting checklist

Report total images and independent biological groups; repeated-measure
structure; exact train/validation/test grouping; class balance by
partition; preprocessing fit scope; augmentation timing and policy;
feature/threshold selection data; number of tuning rounds; stopping
rule; final untouched test definition; external validation domain;
seeds; model hash; software versions; and all exclusions.

## 23. Final perspective

Leakage control is part of measurement validity. A sophisticated network
evaluated on non-independent observations can be less informative than a
simple model evaluated on a genuinely independent biological test.
OmniPhenoR therefore places split structure before architecture in the
workflow.

## References

Kapoor, Sayash, and Arvind Narayanan. 2023. “Leakage and the
Reproducibility Crisis in Machine-Learning-Based Science.” *Patterns* 4
(9): 100804. <https://doi.org/10.1016/j.patter.2023.100804>.

Shorten, Connor, and Taghi M. Khoshgoftaar. 2019. “A Survey on Image
Data Augmentation for Deep Learning.” *Journal of Big Data* 6 (1): 60.
<https://doi.org/10.1186/s40537-019-0197-0>.
