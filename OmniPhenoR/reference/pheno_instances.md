# Create canonical instance-segmentation output

Create canonical instance-segmentation output

## Usage

``` r
pheno_instances(
  masks,
  classes = NULL,
  confidence = NULL,
  image_id = NULL,
  engine = "native",
  model_id = NULL,
  provenance = list(),
  image_size = NULL
)
```

## Arguments

- masks:

  List of logical/binary matrices.

- classes:

  Class label per mask.

- confidence:

  Confidence per mask.

- image_id:

  Optional image identifier.

- engine:

  Backend label.

- model_id:

  Optional model identifier.

- provenance:

  Provenance list.

- image_size:

  Optional `c(height, width)` used when `masks` is empty.

## Value

`pheno_instances` object.

## Examples

``` r
m1 <- matrix(FALSE,20,20); m1[2:8,3:10] <- TRUE
m2 <- matrix(FALSE,20,20); m2[11:18,12:19] <- TRUE
pheno_instances(list(m1,m2),classes=c("leaf","leaf"),confidence=c(.9,.8))
#> <pheno_instances>
#>   instances: 2 
#>   engine: native 
#>   image: 20 x 20 
pheno_instances(list(m1),image_id="plant1")
#> <pheno_instances>
#>   instances: 1 
#>   engine: native 
#>   image: 20 x 20 
pheno_instances(list(), image_id="plant_without_objects", image_size=c(20,20))
#> <pheno_instances>
#>   instances: 0 
#>   engine: native 
```
