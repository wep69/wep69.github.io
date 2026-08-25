# Quality control for object/instance annotations

Quality control for object/instance annotations

## Usage

``` r
pheno_annotation_qc(
  x,
  image_width = NULL,
  image_height = NULL,
  min_area = 1,
  duplicate_iou = 0.95
)
```

## Arguments

- x:

  `pheno_detection`, `pheno_instances`, or detection table.

- image_width:

  Optional width.

- image_height:

  Optional height.

- min_area:

  Minimum box/mask area.

- duplicate_iou:

  IoU threshold for duplicate-box flags.

## Value

A tibble of issues; empty means no detected issue.

## Examples

``` r
d<-pheno_detection(data.frame(class="leaf",confidence=.9,xmin=-1,ymin=0,xmax=5,ymax=5))
pheno_annotation_qc(d,image_width=10,image_height=10)
#> # A tibble: 1 × 3
#>   object_id issue           detail                 
#>       <int> <chr>           <chr>                  
#> 1         1 x_out_of_bounds box exceeds image width
pheno_annotation_qc(pheno_instances(list(matrix(TRUE,5,5))))
#> # A tibble: 0 × 3
#> # ℹ 3 variables: object_id <int>, issue <chr>, detail <chr>
```
