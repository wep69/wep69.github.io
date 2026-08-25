# Compute SHA-256 checksums

Compute SHA-256 checksums

## Usage

``` r
pheno_checksum(paths)
```

## Arguments

- paths:

  File paths.

## Value

Tibble with file, size, and SHA-256.

## Examples

``` r
f<-tempfile();writeLines("phenotype",f);pheno_checksum(f)
#> # A tibble: 1 × 3
#>   path                                                               size sha256
#>   <chr>                                                             <dbl> <chr> 
#> 1 "C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fcbde…    11 abd5f…
f2<-tempfile();writeLines("image",f2);pheno_checksum(c(f,f2))
#> # A tibble: 2 × 3
#>   path                                                               size sha256
#>   <chr>                                                             <dbl> <chr> 
#> 1 "C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fcbde…    11 abd5f…
#> 2 "C:\\Users\\wep69\\AppData\\Local\\Temp\\RtmpGMA6XO\\file38fc8ca…     7 3129b…
pheno_checksum(character())
#> # A tibble: 0 × 3
#> # ℹ 3 variables: path <chr>, size <dbl>, sha256 <chr>
```
