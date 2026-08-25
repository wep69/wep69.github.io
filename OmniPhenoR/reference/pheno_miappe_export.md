# Export MIAPPE-aligned experiment metadata

Export MIAPPE-aligned experiment metadata

## Usage

``` r
pheno_miappe_export(experiment, path = NULL, format = c("list", "json", "tsv"))
```

## Arguments

- experiment:

  `pheno_experiment`.

- path:

  Optional output path.

- format:

  `list`, `json`, or `tsv`.

## Value

Export object or path invisibly.

## Examples

``` r
e<-pheno_experiment("demo","Demo");pheno_miappe_export(e,format="list")
#> $standard
#> [1] "MIAPPE"
#> 
#> $version
#> [1] "1.2"
#> 
#> $experiment_id
#> [1] "exp_2bb828a5330a"
#> 
#> $fields
#>                section                     field requirement            value
#> 1        Investigation   Investigation unique ID    required exp_2bb828a5330a
#> 2        Investigation       Investigation title    required             Demo
#> 3        Investigation Investigation description recommended                 
#> 4                Study           Study unique ID    required             demo
#> 5                Study          Study start date recommended                 
#> 6                Study            Study end date recommended                 
#> 7                Study                Study type recommended                 
#> 8               Person               Person name recommended                 
#> 9               Person              Person ORCID recommended                 
#> 10            Location             Location name    required                 
#> 11            Location         Location latitude recommended                 
#> 12            Location        Location longitude recommended                 
#> 13 Biological material    Biological material ID    required                 
#> 14 Biological material             Genus species    required                 
#> 15   Observed variable               Variable ID    required                 
#> 16   Observed variable                     Trait    required                 
#> 17   Observed variable                    Method    required                 
#> 18   Observed variable                     Scale    required                 
#> 19   Observed variable                      Unit recommended                 
#> 20           Data file            Data file link recommended                 
#> 
p<-tempfile(fileext=".tsv");pheno_miappe_export(e,p,"tsv");file.exists(p)
#> [1] TRUE
if (FALSE) pheno_miappe_export(e,tempfile(fileext=".json"),"json") # \dontrun{}
```
