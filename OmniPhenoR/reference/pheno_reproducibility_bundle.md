# Create a reproducibility bundle summary

Create a reproducibility bundle summary

## Usage

``` r
pheno_reproducibility_bundle(experiment, format = c("list", "text"))
```

## Arguments

- experiment:

  `pheno_experiment`.

- format:

  `list` or `text`.

## Value

List or text.

## Examples

``` r
pheno_reproducibility_bundle(pheno_experiment("demo"))
#> $experiment_id
#> [1] "exp_b3c117515803"
#> 
#> $study
#> [1] "demo"
#> 
#> $schema_version
#> [1] "0.5"
#> 
#> $created
#> [1] "2026-08-25 01:03:04 UTC"
#> 
#> $R
#> [1] "R version 4.6.0 (2026-04-24 ucrt)"
#> 
#> $OmniPhenoR
#> [1] "1.0.0"
#> 
#> $session
#>  [1] "R version 4.6.0 (2026-04-24 ucrt)"                                               
#>  [2] "Platform: x86_64-w64-mingw32/x64"                                                
#>  [3] "Running under: Windows 11 x64 (build 26200)"                                     
#>  [4] ""                                                                                
#>  [5] "Matrix products: default"                                                        
#>  [6] "  LAPACK version 3.12.1"                                                         
#>  [7] ""                                                                                
#>  [8] "locale:"                                                                         
#>  [9] "[1] LC_COLLATE=C                       LC_CTYPE=Portuguese_Brazil.utf8   "       
#> [10] "[3] LC_MONETARY=Portuguese_Brazil.utf8 LC_NUMERIC=C                      "       
#> [11] "[5] LC_TIME=Portuguese_Brazil.utf8    "                                          
#> [12] ""                                                                                
#> [13] "time zone: America/Sao_Paulo"                                                    
#> [14] "tzcode source: internal"                                                         
#> [15] ""                                                                                
#> [16] "attached base packages:"                                                         
#> [17] "[1] stats     graphics  grDevices utils     datasets  methods   base     "       
#> [18] ""                                                                                
#> [19] "other attached packages:"                                                        
#> [20] "[1] OmniPhenoR_1.0.0"                                                            
#> [21] ""                                                                                
#> [22] "loaded via a namespace (and not attached):"                                      
#> [23] " [1] gtable_0.3.6       xfun_0.60          bslib_0.12.0       ggplot2_4.0.3     "
#> [24] " [5] httr2_1.3.0        htmlwidgets_1.6.4  remotes_2.5.0      processx_3.9.0    "
#> [25] " [9] lattice_0.22-9     callr_3.8.0        generics_0.1.4     vctrs_0.7.3       "
#> [26] "[13] tools_4.6.0        pak_0.11.1         stats4_4.6.0       curl_7.1.0        "
#> [27] "[17] tibble_3.3.1       proxy_0.4-29       fansi_1.0.7        pkgconfig_2.0.3   "
#> [28] "[21] torch_0.17.0       Matrix_1.7-6       KernSmooth_2.23-26 RColorBrewer_1.1-3"
#> [29] "[25] S7_0.2.2           desc_1.4.3         lifecycle_1.0.5    farver_2.1.2      "
#> [30] "[29] compiler_4.6.0     textshaping_1.0.5  terra_1.9-34       fontawesome_0.5.3 "
#> [31] "[33] codetools_0.2-20   htmltools_0.5.9    class_7.3-23       sass_0.4.10       "
#> [32] "[37] yaml_2.3.12        pillar_1.11.1      pkgdown_2.2.1      jquerylib_0.1.4   "
#> [33] "[41] whisker_0.4.1      classInt_0.4-11    cachem_1.1.0       magick_2.9.1      "
#> [34] "[45] nlme_3.1-170       tidyselect_1.2.1   digest_0.6.39      dplyr_1.2.1       "
#> [35] "[49] sf_1.1-2           purrr_1.2.2        labeling_0.4.3     fastmap_1.2.0     "
#> [36] "[53] grid_4.6.0         cli_3.6.6          magrittr_2.0.5     dichromat_2.0-1   "
#> [37] "[57] utf8_1.2.6         e1071_1.7-17       withr_3.0.3        scales_1.4.0      "
#> [38] "[61] bit64_4.8.2        rmarkdown_2.31     bit_4.6.0          otel_0.2.0        "
#> [39] "[65] reticulate_1.46.0  ragg_1.5.2         png_0.1-9          memoise_2.0.1     "
#> [40] "[69] evaluate_1.0.5     knitr_1.51         rlang_1.3.0        downlit_0.4.5     "
#> [41] "[73] Rcpp_1.1.2         glue_1.8.1         DBI_1.3.0          xml2_1.6.0        "
#> [42] "[77] coro_1.1.0         rstudioapi_0.19.0  jsonlite_2.0.0     R6_2.6.1          "
#> [43] "[81] systemfonts_1.3.2  fs_2.1.0           units_1.0-1       "                   
#> 
#> $files
#> # A tibble: 0 × 0
#> 
#> $lineage
#> data frame with 0 columns and 0 rows
#> 
#> $standards
#> character(0)
#> 
pheno_reproducibility_bundle(pheno_experiment("demo"),"text")
#>  [1] "List of 10"                                                                                                                                                       
#>  [2] " $ experiment_id : chr \"exp_19f4281e2053\""                                                                                                                      
#>  [3] " $ study         : chr \"demo\""                                                                                                                                  
#>  [4] " $ schema_version: chr \"0.5\""                                                                                                                                   
#>  [5] " $ created       : chr \"2026-08-25 01:03:04 UTC\""                                                                                                               
#>  [6] " $ R             : chr \"R version 4.6.0 (2026-04-24 ucrt)\""                                                                                                     
#>  [7] " $ OmniPhenoR    : chr \"1.0.0\""                                                                                                                                 
#>  [8] " $ session       : chr [1:43] \"R version 4.6.0 (2026-04-24 ucrt)\" \"Platform: x86_64-w64-mingw32/x64\" \"Running under: Windows 11 x64 (build 26200)\" \"\" ..."
#>  [9] " $ files         : tibble [0 × 0] (S3: tbl_df/tbl/data.frame)"                                                                                                    
#> [10] " Named list()"                                                                                                                                                    
#> [11] " $ lineage       :'data.frame':\t0 obs. of  0 variables"                                                                                                          
#> [12] " $ standards     : chr(0) "                                                                                                                                       
length(pheno_reproducibility_bundle(pheno_experiment("demo"),"text"))>0
#> [1] TRUE
```
