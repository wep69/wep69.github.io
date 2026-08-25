# Open a large external dataset

Open a large external dataset

## Usage

``` r
pheno_dataset(path, backend = c("parquet", "csv"))
```

## Arguments

- path:

  Dataset path.

- backend:

  `parquet` or `csv`.

## Value

Arrow Dataset when available or an in-memory data frame.

## Examples

``` r
p<-tempfile(fileext=".csv")
utils::write.csv(data.frame(x=1:3),p,row.names=FALSE)
pheno_dataset(p,"csv")
#>   x
#> 1 1
#> 2 2
#> 3 3
if (FALSE) pheno_dataset("traits/","parquet") # \dontrun{}
if (FALSE) pheno_dataset("traits.parquet","parquet") # \dontrun{}
```
