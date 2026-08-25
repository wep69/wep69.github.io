# OmniPhenoR Classical versus Deep-Learning Phenotyping

## 1. Purpose

A neural method should not be assumed to outperform a classical method
simply because it is newer. Controlled imaging, strong color contrast,
and simple organ geometry can make threshold-based workflows highly
effective. Complex backgrounds, overlapping tissues, variable symptoms,
and domain shifts can make trained models worthwhile. The correct
comparison depends on the final phenotype, validation domain, runtime,
and maintenance cost.

OmniPhenoR 0.2.0 provides a common comparison layer so classical and
model-based masks can be evaluated with the same truth and the same
phenotype function.

## 2. Frozen example

``` r

img <- pheno_data("leaf_rgb")
truth <- pheno_data("leaf_mask")
```

## 3. Classical ExG/Otsu baseline

``` r

classical <- pheno_segment(
  img,
  index="ExG",
  threshold="otsu",
  min_size=50
)
pheno_segmentation_metrics(truth, classical)
#> # A tibble: 1 × 13
#>      tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1  2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```

This is a transparent baseline. Its failure modes can often be
understood by inspecting the index and threshold.

## 4. Probability-model comparator

For tutorial purposes, create a probability model from the same visible
signal. This is not presented as deep learning. It demonstrates that any
model backend can be compared through the same object.

``` r

probability_model <- function(z) {
  exg <- pheno_rgb_indices(z,"ExG")[,,1]
  plogis(30*(exg-median(exg)))
}
model_pred <- pheno_segment_dl(
  img,
  probability_model,
  classes=c("background","leaf"),
  qc=FALSE
)
pheno_segmentation_metrics(truth, model_pred)
#> # A tibble: 1 × 13
#>      tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1  2872     0  9416     0 0.234 0.379     0.234      1           0 0.379
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```

## 5. Side-by-side metric table

``` r

pheno_compare_segmentation(
  truth,
  ExG_Otsu=classical,
  probability_model=model_pred
)
#> # A tibble: 2 × 14
#>   method     tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 ExG_Ot…  2872  9416     0     0 1     1         1          1           1 1    
#> 2 probab…  2872     0  9416     0 0.234 0.379     0.234      1           0 0.379
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```

IoU and Dice quantify overlap, but precision, recall, specificity, area
bias, and boundary disagreement reveal different error structures.

## 6. Compare the phenotypic consequence

``` r

pheno_compare_segmentation(
  truth,
  ExG_Otsu=classical,
  probability_model=model_pred,
  phenotype_fun=mean
)
#> # A tibble: 2 × 17
#>   method     tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 ExG_Ot…  2872  9416     0     0 1     1         1          1           1 1    
#> 2 probab…  2872     0  9416     0 0.234 0.379     0.234      1           0 0.379
#> # ℹ 6 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>, phenotype_estimate <dbl>,
#> #   phenotype_truth <dbl>, phenotype_bias <dbl>
```

If the intended trait is canopy cover, `mean(mask)` is directly
relevant. For leaf area, use calibrated area. For disease severity,
compare the derived severity estimate. A segmentation model with higher
Dice can still be less useful if it creates systematic bias in the final
trait.

## 7. Morphometry consequence

``` r

truth_shape <- pheno_morphology(truth)
classic_shape <- pheno_morphology(classical)
model_shape <- pheno_morphology(model_pred$mask)

rbind(
  truth=truth_shape[,c("area_px","perimeter_px","circularity")],
  classical=classic_shape[,c("area_px","perimeter_px","circularity")],
  model=model_shape[,c("area_px","perimeter_px","circularity")]
)
#> # A tibble: 3 × 3
#>   area_px perimeter_px circularity
#> *   <int>        <int>       <dbl>
#> 1    2872          264       0.518
#> 2    2872          264       0.518
#> 3   12288          448       0.769
```

Perimeter and circularity can be more sensitive to small boundary errors
than area. Method choice should therefore consider the traits actually
used downstream.

## 8. Runtime benchmarking

``` r

bm <- pheno_benchmark(
  img,
  methods=list(
    classical=function(z) pheno_segment(z,"ExG"),
    probability=function(z) pheno_segment_dl(z,probability_model,
      classes=c("background","leaf"),qc=FALSE)
  ),
  repetitions=2,
  device="cpu"
)
bm
#> # A tibble: 4 × 8
#>   method      repetition elapsed_sec output_mb memory_delta_mb gpu_memory_mb
#>   <chr>            <int>       <dbl>     <dbl>           <dbl>         <dbl>
#> 1 classical            1      0.0300    0.0486          0.1000            NA
#> 2 classical            2      0.0100    0.0486          0                 NA
#> 3 probability          1      0.0500    0.381           0.300             NA
#> 4 probability          2      0.0500    0.381           0                 NA
#> # ℹ 2 more variables: input_dimensions <chr>, device <chr>
```

The benchmark records elapsed time and coarse memory diagnostics. It is
not a universal performance ranking: hardware, image dimensions, model
architecture, batch size, and backend versions must accompany any
serious runtime comparison.

## 9. Neural reference model

