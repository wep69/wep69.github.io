# Describe a storage backend

Describe a storage backend

## Usage

``` r
pheno_storage(
  backend = c("memory", "rds", "csv", "parquet", "duckdb"),
  root = NULL
)
```

## Arguments

- backend:

  One of `memory`, `rds`, `csv`, `parquet`, or `duckdb`.

- root:

  Optional storage root.

## Value

`pheno_storage`.

## Examples

``` r
pheno_storage("memory")
#> $backend
#> [1] "memory"
#> 
#> $root
#> NULL
#> 
#> attr(,"class")
#> [1] "pheno_storage"
pheno_storage("parquet",tempdir())
#> $backend
#> [1] "parquet"
#> 
#> $root
#> [1] "C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO"
#> 
#> attr(,"class")
#> [1] "pheno_storage"
pheno_storage("duckdb",tempdir())
#> $backend
#> [1] "duckdb"
#> 
#> $root
#> [1] "C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO"
#> 
#> attr(,"class")
#> [1] "pheno_storage"
```
