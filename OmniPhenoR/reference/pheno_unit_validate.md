# Validate common phenotyping units

Validate common phenotyping units

## Usage

``` r
pheno_unit_validate(unit, dimension = NULL)
```

## Arguments

- unit:

  Unit labels.

- dimension:

  Optional expected dimensions.

## Value

Tibble.

## Examples

``` r
pheno_unit_validate(c("cm","cm2","1","%"))
#> # A tibble: 4 × 3
#>   unit  dimension recognized
#>   <chr> <chr>     <lgl>     
#> 1 cm    NA        TRUE      
#> 2 cm2   NA        TRUE      
#> 3 1     NA        TRUE      
#> 4 %     NA        TRUE      
pheno_unit_validate(c("pixel","pixel2","degree-day"))
#> # A tibble: 3 × 3
#>   unit       dimension recognized
#>   <chr>      <chr>     <lgl>     
#> 1 pixel      NA        TRUE      
#> 2 pixel2     NA        TRUE      
#> 3 degree-day NA        TRUE      
pheno_unit_validate(c("cm","unknown"),dimension=c("length","unknown"))
#> # A tibble: 2 × 3
#>   unit    dimension recognized
#>   <chr>   <chr>     <lgl>     
#> 1 cm      length    TRUE      
#> 2 unknown unknown   FALSE     
```
