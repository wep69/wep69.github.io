# Cross-engine validation against ground truth

Cross-engine validation against ground truth

## Usage

``` r
pheno_cross_engine_validate(
  truth,
  ...,
  type = c("segmentation", "detection"),
  iou_threshold = 0.5
)
```

## Arguments

- truth:

  Ground truth.

- ...:

  Named engine predictions.

- type:

  `segmentation` or `detection`.

- iou_threshold:

  Detection matching IoU.

## Value

Tidy comparison table.

## Examples

``` r
m<-pheno_data("leaf_mask"); pheno_cross_engine_validate(m,native=m,other=m,type="segmentation")
#> # A tibble: 2 × 14
#>   engine    tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>  <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 native  2872  9416     0     0     1     1         1      1           1     1
#> 2 other   2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
d<-pheno_detection(data.frame(class="leaf",confidence=1,xmin=0,ymin=0,xmax=5,ymax=5)); 
    pheno_cross_engine_validate(d,a=d,b=d,type="detection")
#> # A tibble: 2 × 9
#>   engine    tp    fp    fn precision recall    f1 mean_iou count_bias
#>   <chr>  <int> <int> <int>     <dbl>  <dbl> <dbl>    <dbl>      <int>
#> 1 a          1     0     0         1      1     1        1          0
#> 2 b          1     0     0         1      1     1        1          0
pheno_cross_engine_validate(m, classical=m, consensus=m, type="segmentation")
#> # A tibble: 2 × 14
#>   engine     tp    tn    fp    fn   iou  dice precision recall specificity    f1
#>   <chr>   <int> <int> <int> <int> <dbl> <dbl>     <dbl>  <dbl>       <dbl> <dbl>
#> 1 classi…  2872  9416     0     0     1     1         1      1           1     1
#> 2 consen…  2872  9416     0     0     1     1         1      1           1     1
#> # ℹ 3 more variables: balanced_accuracy <dbl>, area_bias <dbl>,
#> #   boundary_disagreement <dbl>
```
