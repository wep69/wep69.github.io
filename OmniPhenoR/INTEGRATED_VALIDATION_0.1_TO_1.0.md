# Integrated Validation: OmniPhenoR 0.1.0 through 1.0.0

Date: 2026-08-24

## Validation scope

This report validates the five historical source/release lines together
and constructs the 1.0.0 consolidated source. The current execution
environment does **not** contain an R runtime, so two validation layers
are distinguished:

1.  **Completed here:** archive/source inspection, namespace
    integration, API regression analysis, R-source structural scanning,
    documentation coverage, test inventory, vignette inventory,
    precompiled-document inventory, reference ledger integrity, BibTeX
    citation integrity, and cross-version compatibility.
2.  **Required locally before freeze:** `roxygen2`, real R
    parsing/loading, `testthat`, authoritative R/knitr rendering,
    `R CMD build`, `R CMD check`, and `R CMD check --as-cran`.

## Cross-version inventory

| Version | Blocks | Exports |  S3 | Tests | test_that | expect\_\* | Rmd | HTML | References |
|---------|-------:|--------:|----:|------:|----------:|-----------:|----:|-----:|-----------:|
| 0.1.0   |    n/a |      33 |   6 |     6 |        18 |         48 |   5 |    5 |         15 |
| 0.2.0   |     72 |      67 |  12 |    14 |        39 |        106 |  13 |   13 |         20 |
| 0.3.0   |    122 |     114 |  14 |    23 |        68 |        177 |  23 |   23 |         25 |
| 0.4.0   |    180 |     151 |  18 |    31 |       109 |        283 |  33 |   33 |         37 |
| 0.5.0   |    237 |     215 |  17 |    40 |       102 |        294 |  43 |   43 |         39 |
| 1.0.0   |    240 |     223 |  20 |    42 |       131 |        355 |  44 |   44 |         50 |

The 1.0.0 block registry is the true union of the richer 0.4.0
longitudinal registry (58 blocks) and the 0.5.0 infrastructure registry
(60 blocks):

``` text
32 + 40 + 50 + 58 + 60 = 240 blocks
```

## API regression detected and corrected

0.1.0 -\> 0.2.0: no removed exports.

0.2.0 -\> 0.3.0: no removed exports.

0.3.0 -\> 0.4.0: no removed exports.

0.4.0 -\> 0.5.0: **8 removed exports** and **3 removed S3 methods** were
detected.

Removed exports:

``` text
pheno_correlation_structures
pheno_curve_compare
pheno_event
pheno_event_response
pheno_growth
pheno_model_diagnostics
pheno_phenology
pheno_series_qc
```

Removed S3 methods:

``` text
print.pheno_functional
print.pheno_growth
print.pheno_repeated_fit
```

Version 1.0.0 restores all of them. Every public export observed in any
of the five historical versions is present in 1.0.0.

The audit also identified **28 changed overlapping signatures** between
the 0.4.0 and 0.5.0 development sources. The stable 1.0 interface
therefore keeps the richer 0.4 longitudinal forms for overlapping names
and retains the non-conflicting 0.5 convenience functions separately.

## 1.0.0 static validation result

``` text
public exports                          223
export definitions missing               0
documented export aliases               223
export documentation missing             0
S3 methods                               20
S3 method definitions missing             0
duplicate R function definitions          0
R bracket/string structural issues        0

test files                               42
test_that blocks                         131
expect_* calls                           355

Rmd vignettes                            44
precompiled vignette HTML                44
vignettes missing metadata headers        0
vignettes missing precompiled HTML        0
missing cited BibTeX keys                  0

reference ledger records                 50
records missing source_1/source_2          0
duplicate reference keys                   0
```

Static integrated validation status: **PASS**.

## Standards check

The consolidated source records MIAPPE 1.2 and BrAPI v2.1 as the current
interoperability targets. These are living standards and must be
rechecked at the final release date. FAIR functionality remains an
evidence audit rather than a claim of certification.

## Vignette policy

The consolidated source contains v00 through v43. Installed HTML is
distributed under `inst/doc/` to support normal GitHub installation with
`build_vignettes = FALSE`.

The preview HTML is **not** the final release proof. Before the 1.0.0
freeze, all 44 Rmd sources must be rebuilt with R/knitr/rmarkdown and
the resulting HTML must replace preview artifacts.

## Freeze decision

The source is suitable as a **1.0.0 consolidated source candidate**, not
yet as a frozen CRAN release. The exact release designation is permitted
only after the local procedure in `LOCAL_VALIDATION_1.0.0.md` completes
successfully on the exact final tarball.
