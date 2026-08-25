# Create a canonical object-detection result

Create a canonical object-detection result

## Usage

``` r
pheno_detection(
  data,
  image_size = NULL,
  image_id = NULL,
  engine = "native",
  model_id = NULL,
  model_hash = NULL,
  coordinate_system = c("pixel_xy", "normalized_xy"),
  provenance = list()
)
```

## Arguments

- data:

  Detection table with `class`, `confidence`, `xmin`, `ymin`, `xmax`,
  `ymax`.

- image_size:

  Optional `c(height, width)`.

- image_id:

  Optional image identifier.

- engine:

  Backend/engine label.

- model_id:

  Optional model identifier.

- model_hash:

  Optional model SHA-256.

- coordinate_system:

  `pixel_xy` or `normalized_xy`.

- provenance:

  Named provenance list.

## Value

A `pheno_detection` object.

## Examples

``` r
d <- data.frame(class=c("leaf","leaf"),confidence=c(.9,.8),
  xmin=c(1,20),ymin=c(2,10),xmax=c(15,35),ymax=c(18,30))
pheno_detection(d, image_size=c(40,40), image_id="plant1")
#> <pheno_detection>
#>   objects: 2 
#>   engine: native 
#>   image: plant1 
#>   classes: leaf 
pheno_detection(d[1,], engine="function")
#> <pheno_detection>
#>   objects: 1 
#>   engine: function 
#>   classes: leaf 
print(pheno_detection(d))
#> <pheno_detection>
#>   objects: 2 
#>   engine: native 
#>   classes: leaf 
```
