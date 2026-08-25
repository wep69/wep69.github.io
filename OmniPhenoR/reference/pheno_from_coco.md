# Import a COCO-style detection object

Import a COCO-style detection object

## Usage

``` r
pheno_from_coco(x, image_id = NULL)
```

## Arguments

- x:

  COCO list or JSON file.

- image_id:

  Optional selected image id.

## Value

`pheno_detection` or named list for multiple images.

## Examples

``` r
d<-pheno_detection(data.frame(class="leaf",confidence=.9,xmin=10,ymin=20,xmax=50,ymax=60))
co<-pheno_to_coco(d,1,100,100);pheno_from_coco(co)
#> <pheno_detection>
#>   objects: 1 
#>   engine: coco 
#>   image: 1 
#>   classes: leaf 
pheno_from_coco(co,image_id=1)
#> <pheno_detection>
#>   objects: 1 
#>   engine: coco 
#>   image: 1 
#>   classes: leaf 
```
