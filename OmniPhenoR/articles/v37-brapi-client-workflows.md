# BrAPI v2 Client Workflows with Offline Dry-Run Validation

## Why this capability exists

BrAPI is a standardized REST API for breeding data exchange. OmniPhenoR
0.5.0 implements a client-side mapping layer and keeps all network
activity opt-in. Examples, vignettes, and CRAN checks use dry-run
requests or frozen fixtures.

Version 0.5.0 treats experiment metadata and storage as scientific
infrastructure. The data model is intentionally R-first and
offline-first. Optional database, Arrow, JSON, and HTTP backends are
activated only when the corresponding capability is explicitly
requested.

Relevant functions in this vignette include:

- [`pheno_brapi()`](https://wep69.github.io/OmniPhenoR/reference/pheno_brapi.md)
- [`pheno_brapi_status()`](https://wep69.github.io/OmniPhenoR/reference/pheno_brapi_status.md)
- [`pheno_brapi_capabilities()`](https://wep69.github.io/OmniPhenoR/reference/pheno_brapi_capabilities.md)
- [`pheno_brapi_call()`](https://wep69.github.io/OmniPhenoR/reference/pheno_brapi_call.md)
- [`pheno_brapi_export()`](https://wep69.github.io/OmniPhenoR/reference/pheno_brapi_export.md)
- [`pheno_brapi_import()`](https://wep69.github.io/OmniPhenoR/reference/pheno_brapi_import.md)

## Conceptual workflow

![Conceptual 0.5.0
workflow](../inst/extdata/vignette-results/figures/v37-brapi-client-workflows.png)

Conceptual 0.5.0 workflow

The illustration is a local package asset. It is distributed with the
vignette so that the installed documentation remains useful without
internet access.

## Worked agronomic example

``` r

client <- pheno_brapi(
  "https://example.org",
  version = "2.1"
)
pheno_brapi_status(client)
#> # A tibble: 1 × 4
#>   base_url            version has_token httr2_available
#>   <chr>               <chr>   <lgl>     <lgl>          
#> 1 https://example.org 2.1     FALSE     TRUE
pheno_brapi_capabilities(client)
#> # A tibble: 9 × 4
#>   module      entity               endpoint                   direction 
#>   <chr>       <chr>                <chr>                      <chr>     
#> 1 Core        Studies              /brapi/v2/studies          read/write
#> 2 Core        Locations            /brapi/v2/locations        read/write
#> 3 Germplasm   Germplasm            /brapi/v2/germplasm        read/write
#> 4 Phenotyping ObservationUnits     /brapi/v2/observationunits read/write
#> 5 Phenotyping Observations         /brapi/v2/observations     read/write
#> 6 Phenotyping ObservationVariables /brapi/v2/variables        read/write
#> 7 Phenotyping Traits               /brapi/v2/traits           read/write
#> 8 Phenotyping Methods              /brapi/v2/methods          read/write
#> 9 Phenotyping Scales               /brapi/v2/scales           read/write

# No request is sent. This returns a fully inspectable request specification.
req <- pheno_brapi_call(
  client,
  endpoint = "/brapi/v2/studies",
  method = "GET",
  dry_run = TRUE
)
req
#> $url
#> [1] "https://example.org/brapi/v2/studies"
#> 
#> $method
#> [1] "GET"
#> 
#> $body
#> NULL
#> 
#> $version
#> [1] "2.1"
#> 
#> $authenticated
#> [1] FALSE
#> 
#> attr(,"class")
#> [1] "pheno_brapi_request"

exp <- pheno_experiment("S1", "Soybean trial")
payload <- pheno_brapi_export(exp)
payload$study
#> $studyDbId
#> [1] "S1"
#> 
#> $studyName
#> [1] "Soybean trial"

fixture <- list(study = list(studyDbId = "S2", studyName = "Imported trial"))
pheno_brapi_import(fixture)
#> <pheno_experiment> S2 
#>   id: exp_387bcb0bca08  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0
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
#> 1 file_3b3c1e… trai… phen… csv       38 2bb75… NA             NA      2026-08-2…
pheno_verify_manifest(portable)
#> # A tibble: 1 × 6
#>   file_id           path     exists checksum_ok current_sha256 registered_sha256
#>   <chr>             <chr>    <lgl>  <lgl>       <chr>          <chr>            
#> 1 file_3b3c1e6d6400 traits.… TRUE   TRUE        2bb75ff315e6b… 2bb75ff315e6b251…
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
verification sources. The relevant sources here include Selby et al.
(2019) Selby et al. (2025).

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

Selby, Peter, Rafael Abbeloos, Anne-Francoise Adam-Blondon, others, and
BrAPI Consortium. 2025. “BrAPI V2: Real-World Applications for Data
Integration and Collaboration in the Breeding and Genetics Community.”
*Database* 2025: baaf048. <https://doi.org/10.1093/database/baaf048>.

Selby, Peter, Rafael Abbeloos, Jan Erik Backlund, others, and BrAPI
consortium. 2019. “BrAPI—an Application Programming Interface for Plant
Breeding Applications.” *Bioinformatics* 35 (20): 4147–55.
<https://doi.org/10.1093/bioinformatics/btz190>.
