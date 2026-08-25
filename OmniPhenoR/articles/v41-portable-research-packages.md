# Portable Research Packages, Snapshots, and Dataset Differences

## Why this capability exists

A portable data bundle separates the experiment model from one machine
and records enough metadata to move, archive, inspect, and compare
research states.

Version 0.5.0 treats experiment metadata and storage as scientific
infrastructure. The data model is intentionally R-first and
offline-first. Optional database, Arrow, JSON, and HTTP backends are
activated only when the corresponding capability is explicitly
requested.

Relevant functions in this vignette include:

- [`pheno_pack()`](https://wep69.github.io/OmniPhenoR/reference/pheno_pack.md)
- [`pheno_pack_open()`](https://wep69.github.io/OmniPhenoR/reference/pheno_pack_open.md)
- [`pheno_snapshot()`](https://wep69.github.io/OmniPhenoR/reference/pheno_snapshot.md)
- [`pheno_diff()`](https://wep69.github.io/OmniPhenoR/reference/pheno_diff.md)
- [`pheno_dataset_readme()`](https://wep69.github.io/OmniPhenoR/reference/pheno_dataset_readme.md)
- [`pheno_citation()`](https://wep69.github.io/OmniPhenoR/reference/pheno_citation.md)

## Conceptual workflow

![Conceptual 0.5.0
workflow](../inst/extdata/vignette-results/figures/v41-portable-research-packages.png)

Conceptual 0.5.0 workflow

The illustration is a local package asset. It is distributed with the
vignette so that the installed documentation remains useful without
internet access.

## Worked agronomic example

``` r

exp <- pheno_experiment(
  "maize_growth",
  "Maize longitudinal growth",
  tables = list(
    traits = data.frame(
      plot_id = c("P1", "P2"),
      max_leaf_area = c(120, 133)
    )
  )
)

before <- pheno_snapshot(exp, "before_export")

bundle <- file.path(tempdir(), "maize_growth_bundle")
pheno_pack(exp, bundle, profile = "analysis", overwrite = TRUE)
restored <- pheno_pack_open(bundle)
restored$study
#> [1] "maize_growth"

pheno_dataset_readme(restored)
#>  [1] "# Maize longitudinal growth"                                        
#>  [2] ""                                                                   
#>  [3] "Study ID: `maize_growth`"                                           
#>  [4] "Experiment ID: `exp_d8fd6ea42f0c`"                                  
#>  [5] "OmniPhenoR schema: `0.5`"                                           
#>  [6] ""                                                                   
#>  [7] "## Contents"                                                        
#>  [8] "- Registered files: 0"                                              
#>  [9] "- Phenotyping variables: 0"                                         
#> [10] "- Germplasm records: 0"                                             
#> [11] ""                                                                   
#> [12] "## Reproducibility"                                                 
#> [13] "See metadata/ and checksum/provenance files in the portable bundle."
pheno_citation(restored)
#> $type
#> [1] "dataset"
#> 
#> $title
#> [1] "Maize longitudinal growth"
#> 
#> $identifier
#> [1] "exp_d8fd6ea42f0c"
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

after <- pheno_snapshot(restored, "after_export")
pheno_diff(before, after)
#> $summary
#> # A tibble: 1 × 3
#>   added removed changed
#>   <int>   <int>   <int>
#> 1     0       0       0
#> 
#> $added
#> character(0)
#> 
#> $removed
#> character(0)
#> 
#> $changed
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
#> 1 file_00cea3… trai… phen… csv       38 2bb75… NA             NA      2026-08-2…
pheno_verify_manifest(portable)
#> # A tibble: 1 × 6
#>   file_id           path     exists checksum_ok current_sha256 registered_sha256
#>   <chr>             <chr>    <lgl>  <lgl>       <chr>          <chr>            
#> 1 file_00cea310d370 traits.… TRUE   TRUE        2bb75ff315e6b… 2bb75ff315e6b251…
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
(2016).

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

Wilkinson, Mark D., Michel Dumontier, IJsbrand Jan Aalbersberg, et al.
2016. “The FAIR Guiding Principles for Scientific Data Management and
Stewardship.” *Scientific Data* 3: 160018.
<https://doi.org/10.1038/sdata.2016.18>.
