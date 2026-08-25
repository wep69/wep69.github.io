# Detect plant objects through a canonical API

Function adapters are fully testable without Python. The optional
`engine = "yolo"` adapter uses a user-supplied Ultralytics installation
and model; OmniPhenoR does not bundle or download Ultralytics weights.

## Usage

``` r
pheno_detect(
  x,
  model,
  engine = c("auto", "function", "yolo"),
  confidence = 0.25,
  iou = 0.45,
  classes = NULL,
  device = "auto",
  image_id = NULL,
  ...
)
```

## Arguments

- x:

  Image object or image path.

- model:

  Function/adapter, model record, or local YOLO model identifier.

- engine:

  `auto`, `function`, or `yolo`.

- confidence:

  Minimum confidence.

- iou:

  NMS IoU setting passed to YOLO.

- classes:

  Optional retained classes.

- device:

  Device label.

- image_id:

  Optional image identifier.

- ...:

  Backend-specific arguments.

## Value

A `pheno_detection`.

## References

Redmon J, Divvala S, Girshick R, Farhadi A (2016). You Only Look Once:
Unified, Real-Time Object Detection. CVPR:779-788.
[doi:10.1109/CVPR.2016.91](https://doi.org/10.1109/CVPR.2016.91) .

## Examples

``` r
mock <- function(z) data.frame(class="leaf",confidence=.95,xmin=5,ymin=5,xmax=40,ymax=30)
pheno_detect(pheno_data("leaf_rgb"), mock)
#> <pheno_detection>
#>   objects: 1 
#>   engine: function 
#>   classes: leaf 
pheno_detect(pheno_data("leaf_rgb"), mock, confidence=.8, image_id="plant1")
#> <pheno_detection>
#>   objects: 1 
#>   engine: function 
#>   image: plant1 
#>   classes: leaf 
pheno_detect(pheno_data("leaf_rgb"), model=mock, engine="auto")
#> <pheno_detection>
#>   objects: 1 
#>   engine: function 
#>   classes: leaf 
```
