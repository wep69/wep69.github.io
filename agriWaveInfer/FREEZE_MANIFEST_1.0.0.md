# Freeze manifest — agriWaveInfer 1.0.0

## Release identity

- Package: `agriWaveInfer`
- Version: `1.0.0`
- Release label: **Consolidated Scientific Release**
- Source state: **SOURCE-FROZEN / RUNTIME-PENDING**
- Freeze date: 2026-09-05
- Public exports: **73**
- Registered S3 methods: **42**
- Vignettes: **18**
- Test files: **51**
- Bibliographic DOI set: **31**
- Synthetic/demo CSV files: **10**
- Golden/reference entries: **14**
- Source-tree files at final archive build: **375**

## Consolidation scope

Version 1.0.0 introduces no new methodological domain. It consolidates
the complete 0.1.0–0.9.0 scientific surface, freezes the API, reconciles
metadata, preserves historical release evidence, adds release-level
regression tests, and seals the source tree for exact-archive runtime
certification.

## Source evidence at freeze

- Static audit: **28/28 PASS**.
- Deterministic data regeneration: **10/10 CSVs exact SHA-256 match**.
- BibTeX/RIS DOI reconciliation: **31/31-set agreement**.
- Golden/reference hash verification: **14/14 entries**.
- Causal interpretation safeguards retained across phase, propagation,
  network, spatial and reporting modules.

## Runtime boundary

R and Rscript are unavailable in the construction environment. Therefore
package load, `testthat`, backend differential tests, truth-known
recovery through the actual R engines, vignette rendering, clean-library
installation, `R CMD build`, and `R CMD check --as-cran` remain
`NOT_RUN`. They must not be represented as passing until executed on the
exact final archive.

## Archive policy

Final ZIP and TAR.GZ archives are produced deterministically and
verified by extraction plus file-level SHA-256 comparison against this
source tree. External archive SHA-256 values are stored outside the
package tree in `agriWaveInfer_1.0.0_ARCHIVE_SHA256_FINAL.txt`.
