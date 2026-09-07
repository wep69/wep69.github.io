# Windows validation — agriWaveInfer 1.0.0

## Reference environment

Use R 4.6.1 (reference release for 1.0.0 certification), Rtools matching
that R release, and a fresh user library. The package minimum remains R
\>= 4.3.0.

## 1. Verify archive hash

Compare the downloaded archive with
`agriWaveInfer_1.0.0_ARCHIVE_SHA256_FINAL.txt`. Do not continue with a
mismatched archive.

## 2. Clean-library install

``` r

dir.create("C:/temp/awi-lib", recursive = TRUE, showWarnings = FALSE)
.libPaths(c("C:/temp/awi-lib", .libPaths()))
install.packages(c("S7", "testthat", "knitr", "rmarkdown"))
install.packages(c("biwavelet", "WaveletComp", "vectorwavelet", "igraph", "terra", "stars", "digest"))
install.packages("agriWaveInfer_1.0.0.tar.gz", repos = NULL, type = "source")
library(agriWaveInfer)
packageVersion("agriWaveInfer")
awi_capabilities()
```

Expected package version: `1.0.0`.

## 3. Unit, property, metamorphic and invariant tests

From the unpacked source tree:

``` r

testthat::test_dir("tests/testthat", reporter = "summary")
```

No ERROR/FAIL/WARN result should be accepted without investigation.

## 4. Truth-known and numerical validation

Run every script under `inst/validation/`, especially surrogate
invariants, type-I field benchmark, driver-selection stability,
phase/lag recovery, phenology/propagation recovery, network topology
recovery, spatial recovery, and publication-layer smoke tests. Compare
outputs with `GOLDEN_MANIFEST_1.0.0.json` and the JSON reference files
under `inst/validation/`.

## 5. Differential validation

Confirm CWT/XWT/WTC against `biwavelet`, legacy PWC compatibility where
mathematically defined, and MWC comparison against `vectorwavelet`.
Reconcile parameterization differences (smoothing, scale-period
conversion, normalization and COI) before interpreting discrepancies.

## 6. Vignettes

``` r

files <- list.files("vignettes", pattern = "[.]Rmd$", full.names = TRUE)
for (f in files) rmarkdown::render(f, quiet = TRUE)
```

All 18 vignettes must render in a clean environment.

## 7. Exact-tarball package checks

In a Windows terminal with Rtools available:

``` text
R CMD build agriWaveInfer_1.0.0
R CMD check --as-cran agriWaveInfer_1.0.0.tar.gz
```

Repeat on the exact final tarball. Archive and preserve the complete
check directory/logs.

## 8. Minimum compatibility

Repeat core load/test checks on R 4.3.x if minimum-version support is
claimed. Optional backend versions may differ by R release; record them
via
[`awi_capabilities()`](https://wep69.github.io/agriWaveInfer/reference/awi_capabilities.md)
and [`sessionInfo()`](https://rdrr.io/r/utils/sessionInfo.html).

## 9. Freeze condition

Runtime certification can be declared only when exact-archive
installation, tests, truth-known validation, vignettes and
`R CMD check --as-cran` are all reviewed and the validation ledger is
updated from `NOT_RUN` to evidence-backed statuses.
