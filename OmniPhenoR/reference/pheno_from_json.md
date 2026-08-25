# Import experiment metadata from JSON

Import experiment metadata from JSON

## Usage

``` r
pheno_from_json(x)
```

## Arguments

- x:

  JSON text or path.

## Value

`pheno_experiment`.

## Examples

``` r
if (FALSE) j<-pheno_to_json(pheno_experiment("demo"));pheno_from_json(j) # \dontrun{}
#> Error: object 'j' not found
if (FALSE) p<-tempfile();pheno_to_json(pheno_experiment("demo"),p);pheno_from_json(p) # \dontrun{}
#> Error: object 'p' not found
if (FALSE) inherits(pheno_from_json(pheno_to_json(pheno_experiment("x"))),"pheno_experiment") # \dontrun{}
```
