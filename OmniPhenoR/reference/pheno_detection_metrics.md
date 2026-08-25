# Detection metrics using greedy IoU matching

Detection metrics using greedy IoU matching

## Usage

``` r
pheno_detection_metrics(
  truth,
  prediction,
  iou_threshold = 0.5,
  class_aware = TRUE
)
```

## Arguments

- truth:

  Ground-truth detections.

- prediction:

  Predicted detections.

- iou_threshold:

  Matching threshold.

- class_aware:

  Require equal classes.

## Value

List with summary and matched-pair table.

## Examples

``` r
truth <- pheno_detection(data.frame(class="leaf",confidence=1,xmin=0,ymin=0,xmax=10,ymax=10))
pred <- pheno_detection(data.frame(class="leaf",confidence=.9,xmin=1,ymin=1,xmax=10,ymax=10))
pheno_detection_metrics(truth,pred)
#> $summary
#> # A tibble: 1 × 8
#>      tp    fp    fn precision recall    f1 mean_iou count_bias
#>   <int> <int> <int>     <dbl>  <dbl> <dbl>    <dbl>      <int>
#> 1     1     0     0         1      1     1     0.81          0
#> 
#> $matches
#> # A tibble: 1 × 4
#>   truth_id pred_id class   iou
#>      <int>   <int> <chr> <dbl>
#> 1        1       1 leaf   0.81
#> 
pheno_detection_metrics(truth,pred,iou_threshold=.75)$summary
#> # A tibble: 1 × 8
#>      tp    fp    fn precision recall    f1 mean_iou count_bias
#>   <int> <int> <int>     <dbl>  <dbl> <dbl>    <dbl>      <int>
#> 1     1     0     0         1      1     1     0.81          0
```
