# Export detections to YOLO label format

Export detections to YOLO label format

## Usage

``` r
pheno_to_yolo(
  detections,
  image_width,
  image_height,
  class_map = NULL,
  path = NULL
)
```

## Arguments

- detections:

  `pheno_detection` or compatible table.

- image_width:

  Image width.

- image_height:

  Image height.

- class_map:

  Named integer vector or character class order.

- path:

  Optional output text file.

## Value

Character YOLO lines invisibly if written, visibly otherwise.

## Examples

``` r
d<-pheno_detection(data.frame(class="leaf",confidence=.9,xmin=10,ymin=20,xmax=50,ymax=60))
pheno_to_yolo(d,100,100,class_map="leaf")
#> [1] "0 0.30000000 0.40000000 0.40000000 0.40000000"
pheno_to_yolo(d,100,100,class_map=c(leaf=0L))
#> [1] "0 0.30000000 0.40000000 0.40000000 0.40000000"
f<-tempfile();pheno_to_yolo(d,100,100,class_map="leaf",path=f);unlink(f)
```
