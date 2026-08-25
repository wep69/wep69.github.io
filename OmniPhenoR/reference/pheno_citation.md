# Create a simple citation record for an experiment dataset

Create a simple citation record for an experiment dataset

## Usage

``` r
pheno_citation(experiment)
```

## Arguments

- experiment:

  `pheno_experiment`.

## Value

Named list suitable for later CITATION.cff generation.

## Examples

``` r
pheno_citation(pheno_experiment("demo","Demo"))
#> $type
#> [1] "dataset"
#> 
#> $title
#> [1] "Demo"
#> 
#> $identifier
#> [1] "exp_9d5e46ee618e"
#> 
#> $doi
#> [1] NA
#> 
#> $authors
#> data frame with 0 columns and 0 rows
#> 
#> $license
#> data frame with 0 columns and 0 rows
#> 
#> $date_released
#> [1] NA
#> 
e<-pheno_experiment("demo",metadata=list(doi="10.1234/demo"));pheno_citation(e)$doi
#> [1] "10.1234/demo"
names(pheno_citation(e))
#> [1] "type"          "title"         "identifier"    "doi"          
#> [5] "authors"       "license"       "date_released"
```
