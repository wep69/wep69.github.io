# Create a custom external/Python model adapter

Create a custom external/Python model adapter

## Usage

``` r
pheno_python_model(
  predict,
  task = c("object_detection", "instance_segmentation", "classification",
    "semantic_segmentation"),
  classes = NULL,
  metadata = list()
)
```

## Arguments

- predict:

  R function that returns a canonical or convertible result.

- task:

  Task label.

- classes:

  Optional classes.

- metadata:

  Additional metadata.

## Value

`pheno_python_model_adapter`.

## Examples

``` r
a<-pheno_python_model(function(x) data.frame(class="leaf",confidence=.9,
  xmin=1,ymin=1,xmax=5,ymax=5),"object_detection")
a$task
#> [1] "object_detection"
pheno_detect(matrix(0,10,10),a)
#> <pheno_detection>
#>   objects: 1 
#>   engine: function 
#>   classes: leaf 
```
