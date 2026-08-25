# Import YOLO bounding-box labels

Import YOLO bounding-box labels

## Usage

``` r
pheno_from_yolo(path, image_width, image_height, class_map, image_id = NULL)
```

## Arguments

- path:

  Label text file or character lines.

- image_width:

  Image width.

- image_height:

  Image height.

- class_map:

  Character class order; first entry is class 0.

- image_id:

  Optional image identifier.

## Value

`pheno_detection`.

## Examples

``` r
lines<-"0 0.5 0.5 0.4 0.2"
pheno_from_yolo(lines,100,100,"leaf")
#> <pheno_detection>
#>   objects: 1 
#>   engine: yolo_format 
#>   classes: leaf 
f<-tempfile();writeLines(lines,f);pheno_from_yolo(f,100,100,"leaf");unlink(f)
#> <pheno_detection>
#>   objects: 1 
#>   engine: yolo_format 
#>   classes: leaf 
```
