# Binary segmentation metrics

Binary segmentation metrics

## Usage

``` r
pheno_segmentation_metrics(truth, prediction, positive = NULL)
```

## Arguments

- truth:

  Ground-truth mask.

- prediction:

  Predicted mask or `pheno_prediction`.

- positive:

  Optional positive value for non-binary integer masks.

## Value

One-row tibble with confusion counts, IoU, Dice, precision, recall,
specificity, F1, balanced accuracy, area bias, and a boundary
disagreement fraction.

## Examples

``` r
truth <- matrix(c(1,1,0,0,1,1,0,0,0),3)
pred <- matrix(c(1,0,0,0,1,1,1,0,0),3)
pheno_segmentation_metrics(truth, pred)
#> # A tibble: 1 × 13
#>      tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1     3     4     1     1   0.6  0.75      0.75   0.75         0.8  0.75
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
pheno_segmentation_metrics(pheno_data("leaf_mask"), pheno_data("leaf_mask"))
#> # A tibble: 1 × 13
#>      tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1  2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
p <- pheno_prediction(pred != 0); pheno_segmentation_metrics(truth, p)
#> # A tibble: 1 × 13
#>      tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1     3     4     1     1   0.6  0.75      0.75   0.75         0.8  0.75
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```
