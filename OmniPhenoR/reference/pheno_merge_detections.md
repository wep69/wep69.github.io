# Non-maximum suppression / merge for detections

Non-maximum suppression / merge for detections

## Usage

``` r
pheno_merge_detections(..., iou_threshold = 0.5, class_aware = TRUE)
```

## Arguments

- ...:

  `pheno_detection` objects or compatible tables.

- iou_threshold:

  IoU above which lower-confidence boxes are suppressed.

- class_aware:

  Suppress only within the same class.

## Value

Merged `pheno_detection`.

## Examples

``` r
a <- pheno_detection(data.frame(class="leaf",confidence=.9,xmin=0,ymin=0,xmax=10,ymax=10))
b <- pheno_detection(data.frame(class="leaf",confidence=.8,xmin=1,ymin=1,xmax=11,ymax=11))
pheno_merge_detections(a,b)
#> <pheno_detection>
#>   objects: 1 
#>   engine: merged 
#>   classes: leaf 
```
