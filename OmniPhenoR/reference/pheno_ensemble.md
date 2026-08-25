# Ensemble masks or object detections

Ensemble masks or object detections

## Usage

``` r
pheno_ensemble(..., threshold = 0.5, iou_threshold = 0.5)
```

## Arguments

- ...:

  Predictions/detections.

- threshold:

  Fraction of mask votes required for consensus.

- iou_threshold:

  IoU threshold for detection merging.

## Value

Consensus prediction or merged detections.

## Examples

``` r
m<-pheno_data("leaf_mask"); pheno_ensemble(m,m,threshold=.5)
#> <pheno_prediction>
#>   engine: ensemble  device: cpu 
#>   mask dimensions: 96 x 128 
d<-pheno_detection(data.frame(class="leaf",confidence=.9,xmin=1,ymin=1,xmax=5,ymax=5));
    pheno_ensemble(d,d)
#> <pheno_detection>
#>   objects: 1 
#>   engine: merged 
#>   classes: leaf 
pheno_ensemble(pheno_prediction(m), pheno_prediction(m), threshold = .75)
#> <pheno_prediction>
#>   engine: ensemble  device: cpu 
#>   mask dimensions: 96 x 128 
```
