# Publication, Privacy Review, and Reuse Readiness

## Why this capability exists

Publication readiness is a collection of checks rather than a single
approval flag. Metadata completeness, licensing, provenance, file
integrity, local paths, personal data, and repository identifiers should
be reviewed before release.

Version 0.5.0 treats experiment metadata and storage as scientific
infrastructure. The data model is intentionally R-first and
offline-first. Optional database, Arrow, JSON, and HTTP backends are
activated only when the corresponding capability is explicitly
requested.

Relevant functions in this vignette include:

- [`pheno_privacy_audit()`](https://wep69.github.io/OmniPhenoR/reference/pheno_privacy_audit.md)
- [`pheno_publish_check()`](https://wep69.github.io/OmniPhenoR/reference/pheno_publish_check.md)
- [`pheno_miappe_validate()`](https://wep69.github.io/OmniPhenoR/reference/pheno_miappe_validate.md)
- [`pheno_fair_audit()`](https://wep69.github.io/OmniPhenoR/reference/pheno_fair_audit.md)
- [`pheno_manifest()`](https://wep69.github.io/OmniPhenoR/reference/pheno_manifest.md)
- [`pheno_citation()`](https://wep69.github.io/OmniPhenoR/reference/pheno_citation.md)

## Conceptual workflow

![Conceptual 0.5.0
workflow](../inst/extdata/vignette-results/figures/v42-publication-and-reuse.png)

Conceptual 0.5.0 workflow

The illustration is a local package asset. It is distributed with the
vignette so that the installed documentation remains useful without
internet access.

## Worked agronomic example

``` r

exp <- pheno_experiment(
  "release_candidate",
  "Reusable phenotyping dataset",
  metadata = list(
    description = "Example release candidate",
    repository = "institutional repository"
  )
)
exp$licenses <- pheno_license(
  "ccby4", "CC BY 4.0",
  "https://creativecommons.org/licenses/by/4.0/",
  applies_to = "data"
)
exp$access <- pheno_access_policy("open")

pheno_privacy_audit(exp)
#> # A tibble: 5 × 3
#>   check                 detected action                                         
#>   <chr>                 <lgl>    <chr>                                          
#> 1 email                 FALSE    Review whether personal contact should be publ…
#> 2 absolute_windows_path TRUE     Replace local absolute paths with relative pat…
#> 3 absolute_unix_path    FALSE    Replace local absolute paths with relative pat…
#> 4 token_keyword         FALSE    Remove credentials from objects/files          
#> 5 precise_coordinates   FALSE    Consider coordinate precision/privacy before p…
pheno_miappe_validate(exp)$summary
#> # A tibble: 1 × 5
#>   standard version required_missing recommended_missing complete
#>   <chr>    <chr>              <int>               <int> <lgl>   
#> 1 MIAPPE   1.2                    7                   9 FALSE
pheno_fair_audit(exp)
#> # A tibble: 13 × 4
#>    principle evidence status     recommendation                                 
#>    <chr>     <lgl>    <chr>      <chr>                                          
#>  1 F1        FALSE    incomplete Assign a persistent identifier                 
#>  2 F2        FALSE    incomplete Add rich study metadata                        
#>  3 F3        FALSE    incomplete Link metadata to registered files              
#>  4 F4        TRUE     pass       Register/index dataset in a repository         
#>  5 A1        TRUE     pass       Document retrieval/access protocol             
#>  6 A1.2      TRUE     pass       Document authentication/authorization if needed
#>  7 I1        FALSE    incomplete Use machine-readable structured metadata       
#>  8 I2        FALSE    incomplete Use resolvable controlled vocabulary identifie…
#>  9 I3        FALSE    incomplete Record qualified relations/lineage             
#> 10 R1        FALSE    incomplete Provide sufficient domain attributes           
#> 11 R1.1      TRUE     pass       Declare explicit data usage license            
#> 12 R1.2      TRUE     pass       Record detailed provenance                     
#> 13 R1.3      FALSE    incomplete Use community standards such as MIAPPE/BrAPI w…
pheno_publish_check(exp)
#> $checks
#> # A tibble: 4 × 3
#>   check                            pass  detail                          
#>   <chr>                            <lgl> <chr>                           
#> 1 MIAPPE required metadata         FALSE 7 required fields missing       
#> 2 FAIR license/provenance evidence TRUE  8 FAIR evidence items incomplete
#> 3 Manifest integrity               TRUE  Verify registered files         
#> 4 Privacy review                   FALSE Review detected privacy items   
#> 
#> $ready
#> [1] FALSE

# A publication check is diagnostic. Missing MIAPPE fields should be completed
# from the real experiment; they must never be invented automatically.
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
#> 1 file_056ff3… trai… phen… csv       38 2bb75… NA             NA      2026-08-2…
pheno_verify_manifest(portable)
#> # A tibble: 1 × 6
#>   file_id           path     exists checksum_ok current_sha256 registered_sha256
#>   <chr>             <chr>    <lgl>  <lgl>       <chr>          <chr>            
#> 1 file_056ff3a40c14 traits.… TRUE   TRUE        2bb75ff315e6b… 2bb75ff315e6b251…
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
verification sources. The relevant sources here include MIAPPE community
(2026) Wilkinson et al. (2016) GO FAIR initiative (2026).

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

MIAPPE community. 2026. *Minimum Information about a Plant Phenotyping
Experiment (MIAPPE) Checklist and Data Model V1.2*.
<https://github.com/MIAPPE/MIAPPE>.

Wilkinson, Mark D., Michel Dumontier, IJsbrand Jan Aalbersberg, et al.
2016. “The FAIR Guiding Principles for Scientific Data Management and
Stewardship.” *Scientific Data* 3: 160018.
<https://doi.org/10.1038/sdata.2016.18>.
