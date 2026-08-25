# Merge overlapping instance predictions

Merge overlapping instance predictions

## Usage

``` r
pheno_merge_instances(..., iou_threshold = 0.5, class_aware = TRUE)
```

## Arguments

- ...:

  `pheno_instances` objects.

- iou_threshold:

  Mask-IoU suppression threshold.

- class_aware:

  Suppress only within classes.

## Value

Merged `pheno_instances`.

## Examples

``` r
m<-matrix(FALSE,20,20);m[2:10,2:10]<-TRUE
a<-pheno_instances(list(m),"leaf",.9); b<-pheno_instances(list(m),"leaf",.8)
pheno_merge_instances(a,b)
#> <pheno_instances>
#>   instances: 1 
#>   engine: merged 
#>   image: 20 x 20 
```
