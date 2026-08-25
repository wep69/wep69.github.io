# OmniPhenoR Semantic Segmentation: From Classical Masks to Pixel Probabilities

## 1. Purpose

Semantic segmentation assigns a class to each pixel. In plant
phenotyping, that class may represent background versus leaf, soil
versus canopy, healthy versus diseased tissue, or several biological
regions at once. Version 0.2.0 keeps classical threshold segmentation as
a first-class baseline and adds probability-preserving model
segmentation.

A strong segmentation workflow asks two questions:

1.  Does the predicted mask agree with annotated image truth?
2.  Does any mask error materially change the phenotype that will be
    analyzed?

The second question is often neglected. A modest boundary difference can
be irrelevant for leaf count but consequential for lesion severity,
margin texture, or calibrated area.

## 2. Establish a frozen truth example

``` r

img <- pheno_data("leaf_rgb")
truth <- pheno_data("leaf_mask")

c(height=nrow(truth), width=ncol(truth), truth_fraction=mean(truth))
#>         height          width truth_fraction 
#>      96.000000     128.000000       0.233724
```

The teaching mask is known exactly because the image is synthetic. In a
real validation study, the reference should be independently annotated
using a documented protocol and, when possible, multiple annotators or
adjudication for ambiguous boundaries.

## 3. Classical segmentation remains the baseline

``` r

native <- pheno_segment(
  img,
  index="ExG",
  threshold="otsu",
  min_size=50,
  engine="native"
)

native
#> <pheno_mask> 96 x 128 
#>   foreground pixels: 2872 ( 23.37 %)
#>   index: ExG  threshold: -0.045772 
#>   engine: native
pheno_segmentation_metrics(truth, native)
#> # A tibble: 1 × 13
#>      tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1  2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```

The mask records the selected index, threshold, direction, minimum
component size, and engine. A method should not be dismissed merely
because it is simple. Controlled-background plant imaging can often be
solved reliably with a transparent index and threshold.

## 4. `engine = "auto"` is intentionally conservative

``` r

auto_native <- pheno_segment(img, index="ExG", engine="auto")
attr(auto_native, "engine_reason")
#> [1] "no model supplied, using transparent native segmentation"
```

If no model is supplied, `auto` chooses the native route. This rule
prevents an installed neural backend from silently changing an
established analysis.

When a model is explicitly supplied, the reason is recorded.

``` r

mock_prob <- function(z) {
  exg <- pheno_rgb_indices(z, "ExG")[, , 1]
  plogis(25 * (exg - median(exg)))
}

model_pred <- pheno_segment(
  img,
  engine="auto",
  model=mock_prob,
  return="prediction",
  classes=c("background", "leaf")
)
model_pred$provenance$engine_reason
#> [1] "model supplied, using model-based segmentation"
```

## 5. Why retain probabilities?

A hard mask records a decision. A probability field records evidence
before the decision threshold.

``` r

dim(model_pred$probability)
#> [1]  96 128   2
range(model_pred$probability[, , "leaf"])
#> [1] 0.5 1.0
summary(as.vector(model_pred$uncertainty))
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>  0.0000  1.0000  1.0000  0.7697  1.0000  1.0000
```

Pixels near an organ boundary or unusual symptom may have probabilities
close to 0.5. Those pixels are more informative for QC than a binary map
alone.

## 6. Threshold sensitivity

For binary segmentation, the same probability map can generate several
masks.

``` r

p_leaf <- model_pred$probability[, , "leaf"]
thresholds <- c(.30, .50, .70)
threshold_results <- lapply(thresholds, function(t) {
  m <- p_leaf >= t
  z <- pheno_segmentation_metrics(truth, m)
  data.frame(threshold=t, z)
})
do.call(rbind, threshold_results)
#>   threshold   tp   tn   fp fn      iou      dice precision recall specificity
#> 1       0.3 2872    0 9416  0 0.233724 0.3788918  0.233724      1           0
#> 2       0.5 2872    0 9416  0 0.233724 0.3788918  0.233724      1           0
#> 3       0.7 2872 9416    0  0 1.000000 1.0000000  1.000000      1           1
#>          f1 balanced_accuracy area_bias boundary_disagreement
#> 1 0.3788918               0.5  0.766276            0.03222656
#> 2 0.3788918               0.5  0.766276            0.03222656
#> 3 1.0000000               1.0  0.000000            0.00000000
```

