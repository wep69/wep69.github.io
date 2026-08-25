# Create a schema definition

Create a schema definition

## Usage

``` r
pheno_schema(fields, required = names(fields), version = "1")
```

## Arguments

- fields:

  Named character vector/list of expected types.

- required:

  Required fields.

- version:

  Schema version.

## Value

`pheno_schema`.

## Examples

``` r
pheno_schema(c(id="character",value="numeric"))
#> $fields
#> $fields$id
#> [1] "character"
#> 
#> $fields$value
#> [1] "numeric"
#> 
#> 
#> $required
#> [1] "id"    "value"
#> 
#> $version
#> [1] "1"
#> 
#> attr(,"class")
#> [1] "pheno_schema"
pheno_schema(c(id="character",day="numeric"),required="id")
#> $fields
#> $fields$id
#> [1] "character"
#> 
#> $fields$day
#> [1] "numeric"
#> 
#> 
#> $required
#> [1] "id"
#> 
#> $version
#> [1] "1"
#> 
#> attr(,"class")
#> [1] "pheno_schema"
pheno_schema(c(date="Date"),version="1")
#> $fields
#> $fields$date
#> [1] "Date"
#> 
#> 
#> $required
#> [1] "date"
#> 
#> $version
#> [1] "1"
#> 
#> attr(,"class")
#> [1] "pheno_schema"
```
