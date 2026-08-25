# Write a phenotype table using an explicit backend

Write a phenotype table using an explicit backend

## Usage

``` r
pheno_table_write(x, path, backend = c("rds", "csv", "parquet"))
```

## Arguments

- x:

  Data frame.

- path:

  Output path.

- backend:

  `rds`, `csv`, or `parquet`.

## Value

Path invisibly.

## Examples

``` r
p<-tempfile(fileext=".rds");pheno_table_write(data.frame(x=1:3),p,"rds")
p2<-tempfile(fileext=".csv");pheno_table_write(data.frame(x=1:3),p2,"csv")
if (FALSE) pheno_table_write(data.frame(x=1:3),tempfile(fileext=".parquet"),"parquet") # \dontrun{}
```