Threshold tuning belongs to training/validation data. A threshold should
not be selected on the final test images and then evaluated on those
same images as if it had been prespecified.

## 7. U-Net reference architecture

U-Net is a standard reference architecture for dense segmentation
([Ronneberger et al. 2015](#ref-Ronneberger2015_UNet)). OmniPhenoR
implements a compact R `torch` version.

``` r

net <- pheno_unet(
  in_channels=3,
  out_channels=2,
  base_channels=8,
  dropout=.1
)

pred_torch <- pheno_segment_dl(
  img,
  net,
  classes=c("background", "leaf"),
  device="cpu"
)
```

An untrained network produces meaningless biological predictions. The
code above demonstrates construction and inference grammar only.
Training requires independent images and masks, a group-aware split, and
an evaluation design that matches deployment.

## 8. Segmentation metrics

``` r

pheno_segmentation_metrics(truth, truth)
#> # A tibble: 1 × 13
#>      tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1  2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```

For binary masks:

- **IoU** measures intersection divided by union;
- **Dice** emphasizes overlap and is algebraically related to IoU;
- **precision** asks how much predicted foreground is truly foreground;
- **recall** asks how much true foreground was recovered;
- **specificity** measures background recovery;
- **balanced accuracy** combines recall and specificity;
- **area bias** reports the signed difference in foreground fraction;
- **boundary disagreement** is a simple pixel-edge disagreement screen.

No single metric captures every failure pattern. A model that slightly
erodes all leaf margins may retain high overlap but systematically
underestimate area.

## 9. Compare methods in one table

``` r

perfect_probability <- function(z) matrix(as.numeric(truth), nrow(truth), ncol(truth))
perfect_pred <- pheno_segment_dl(
  img,
  perfect_probability,
  classes=c("background", "leaf"),
  qc=FALSE
)

comparison <- pheno_compare_segmentation(
  truth,
  classical=native,
  probability_model=perfect_pred
)
comparison
#> # A tibble: 2 × 14
#>   method     tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 classi…  2872  9416     0     0     1     1         1      1           1     1
#> 2 probab…  2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```

This comparison deliberately treats a classical and model-based mask
through the same metric function.

## 10. Compare the final phenotype, not only the mask

Suppose the scientific trait is leaf cover fraction.

``` r

pheno_compare_segmentation(
  truth,
  classical=native,
  probability_model=perfect_pred,
  phenotype_fun=mean
)
#> # A tibble: 2 × 17
#>   method     tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 classi…  2872  9416     0     0     1     1         1      1           1     1
#> 2 probab…  2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 6 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>, phenotype_estimate <dbl>,
#> #   phenotype_truth <dbl>, phenotype_bias <dbl>
```

For calibrated leaf area, a more meaningful phenotype function can apply
a pixel-to-area conversion. For disease, the consequential output may be
lesion percentage rather than total foreground area.

## 11. Morphology from a model mask

``` r

shape <- pheno_morphology(perfect_pred$mask)
shape[, c("area_px", "perimeter_px", "circularity", "aspect_ratio")]
#> # A tibble: 1 × 4
#>   area_px perimeter_px circularity aspect_ratio
#>     <int>        <int>       <dbl>        <dbl>
#> 1    2872          264       0.518          2.3
```

The downstream morphology function does not need to know whether the
mask came from ExG, a U-Net, or a future external model. Method
provenance stays in the prediction object.

## 12. Multiclass segmentation concept

A disease segmentation can use three channels:

``` text
background
healthy_leaf
lesion
```

A function adapter can demonstrate the expected H x W x C structure.

``` r

lesion <- pheno_data("lesion_mask")
multiclass_fun <- function(z) {
  pr <- array(0.02, c(nrow(truth), ncol(truth), 3),
              dimnames=list(NULL,NULL,c("background","healthy_leaf","lesion")))
  pr[, , "background"] <- ifelse(!truth, .96, .02)
  pr[, , "healthy_leaf"] <- ifelse(truth & !lesion, .96, .02)
  pr[, , "lesion"] <- ifelse(lesion, .96, .02)
  den <- apply(pr, c(1,2), sum)
  for (k in 1:3) pr[, , k] <- pr[, , k] / den
  pr
}

multi <- pheno_segment_dl(
  img,
  multiclass_fun,
  classes=c("background", "healthy_leaf", "lesion"),
  qc=FALSE
)
table(multi$mask)
#> 
#>    1    2    3 
#> 9416 2555  317
attr(multi$mask, "class_labels")
#> [1] "background"   "healthy_leaf" "lesion"
```

The integer mask carries class labels as an attribute. For production
disease calculations, explicit class extraction should be documented
before calling
[`pheno_disease()`](https://wep69.github.io/OmniPhenoR/reference/pheno_disease.md).

## 13. Post-processing is part of the method

A binary model mask can still be filtered by connected-component size
through the unified
[`pheno_segment()`](https://wep69.github.io/OmniPhenoR/reference/pheno_segment.md)
interface.

``` r

all_green <- function(z) matrix(.8, dim(z)[1], dim(z)[2])
post <- pheno_segment(
  img,
  engine="torch",
  model=all_green,
  threshold=.5,
  min_size=50,
  return="prediction",
  classes=c("background", "leaf")
)
mean(post$mask)
#> [1] 1
```

If component filtering, hole filling, erosion, dilation, or border
trimming materially changes the phenotype, it should be validated and
reported as part of the method rather than treated as cosmetic cleanup.

## 14. Uncertainty at pixel level

``` r

confident <- pheno_validation_data("ml")$probability
ambiguous <- pheno_validation_data("ml")$ambiguous_probability

c(
  confident_entropy=mean(pheno_uncertainty(confident, "entropy")),
  ambiguous_entropy=mean(pheno_uncertainty(ambiguous, "entropy"))
)
#> confident_entropy ambiguous_entropy 
#>          0.286397          1.000000
```

Uncertainty is not a substitute for error. A model can be confidently
wrong. Its value is diagnostic: uncertain regions can be prioritized for
review, annotation, or rejection criteria.

## 15. Three realistic phenotyping scenarios

### 15.1 Detached leaves on a controlled background

Start with ExG/Otsu. A neural model is justified only if the classical
baseline fails across relevant genotypes, senescence stages, shadows, or
acquisition conditions.

### 15.2 Disease lesions with heterogeneous symptoms

A multiclass model may be useful when lesion color overlaps with healthy
tissue or background. Evaluate severity error across the entire severity
range, not only mean overlap.

### 15.3 Field canopy segmentation

Soil moisture, residue, shadows, flowers, senescent leaves, and
neighboring plots can challenge fixed thresholds. Split by
plot/date/environment according to intended deployment, and quantify
both segmentation quality and plot-level canopy-cover bias.

## 16. Failure analysis table

A useful validation report should stratify errors by conditions such as
crop stage, genotype, severity, illumination, background, camera, date,
and field environment. An overall Dice score can hide systematic failure
in one agronomically important subset.

## 17. Reporting checklist

Report annotation protocol, biological unit, split grouping, number of
independent units, model architecture, preprocessing, class order,
probability threshold, post-processing, IoU/Dice and complementary
metrics, phenotype-level bias/error, uncertainty diagnostics,
runtime/device, validation strata, and examples of both successful and
failed segmentations.

## 18. Final perspective

The purpose of model-based segmentation is not to produce a more
sophisticated-looking mask. It is to produce a better validated
measurement. In OmniPhenoR, the preferred method is the simplest method
that remains accurate enough for the final phenotype across the intended
biological and acquisition domain.

## References

Ronneberger, Olaf, Philipp Fischer, and Thomas Brox. 2015. “U-Net:
Convolutional Networks for Biomedical Image Segmentation.” *Medical
Image Computing and Computer-Assisted Intervention – MICCAI 2015*,
Lecture notes in computer science, vol. 9351: 234–41.
<https://doi.org/10.1007/978-3-319-24574-4_28>.
