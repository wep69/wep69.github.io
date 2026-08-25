# OmniPhenoR Image Classification: Classes, Probabilities, and Biological Meaning

## 1. Purpose

Image classification assigns one label to an image or region. This can
be scientifically useful for disease presence, organ identity, quality
class, nutrient-deficiency category, developmental stage, or another
truly categorical outcome. It should not be used automatically when the
biological variable is continuous.

For example, classifying a leaf as `high disease` may be operationally
useful, but it discards the quantitative information contained in lesion
area. OmniPhenoR therefore keeps classification separate from morphology
and phytopathometry.

## 2. A probability-first classifier interface

A function model can demonstrate the classification contract without
requiring a trained neural network.

``` r

img <- pheno_data("leaf_rgb")

mock_classifier <- function(z) {
  c(healthy=.20, diseased=.80)
}

pred <- pheno_classify_dl(
  img,
  model=mock_classifier,
  classes=c("healthy", "diseased")
)
pred
#> <pheno_prediction>
#>   engine: function  device: cpu 
#>   classes: healthy, diseased 
#>   runtime: 0 s
pred$prediction
#> [1] "diseased"
pred$probability
#>  healthy diseased 
#>      0.2      0.8
pred$uncertainty
#> [1] 0.7219281
```

The output is a `pheno_prediction`, the same class used for
segmentation. This common object keeps model identity, class labels,
device, probabilities, uncertainty, QC, runtime, and provenance
together.

## 3. Named probabilities can define class order

``` r

pred_named <- pheno_classify_dl(
  img,
  model=function(z) c(control=.65, water_stress=.35),
  qc=FALSE
)
pred_named$classes
#> [1] "control"      "water_stress"
pred_named$probability
#>      control water_stress 
#>         0.65         0.35
```

When a registered model is used, class order should also be stored in
its model record. Class order is essential because a vector such as
`c(.8,.2)` has no biological meaning without labels.

## 4. Compact CNN reference model

``` r

net <- pheno_cnn(
  in_channels=3,
  classes=3,
  base_channels=8,
  dropout=.2
)

p_torch <- pheno_classify_dl(
  img,
  net,
  classes=c("healthy", "chlorotic", "necrotic"),
  device="cpu"
)
```

The architecture is intentionally compact. A random, untrained network
is not a classifier. A valid use requires a labelled image collection, a
grouped split, training, tuning on validation data, and final evaluation
on data not used for model decisions.

## 5. Classification dataset

``` r

images <- replicate(12, img, simplify=FALSE)
labels <- rep(c("healthy", "diseased"), 6)
plants <- rep(paste0("plant_", 1:6), each=2)

ds <- pheno_dl_dataset(
  images,
  targets=labels,
  groups=plants,
  task="classification",
  metadata=data.frame(
    block=rep(1:3, each=4),
    date=rep(c("D1", "D2"), 6)
  )
)
ds
#> <pheno_dl_dataset>
#>   task: classification 
#>   images: 12 
#>   groups: 6 
#>   targets: yes
```

## 6. Split at the biological level

``` r

sp <- pheno_dl_split(
  ds,
  proportions=c(train=.5, validation=.25, test=.25),
  seed=101
)
sp
#> <pheno_split> group-aware
#>   train: 6 rows; 3 groups
#>   validation: 4 rows; 2 groups
#>   test: 2 rows; 1 groups
```

