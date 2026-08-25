# Query a phenotype table with simple filters

Query a phenotype table with simple filters

## Usage

``` r
pheno_query(x, table = NULL, ...)
```

## Arguments

- x:

  Data frame or `pheno_experiment`.

- table:

  Experiment table name when `x` is an experiment.

- ...:

  Named equality filters.

## Value

Tibble.

## Examples

``` r
d<-data.frame(trt=c("A","A","B"),value=1:3);pheno_query(d,trt="A")
#> # A tibble: 2 × 2
#>   trt   value
#>   <chr> <int>
#> 1 A         1
#> 2 A         2
e<-pheno_experiment("d",tables=list(traits=d));pheno_query(e,"traits",trt="B")
#> # A tibble: 1 × 2
#>   trt   value
#>   <chr> <int>
#> 1 B         3
pheno_query(d)
#> # A tibble: 3 × 2
#>   trt   value
#>   <chr> <int>
#> 1 A         1
#> 2 A         2
#> 3 B         3
```
