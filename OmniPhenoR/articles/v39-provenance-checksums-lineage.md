# Checksums, Manifests, Provenance, and Data Lineage

## Why this capability exists

Reproducibility requires more than recording software versions. A result
should be traceable back through its inputs, transformations, files, and
checksums.

Version 0.5.0 treats experiment metadata and storage as scientific
infrastructure. The data model is intentionally R-first and
offline-first. Optional database, Arrow, JSON, and HTTP backends are
activated only when the corresponding capability is explicitly
requested.

Relevant functions in this vignette include:

- [`pheno_checksum()`](https://wep69.github.io/OmniPhenoR/reference/pheno_checksum.md)
- [`pheno_file_register()`](https://wep69.github.io/OmniPhenoR/reference/pheno_file_register.md)
- [`pheno_manifest()`](https://wep69.github.io/OmniPhenoR/reference/pheno_manifest.md)
- [`pheno_verify_manifest()`](https://wep69.github.io/OmniPhenoR/reference/pheno_verify_manifest.md)
- [`pheno_lineage_add()`](https://wep69.github.io/OmniPhenoR/reference/pheno_lineage_add.md)
- [`pheno_lineage()`](https://wep69.github.io/OmniPhenoR/reference/pheno_lineage.md)
- [`pheno_reproducibility_bundle()`](https://wep69.github.io/OmniPhenoR/reference/pheno_reproducibility_bundle.md)

## Conceptual workflow

![Conceptual 0.5.0
workflow](../inst/extdata/vignette-results/figures/v39-provenance-checksums-lineage.png)

Conceptual 0.5.0 workflow

The illustration is a local package asset. It is distributed with the
vignette so that the installed documentation remains useful without
internet access.

## Worked agronomic example

``` r

root <- tempfile("phenotyping_exp_")
dir.create(root)
raw <- file.path(root, "image_001.txt")
writeLines("synthetic image placeholder", raw)

exp <- pheno_experiment("trace_demo", root = root)
exp <- pheno_file_register(exp, raw, type = "raw_image")
pheno_manifest(exp)
#> # A tibble: 1 × 9
#>   file_id      path  type  format  size sha256 acquisition_id license registered
#>   <chr>        <chr> <chr> <chr>  <dbl> <chr>  <chr>          <chr>   <chr>     
#> 1 file_47ad85… imag… raw_… txt       29 7dcdc… NA             NA      2026-08-2…
pheno_verify_manifest(exp)
#> # A tibble: 1 × 6
#>   file_id           path     exists checksum_ok current_sha256 registered_sha256
#>   <chr>             <chr>    <lgl>  <lgl>       <chr>          <chr>            
#> 1 file_47ad85694645 image_0… TRUE   TRUE        7dcdcc8dc133b… 7dcdcc8dc133b976…

exp <- pheno_lineage_add(
  exp,
  input_id = exp$files$file_id[1],
  output_id = "mask_001",
  operation = "segmentation",
  details = "native threshold workflow"
)
exp <- pheno_lineage_add(
  exp, "mask_001", "traits_001", "morphology"
)
pheno_lineage(exp)
#> # A tibble: 2 × 5
#>   input_id          output_id  operation    details                    timestamp
#>   <chr>             <chr>      <chr>        <chr>                      <chr>    
#> 1 file_47ad85694645 mask_001   segmentation "native threshold workflo… 2026-08-…
#> 2 mask_001          traits_001 morphology   ""                         2026-08-…
pheno_reproducibility_bundle(exp)
#> $experiment_id
#> [1] "exp_0d51a70be8cf"
#> 
#> $study
#> [1] "trace_demo"
#> 
#> $schema_version
#> [1] "0.5"
#> 
#> $created
#> [1] "2026-08-25 01:08:39 UTC"
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
#>  [9] "[1] LC_COLLATE=Portuguese_Brazil.utf8  LC_CTYPE=Portuguese_Brazil.utf8   "   
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
#> [23] " [1] vctrs_0.7.3       cli_3.6.6         knitr_1.51        rlang_1.3.0      "
#> [24] " [5] xfun_0.60         otel_0.2.0        textshaping_1.0.5 jsonlite_2.0.0   "
#> [25] " [9] glue_1.8.1        htmltools_0.5.9   ragg_1.5.2        sass_0.4.10      "
#> [26] "[13] rmarkdown_2.31    evaluate_1.0.5    jquerylib_0.1.4   tibble_3.3.1     "
#> [27] "[17] fastmap_1.2.0     yaml_2.3.12       lifecycle_1.0.5   compiler_4.6.0   "
#> [28] "[21] fs_2.1.0          pkgconfig_2.0.3   htmlwidgets_1.6.4 systemfonts_1.3.2"
#> [29] "[25] digest_0.6.39     R6_2.6.1          utf8_1.2.6        pillar_1.11.1    "
#> [30] "[29] magrittr_2.0.5    bslib_0.12.0      tools_4.6.0       pkgdown_2.2.1    "
#> [31] "[33] cachem_1.1.0      desc_1.4.3       "                                    
#> 
#> $files
#> # A tibble: 1 × 9
#>   file_id      path  type  format  size sha256 acquisition_id license registered
#>   <chr>        <chr> <chr> <chr>  <dbl> <chr>  <chr>          <chr>   <chr>     
#> 1 file_47ad85… imag… raw_… txt       29 7dcdc… NA             NA      2026-08-2…
#> 
#> $lineage
#>            input_id  output_id    operation                   details
#> 1 file_47ad85694645   mask_001 segmentation native threshold workflow
#> 2          mask_001 traits_001   morphology                          
#>                 timestamp
#> 1 2026-08-25 01:08:39 UTC
#> 2 2026-08-25 01:08:39 UTC
#> 
#> $standards
#> character(0)
```

## Interpretation

The important output is not merely an R object. It is a documented link
between the experimental question, biological units, data files,
observed variables, processing history, and downstream reuse. A workflow
is scientifically stronger when identifiers survive the entire chain
from raw acquisition to published dynamic trait.

The example above is deliberately small enough for release validation.
Real imaging projects may contain millions of object-level rows and
thousands of image files; those data should normally remain external or
use efficient storage rather than being embedded in the R package.

## Example 2: validation before publication

``` r

exp2 <- pheno_experiment(
  study = "validation_example",
  title = "Validation example",
  metadata = list(description = "Teaching example")
)

miappe_check <- pheno_miappe_validate(exp2)
miappe_check$summary
#> # A tibble: 1 × 5
#>   standard version required_missing recommended_missing complete
#>   <chr>    <chr>              <int>               <int> <lgl>   
#> 1 MIAPPE   1.2                    7                   9 FALSE

fair_check <- pheno_fair_audit(exp2)
subset(fair_check, status == "incomplete")
#> # A tibble: 12 × 4
#>    principle evidence status     recommendation                                 
#>    <chr>     <lgl>    <chr>      <chr>                                          
#>  1 F1        FALSE    incomplete Assign a persistent identifier                 
#>  2 F2        FALSE    incomplete Add rich study metadata                        
#>  3 F3        FALSE    incomplete Link metadata to registered files              
#>  4 F4        FALSE    incomplete Register/index dataset in a repository         
#>  5 A1        FALSE    incomplete Document retrieval/access protocol             
#>  6 A1.2      FALSE    incomplete Document authentication/authorization if needed
#>  7 I1        FALSE    incomplete Use machine-readable structured metadata       
#>  8 I2        FALSE    incomplete Use resolvable controlled vocabulary identifie…
#>  9 I3        FALSE    incomplete Record qualified relations/lineage             
#> 10 R1        FALSE    incomplete Provide sufficient domain attributes           
#> 11 R1.1      FALSE    incomplete Declare explicit data usage license            
#> 12 R1.3      FALSE    incomplete Use community standards such as MIAPPE/BrAPI w…
```

Missing metadata are reported; OmniPhenoR does not infer scientific
facts that were never provided. This is particularly important for
germplasm identity, experimental location, methods, licenses, persistent
identifiers, and observation-variable semantics.

## Example 3: reproducibility and portability

``` r

root <- tempfile("portable_exp_")
dir.create(root)
example_file <- file.path(root, "traits.csv")
write.csv(data.frame(plot = c("P1", "P2"), value = c(0.42, 0.55)),
          example_file, row.names = FALSE)

portable <- pheno_experiment("portable", root = root)
portable <- pheno_file_register(portable, example_file, type = "phenotype_table")
pheno_manifest(portable)
#> # A tibble: 1 × 9
#>   file_id      path  type  format  size sha256 acquisition_id license registered
#>   <chr>        <chr> <chr> <chr>  <dbl> <chr>  <chr>          <chr>   <chr>     
#> 1 file_b141c2… trai… phen… csv       38 2bb75… NA             NA      2026-08-2…
pheno_verify_manifest(portable)
#> # A tibble: 1 × 6
#>   file_id           path     exists checksum_ok current_sha256 registered_sha256
#>   <chr>             <chr>    <lgl>  <lgl>       <chr>          <chr>            
#> 1 file_b141c26d8522 traits.… TRUE   TRUE        2bb75ff315e6b… 2bb75ff315e6b251…
```

Checksums detect file changes; they do not prove that a scientific
analysis is valid. Conversely, a scientifically valid analysis can
become difficult to reuse if its files and provenance cannot be
reconstructed. Both dimensions matter.

## Common mistakes

1.  Storing only absolute paths from one workstation.
2.  Treating a file name as a stable scientific identifier.
3.  Mixing trait, method, scale, and unit into one ambiguous label.
4.  Calling a dataset FAIR merely because it is downloadable.
5.  Assuming FAIR means open access; restricted data can still document
    access conditions.
6.  Embedding passwords, API tokens, or personal local paths in
    manifests.
7.  Requiring internet access during `R CMD check`.
8.  Treating MIAPPE or BrAPI mappings as a substitute for checking the
    current official standard.
9.  Publishing model-derived phenotypes without model/version/hash
    provenance.
10. Recreating heavy documentation analyses during every GitHub
    installation.

## Reporting recommendations

For a reusable phenotyping dataset, report at minimum:

- study and experiment identifiers;
- experimental design and biological units;
- germplasm and location metadata;
- observed-variable trait, method, scale, and unit;
- sensor and acquisition information when relevant;
- file formats and integrity checks;
- software and model provenance;
- access conditions and licenses;
- persistent identifiers when assigned;
- community-standard mappings used for exchange;
- any privacy transformations such as reduced coordinate precision.

## Precompiled documentation policy

This vignette is intended to be distributed as precompiled HTML under
`inst/doc/`. The authoritative source remains this Rmd file. Ordinary
GitHub installation should not rerun all vignettes:

``` r

remotes::install_github(
  "wep69/OmniPhenoR",
  build_vignettes = FALSE
)
```

Release validation is different. Before freezing 0.5.0, the maintainer
must rerender every Rmd with R/knitr/rmarkdown, inspect the results, run
the complete package check, install the exact checked tarball, and
verify that all installed vignettes are discoverable without rebuilding
them.

## References and standards

This vignette relies on the package reference-verification ledger.
Metadata for promoted references are recorded with at least two
verification sources. The relevant sources here include Wilkinson et al.
(2016) GO FAIR initiative (2026).

## Take-home workflow

``` text
experimental design
      ↓
canonical OmniPhenoR experiment
      ↓
portable paths + semantic registries
      ↓
manifest + checksum + lineage
      ↓
MIAPPE / BrAPI / FAIR evidence
      ↓
portable bundle / repository release
      ↓
reusable phenotyping data
```

The 0.5.0 infrastructure is therefore not a replacement for the
image-analysis and longitudinal layers introduced earlier. It is the
layer that preserves their scientific meaning when the experiment leaves
the analyst’s current R session or computer.

GO FAIR initiative. 2026. *FAIR Principles and FAIRification Guidance*.
<https://www.go-fair.org/fair-principles/>.

Wilkinson, Mark D., Michel Dumontier, IJsbrand Jan Aalbersberg, et al.
2016. “The FAIR Guiding Principles for Scientific Data Management and
Stewardship.” *Scientific Data* 3: 160018.
<https://doi.org/10.1038/sdata.2016.18>.
