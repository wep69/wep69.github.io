# Static audit — agriWaveInfer 1.0.0

**Result: 28/28 gates PASS**

| Gate | Status | Evidence |
|----|----|----|
| `description_version` | PASS | 1.0.0 |
| `minimum_R` | PASS | R (\>= 4.3.0) |
| `cff_version` | PASS | 1.0.0 |
| `codemeta_version` | PASS | 1.0.0 |
| `package_citation_version` | PASS | inst/CITATION |
| `export_definition_reconciliation` | PASS | 73 exports; missing=\[\] |
| `export_count_frozen` | PASS | 73 |
| `api_freeze_matches_namespace` | PASS | 73 frozen vs 73 namespace |
| `rd_alias_reconciliation` | PASS | missing=\[\] |
| `s3_method_reconciliation` | PASS | 42 methods; missing=\[\] |
| `R_delimiter_static_balance` | PASS | all balanced by static lexer |
| `no_provisional_markers` | PASS | none |
| `json_validity` | PASS | all active JSON valid |
| `reference_doi_reconciliation` | PASS | BibTeX=31 RIS=31 diff=\[\] |
| `vignette_citation_reconciliation` | PASS | used=28 keys=31 missing=\[\] |
| `synthetic_data_checksums` | PASS | 10 checksum files; failures=\[\] |
| `golden_manifest_hashes` | PASS | 14 entries; failures=\[\] |
| `paper_scaffold_sections` | PASS | all required scaffold sections present |
| `causal_safeguards_present` | PASS | present in all designated modules |
| `optional_backend_policy` | PASS | Imports=S7 (\>= 0.2.2) |
| `release_document_set` | PASS | 9 documents present |
| `history_0.9_preserved` | PASS | inst/history/0.9.0 |
| `test_inventory` | PASS | 51 |
| `vignette_inventory` | PASS | 18 |
| `release_regression_test` | PASS | test-release-1.0.R |
| `release_source_smoke` | PASS | release_source_smoke_1.0.0.R |
| `license_present` | PASS | LICENSE present |
| `no_stale_0.9_release_metadata` | PASS | none |

## Runtime boundary

This audit is source/static evidence. R is unavailable in the
construction environment, so package loading, testthat execution,
backend differential tests, vignette rendering, exact-tarball
installation, `R CMD build`, and `R CMD check --as-cran` remain
`NOT_RUN`.