The same plant cannot occur in multiple partitions. More restrictive
grouping may be required if the deployment target is a new genotype,
location, season, camera, or acquisition protocol. Leakage can produce
highly optimistic scientific claims ([Kapoor and Narayanan
2023](#ref-Kapoor2023_Leakage)).

## 7. Reference training

``` r

fit <- pheno_train(
  ds,
  split=sp,
  architecture="cnn_small",
  classes=c("healthy", "diseased"),
  epochs=30,
  base_channels=8,
  dropout=.2,
  learning_rate=1e-3,
  augmentation=list(
    operations=c("flip_h", "flip_v"),
    probability=.5
  ),
  seed=101,
  device="auto",
  model_id="bean_disease_classifier_v1"
)
fit$history
```

The reference trainer is deliberately transparent. Large studies can
implement custom `torch` loaders and training loops, but should retain
the same split discipline, model metadata, and prediction interface.

## 8. Accuracy is not enough

Consider 100 leaves, 95 healthy and 5 diseased. A classifier that always
predicts healthy obtains 95% accuracy and has zero disease sensitivity.
Evaluation should match the scientific costs of false positives and
false negatives.

At minimum consider confusion matrices, sensitivity/recall, specificity,
precision, balanced accuracy, and class-wise results. For probabilistic
models, also inspect calibration.

## 9. Probability calibration

Modern neural networks can be accurate but poorly calibrated, meaning
predicted confidence does not necessarily match empirical correctness
frequency ([Guo et al. 2017](#ref-Guo2017_Calibration)).

``` r

truth <- c(0, 1, 1, 0, 1, 0, 1, 0)
p_disease <- c(.05, .90, .72, .15, .61, .35, .95, .08)
cal <- pheno_probability_calibration(truth, p_disease, bins=4)
cal$metrics
#> # A tibble: 1 × 4
#>    brier log_loss   ece accuracy
#>    <dbl>    <dbl> <dbl>    <dbl>
#> 1 0.0992    0.213 0.181        1
cal$calibration
#> # A tibble: 4 × 6
#>     bin lower upper     n mean_confidence accuracy
#>   <int> <dbl> <dbl> <int>           <dbl>    <dbl>
#> 1     1  0     0.25     0          NA           NA
#> 2     2  0.25  0.5      0          NA           NA
#> 3     3  0.5   0.75     3           0.66         1
#> 4     4  0.75  1        5           0.914        1
```

For multiclass outcomes, supply an observation-by-class matrix.

``` r

prob <- rbind(
  c(.80,.15,.05),
  c(.20,.70,.10),
  c(.10,.25,.65),
  c(.60,.25,.15),
  c(.25,.60,.15),
  c(.05,.20,.75)
)
truth3 <- c("healthy", "chlorotic", "necrotic", "healthy", "chlorotic", "necrotic")
pheno_probability_calibration(
  truth3,
  prob,
  classes=c("healthy", "chlorotic", "necrotic"),
  bins=3
)$metrics
#> # A tibble: 1 × 4
#>   brier log_loss   ece accuracy
#>   <dbl>    <dbl> <dbl>    <dbl>
#> 1 0.166    0.387 0.317        1
```

Brier score and log loss penalize probability quality, while ECE
summarizes the gap between confidence and observed correctness across
confidence bins. These metrics should be interpreted with sample size
and binning in mind.

## 10. Predictive uncertainty

``` r

c(
  very_confident=pheno_uncertainty(c(.99,.01), "entropy"),
  intermediate=pheno_uncertainty(c(.75,.25), "entropy"),
  ambiguous=pheno_uncertainty(c(.50,.50), "entropy")
)
#> very_confident   intermediate      ambiguous 
#>     0.08079314     0.81127812     1.00000000
```

Entropy increases as the distribution becomes less decisive. Margin and
confidence diagnostics provide alternative views.

``` r

c(
  entropy=pheno_uncertainty(c(.55,.45), "entropy"),
  margin=pheno_uncertainty(c(.55,.45), "margin"),
  confidence=pheno_uncertainty(c(.55,.45), "confidence")
)
#>    entropy     margin confidence 
#>  0.9927745  0.9000000  0.4500000
```

Uncertainty is not proof that an observation is biologically unusual. It
can arise from weak model training, ambiguous imagery, domain shift,
poor calibration, or genuinely intermediate phenotypes.

## 11. Classification versus quantitative phenotyping

### 11.1 Disease

If the question is whether a plant should be flagged for scouting,
classification may be enough. If the question is treatment effect on
disease severity, lesion area should usually be measured quantitatively.

### 11.2 Nutrient deficiency

A class label can be operationally useful when treatments correspond to
known deficiency syndromes. However, visible symptoms may overlap, and
RGB appearance can be confounded by acquisition. Complement image
classes with tissue analysis or other independent measurements when
making physiological claims.

### 11.3 Developmental stage

Stage classification may be valid when stage definitions are categorical
and reproducibly annotated. If the outcome is time-to-stage or growth
rate, retain the temporal structure instead of reducing all observations
to independent images.

## 12. Threshold-based decisions from probabilities

The maximum-probability class is not the only possible decision rule.
Operational screening may require a disease probability above 0.8 before
an automated action, with intermediate cases sent for manual review.
Such thresholds should be selected using validation data and
consequences, not the final test set.

``` r

p <- pred$probability["diseased"]
decision <- if (p >= .8) "automatic flag" else if (p >= .5) "manual review" else "no flag"
decision
#> [1] "automatic flag"
```

## 13. Register and audit a classifier

``` r

pheno_model_register(
  "classifier_teaching_v1",
  task="classification",
  architecture="cnn_small",
  classes=c("healthy", "diseased"),
  input_size=c(256,256),
  engine="torch",
  seed=101,
  loss="cross_entropy",
  optimizer="Adam",
  provenance=list(split_by="plant_id", purpose="teaching"),
  overwrite=TRUE
)
pheno_model_info("classifier_teaching_v1")
#> <pheno_model_record> classifier_teaching_v1 
#>   task: classification 
#>   architecture: cnn_small 
#>   engine: torch  0.17.0 
#>   classes: healthy, diseased
```

A publication model should add training dataset description, versioned
weights and hash, validation metrics, license, citation, and external
test evidence.

## 14. Augmentation and color-sensitive classes

Image augmentation surveys show a wide range of geometric and
photometric transformations ([Shorten and Khoshgoftaar
2019](#ref-Shorten2019_Augmentation)). In plant classification, color
changes require special caution. If the class distinction is chlorosis,
anthocyanin accumulation, necrosis, or another visible-color syndrome,
brightness/gamma/color transformations can alter the very signal the
model should learn.

``` r

try(
  pheno_dl_augment(img, operations="gamma", probability=1),
  silent=TRUE
)
```

OmniPhenoR requires `phenotype_preserving=FALSE` before those
transformations can be used.

## 15. Domain shift

A model validated on detached leaves against a uniform background should
not be described as field-ready. A classifier can fail when background,
camera, growth stage, cultivar, illumination, lesion type, or geographic
environment changes. A useful external validation deliberately samples
those dimensions.

## 16. Common mistakes

1.  Treating several images from one plant as independent test
    observations.
2.  Reporting overall accuracy on a highly imbalanced disease dataset.
3.  Allowing the final test set to guide class thresholds.
4.  Using random color augmentation for a color-defined phenotype
    without justification.
5.  Reporting class probabilities as if they were calibrated without
    checking calibration.
6.  Interpreting a class label as a continuous physiological
    measurement.
7.  Omitting class order from a saved model.
8.  Testing only the same acquisition environment used for training.

## 17. Reporting checklist

Report class definitions and annotation rules; image, plant, plot,
genotype, and environment counts; group used for splitting; class
prevalence in each partition; preprocessing and augmentation;
architecture and training settings; confusion metrics; calibration
diagnostics when probabilities are used; uncertainty or abstention rule;
external-validation domain; model/weights hash; and the biological
interpretation boundary of each class.

## 18. Final perspective

Classification is scientifically useful when the outcome is genuinely
categorical and the evaluation reflects the intended biological
deployment. OmniPhenoR keeps the probability distribution, class
identity, uncertainty, and provenance visible so that a convenient label
does not erase the measurement assumptions behind it.

## References

Guo, Chuan, Geoff Pleiss, Yu Sun, and Kilian Q. Weinberger. 2017. “On
Calibration of Modern Neural Networks.” *Proceedings of the 34th
International Conference on Machine Learning*, Proceedings of machine
learning research, vol. 70: 1321–30.

Kapoor, Sayash, and Arvind Narayanan. 2023. “Leakage and the
Reproducibility Crisis in Machine-Learning-Based Science.” *Patterns* 4
(9): 100804. <https://doi.org/10.1016/j.patter.2023.100804>.

Shorten, Connor, and Taghi M. Khoshgoftaar. 2019. “A Survey on Image
Data Augmentation for Deep Learning.” *Journal of Big Data* 6 (1): 60.
<https://doi.org/10.1186/s40537-019-0197-0>.
