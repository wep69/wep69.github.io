# Instance segmentation through canonical adapters

Instance segmentation through canonical adapters

## Usage

``` r
pheno_segment_instances(
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

  Image object/path.

- model:

  Function adapter or optional YOLO segmentation model.

- engine:

  `auto`, `function`, or `yolo`.

- confidence:

  Detection confidence.

- iou:

  IoU setting for backend NMS.

- classes:

  Optional retained classes.

- device:

  Device label.

- image_id:

  Optional image identifier.

- ...:

  Backend-specific arguments.

## Value

`pheno_instances`.

## References

He K, Gkioxari G, Dollar P, Girshick R (2017). Mask R-CNN. ICCV.
[doi:10.1109/ICCV.2017.322](https://doi.org/10.1109/ICCV.2017.322) .

## Examples

``` r
mock <- function(z) {
  m<-matrix(FALSE,20,20);m[3:12,4:13]<-TRUE
  list(masks=list(m),classes="leaf",confidence=.9)
}
pheno_segment_instances(matrix(0,20,20),mock)
#> <pheno_instances>
#>   instances: 1 
#>   engine: function 
#>   image: 20 x 20 
pheno_segment_instances(matrix(0,20,20),model=mock,engine="auto",image_id="p1")
#> <pheno_instances>
#>   instances: 1 
#>   engine: function 
#>   image: 20 x 20 
```
