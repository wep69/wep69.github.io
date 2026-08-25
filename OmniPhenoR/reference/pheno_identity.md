# Construct explicit biological identity records

Construct explicit biological identity records

## Usage

``` r
pheno_identity(
  image_id,
  study_id,
  environment_id = NULL,
  plot_id = NULL,
  plant_id = NULL,
  organ_id = NULL
)
```

## Arguments

- image_id:

  Unique image identifiers.

- study_id:

  Study identifier.

- environment_id:

  Optional environment identifiers.

- plot_id:

  Optional plot identifiers.

- plant_id:

  Optional plant identifiers.

- organ_id:

  Optional organ identifiers.

## Value

Identity tibble.

## Examples

``` r
pheno_identity(c("i1","i2"),"study1",plot_id=c("p1","p2"),plant_id=c("a","b"))
#> # A tibble: 2 × 6
#>   study_id environment_id plot_id plant_id organ_id image_id
#>   <chr>    <chr>          <chr>   <chr>    <chr>    <chr>   
#> 1 study1   NA             p1      a        NA       i1      
#> 2 study1   NA             p2      b        NA       i2      
pheno_identity("i1","study1",environment_id="field")
#> # A tibble: 1 × 6
#>   study_id environment_id plot_id plant_id organ_id image_id
#>   <chr>    <chr>          <chr>   <chr>    <chr>    <chr>   
#> 1 study1   field          NA      NA       NA       i1      
pheno_identity(c("i1","i2"), "trial", environment_id = "E1", organ_id = c("leaf1","leaf2"))
#> # A tibble: 2 × 6
#>   study_id environment_id plot_id plant_id organ_id image_id
#>   <chr>    <chr>          <chr>   <chr>    <chr>    <chr>   
#> 1 trial    E1             NA      NA       leaf1    i1      
#> 2 trial    E1             NA      NA       leaf2    i2      
```
