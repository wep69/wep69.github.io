# Compare alternative segmentation methods

Compare alternative segmentation methods

## Usage

``` r
pheno_compare_segmentation(truth, ..., phenotype_fun = NULL)
```

## Arguments

- truth:

  Ground-truth binary mask.

- ...:

  Named predictions. Each may be a matrix or `pheno_prediction`.

- phenotype_fun:

  Optional function mapping a mask to a scalar phenotype. When supplied,
  phenotype bias relative to truth is included.

## Value

A tibble with one row per method.

## Examples

``` r
truth <- pheno_data("leaf_mask")
exg <- pheno_segment(pheno_data("leaf_rgb"), "ExG")
pheno_compare_segmentation(truth, exg = exg, perfect = truth)
#> # A tibble: 2 × 14
#>   method     tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 exg      2872  9416     0     0     1     1         1      1           1     1
#> 2 perfect  2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
pheno_compare_segmentation(truth, exg = exg, phenotype_fun = mean)
#> # A tibble: 1 × 17
#>   method    tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>  <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 exg     2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 6 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>, phenotype_estimate <dbl>,
#> #   phenotype_truth <dbl>, phenotype_bias <dbl>
mock <- pheno_segment_dl(pheno_data("leaf_rgb"),
  function(z) matrix(as.numeric(truth), nrow(truth)))
pheno_compare_segmentation(truth, mock = mock, exg = exg)
#> # A tibble: 2 × 14
#>   method    tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>  <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 mock    2872  9416     0     0     1     1         1      1           1     1
#> 2 exg     2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```
