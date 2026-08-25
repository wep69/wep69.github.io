# Export detections to a COCO-style object

Export detections to a COCO-style object

## Usage

``` r
pheno_to_coco(
  detections,
  image_id = 1L,
  image_width,
  image_height,
  categories = NULL,
  file = NULL
)
```

## Arguments

- detections:

  `pheno_detection` or table.

- image_id:

  Integer image id.

- image_width:

  Image width.

- image_height:

  Image height.

- categories:

  Optional class vector.

- file:

  Optional JSON file; requires `jsonlite`.

## Value

COCO-style list.

## References

Lin TY et al. (2014). Microsoft COCO: Common Objects in Context. ECCV
2014.
[doi:10.1007/978-3-319-10602-1_48](https://doi.org/10.1007/978-3-319-10602-1_48)
.

## Examples

``` r
d<-pheno_detection(data.frame(class="leaf",confidence=.9,xmin=10,ymin=20,xmax=50,ymax=60))
pheno_to_coco(d,1,100,100)
#> $images
#> $images[[1]]
#> $images[[1]]$id
#> [1] 1
#> 
#> $images[[1]]$width
#> [1] 100
#> 
#> $images[[1]]$height
#> [1] 100
#> 
#> 
#> 
#> $categories
#> $categories[[1]]
#> $categories[[1]]$id
#> [1] 1
#> 
#> $categories[[1]]$name
#> [1] "leaf"
#> 
#> 
#> 
#> $annotations
#> $annotations[[1]]
#> $annotations[[1]]$id
#> [1] 1
#> 
#> $annotations[[1]]$image_id
#> [1] 1
#> 
#> $annotations[[1]]$category_id
#> [1] 1
#> 
#> $annotations[[1]]$bbox
#> [1] 10 20 40 40
#> 
#> $annotations[[1]]$area
#> [1] 1600
#> 
#> $annotations[[1]]$iscrowd
#> [1] 0
#> 
#> $annotations[[1]]$score
#> [1] 0.9
#> 
#> 
#> 
names(pheno_to_coco(d,1,100,100))
#> [1] "images"      "categories"  "annotations"
```
