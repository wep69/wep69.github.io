# Register experiment files with portable paths and checksums

Register experiment files with portable paths and checksums

## Usage

``` r
pheno_file_register(
  experiment,
  paths,
  type = "data",
  acquisition_id = NA_character_,
  license = NA_character_
)
```

## Arguments

- experiment:

  `pheno_experiment`.

- paths:

  Files to register.

- type:

  Semantic file type.

- acquisition_id:

  Optional acquisition identifier.

- license:

  Optional license id.

## Value

Updated experiment.

## Examples

``` r
f<-tempfile(fileext=".csv");writeLines("x,y\n1,2",f);e<-pheno_experiment("demo",root=tempdir())
e<-pheno_file_register(e,f,type="phenotype_table");e$files
#>                                                                                    file_id
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv file_e44b2e850fe1
#>                                                                                          path
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv file38fc74e731a1.csv
#>                                                                                     type
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv phenotype_table
#>                                                                          format
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv    csv
#>                                                                          size
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv   10
#>                                                                                                                                    sha256
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv 5862881949e3a662534aa26be2ffd7ba1f6a54f4a755d1f3f4d2912b8549e9d4
#>                                                                          acquisition_id
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv           <NA>
#>                                                                          license
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv    <NA>
#>                                                                                       registered
#> C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc74e731a1.csv 2026-08-25 01:02:16 UTC
pheno_file_info(e)
#> # A tibble: 1 × 9
#>   file_id      path  type  format  size sha256 acquisition_id license registered
#>   <chr>        <chr> <chr> <chr>  <dbl> <chr>  <chr>          <chr>   <chr>     
#> 1 file_e44b2e… file… phen… csv       10 58628… NA             NA      2026-08-2…
```
