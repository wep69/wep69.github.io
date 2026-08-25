# Inspect data lineage

Inspect data lineage

## Usage

``` r
pheno_lineage(experiment, id = NULL)
```

## Arguments

- experiment:

  `pheno_experiment`.

- id:

  Optional input/output id filter.

## Value

Tibble.

## Examples

``` r
e<-pheno_experiment("d");pheno_lineage(e)
#> # A tibble: 0 × 0
e<-pheno_lineage_add(e,"a","b","segment");pheno_lineage(e,"a")
#> # A tibble: 1 × 5
#>   input_id output_id operation details timestamp              
#>   <chr>    <chr>     <chr>     <chr>   <chr>                  
#> 1 a        b         segment   ""      2026-08-25 01:02:30 UTC
nrow(pheno_lineage(e))
#> [1] 1
```
