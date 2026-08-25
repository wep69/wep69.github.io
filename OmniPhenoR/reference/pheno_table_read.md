# Read a phenotype table

Read a phenotype table

## Usage

``` r
pheno_table_read(path, backend = c("rds", "csv", "parquet"))
```

## Arguments

- path:

  Input path.

- backend:

  `rds`, `csv`, or `parquet`.

## Value

Data frame/table.

## Examples

``` r
p<-tempfile(fileext=".rds");saveRDS(data.frame(x=1:3),p);pheno_table_read(p,"rds")
#>   x
#> 1 1
#> 2 2
#> 3 3
p2<-tempfile(fileext=".csv");utils::write.csv(data.frame(x=1:3),p2,row.names=FALSE)
pheno_table_read(p2,"csv")
#>   x
#> 1 1
#> 2 2
#> 3 3
if (FALSE) pheno_table_read("traits.parquet","parquet") # \dontrun{}
```