A compact U-Net is available when `torch` is installed ([Ronneberger et
al. 2015](#ref-Ronneberger2015_UNet)).

``` r

net <- pheno_unet(3,2,base_channels=8)
# Train on independent grouped data before evaluating.
torch_pred <- pheno_segment_dl(
  img,
  net,
  classes=c("background","leaf"),
  device="auto"
)
pheno_compare_segmentation(
  truth,
  ExG_Otsu=classical,
  trained_U_Net=torch_pred,
  phenotype_fun=mean
)
```

An untrained network must never be included as a performance comparator.

## 10. When classical methods are attractive

Classical methods are strong candidates when:

- background and plant colors are well separated;
- acquisition is controlled;
- threshold logic is stable across genotypes and dates;
- training annotations are unavailable;
- interpretability and portability are priorities;
- computational resources are limited;
- expected deployment conditions closely match calibration conditions.

RGB index methods also provide interpretable visible-band features.
Foundational color-index work emphasizes the dependence of segmentation
on soil, residue, and illumination conditions ([Woebbecke et al.
1995](#ref-Woebbecke1995)).

## 11. When trained segmentation becomes attractive

A trained model may add value when:

- foreground/background color distributions overlap;
- complex shadows or residue defeat simple thresholds;
- multiple tissue classes must be separated;
- lesions or organs have informative shape/context beyond color;
- overlapping objects require learned spatial cues;
- enough independent annotated data exist;
- the validation design covers intended deployment conditions.

## 12. Complexity has a maintenance cost

A classical formula can often be reconstructed from a manuscript and a
threshold. A neural model requires weights, class order, preprocessing,
runtime dependencies, version compatibility, and a training/validation
record. That extra complexity is justified only when it produces
scientifically meaningful gains.

## 13. Benchmark by image scale

``` r

small <- pheno_data("leaf_rgb")
pheno_benchmark(
  small,
  list(
    exg=function(z) pheno_rgb_indices(z,"ExG"),
    mask=function(z) pheno_segment(z,"ExG")
  ),
  repetitions=1
)
#> # A tibble: 2 × 8
#>   method repetition elapsed_sec output_mb memory_delta_mb gpu_memory_mb
#>   <chr>       <int>       <dbl>     <dbl>           <dbl>         <dbl>
#> 1 exg             1      0.0200    0.0947               0            NA
#> 2 mask            1      0.0200    0.0486               0            NA
#> # ℹ 2 more variables: input_dimensions <chr>, device <chr>
```

For very large imagery, tile-based execution can change the runtime and
memory trade-off. Compare methods at image dimensions representative of
real deployment.

## 14. Compare stability, not only mean accuracy

A model that achieves high average Dice but fails badly on one genotype,
disease stage, or environment may be less useful than a slightly less
accurate but more stable method. Report stratified performance across
scientifically important groups.

## 15. Data requirements matter

A threshold baseline can be calibrated from a modest annotated set. A
neural model may need a much larger and more diverse training
collection. Augmentation can improve data efficiency ([Shorten and
Khoshgoftaar 2019](#ref-Shorten2019_Augmentation)), but it cannot
replace missing independent biological diversity.

## 16. Leakage can reverse apparent superiority

Machine-learning comparisons can be strongly distorted by data leakage
([Kapoor and Narayanan 2023](#ref-Kapoor2023_Leakage)). A neural model
evaluated on images related to its training images may appear much
better than a classical baseline whose performance is assessed on
genuinely independent units. Both methods must be evaluated on the same
clean test population.

## 17. Uncertainty adds another comparison dimension

Classical threshold methods have parameter sensitivity, while
probabilistic models have predictive confidence and calibration. Compare
uncertainty diagnostics with actual error rather than using them as
decoration.

``` r

c(
  model_entropy_mean=mean(model_pred$uncertainty),
  threshold=model_pred$threshold
)
#> model_entropy_mean          threshold 
#>          0.7680574          0.5000000
```

## 18. Decision framework

A practical method-selection sequence is:

1.  Establish annotated image truth and phenotype truth.
2.  Fit or calibrate a transparent classical baseline.
3.  Identify its systematic failure modes.
4.  Introduce a trained model only if those failures matter
    scientifically.
5.  Use group-aware training and independent validation.
6.  Compare masks, final phenotype error, stability, runtime, and
    reproducibility burden.
7.  Select the method before final biological inference.

## 19. Three examples

### 19.1 Leaf area on white background

A classical greenness threshold may be preferable if area bias is
negligible and masks are stable.

### 19.2 Foliar disease with mixed chlorotic and necrotic symptoms

A multiclass trained model may improve lesion delineation, but severity
error across low and high disease levels should decide its usefulness.

### 19.3 UAV canopy cover with residue and shadow

A model can outperform a fixed threshold if validated across flights,
plots, growth stages, and illumination. Tiling, spatial leakage, and
domain shift become central design issues.

## 20. Reporting checklist

For each comparator report identical test observations; exact classical
formulas/thresholds; model architecture and weights hash; split
grouping; image dimensions; hardware; post-processing; overlap metrics;
phenotype-level bias/error; stratified results; uncertainty/calibration
when used; runtime; memory measurement method; and any failures or
excluded images.

## 21. Final perspective

The question is not whether deep learning is more modern. The question
is which validated method produces the most defensible phenotype for the
intended biological domain at an acceptable computational and
reproducibility cost.

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

Woebbecke, D. M., G. E. Meyer, K. Von Bargen, and D. A. Mortensen. 1995.
“Color Indices for Weed Identification Under Various Soil, Residue, and
Lighting Conditions.” *Transactions of the ASAE* 38 (1): 259–69.
<https://doi.org/10.13031/2013.27838>.
