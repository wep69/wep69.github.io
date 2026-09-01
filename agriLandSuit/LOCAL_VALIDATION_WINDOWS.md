# Local validation on Windows — agriLandSuit 1.0.0

The creator/maintainer metadata are complete
(`walterufpb@yahoo.com.br`). The remaining certification steps are
executable checks that must be run on Windows because R is not installed
in the assembly environment.

For a more detailed Portuguese, command-by-command procedure, use
`VALIDACAO_LOCAL_WINDOWS_1.0.0.md` in the package root.

## 1. Prerequisites

Install current 64-bit R for Windows and the matching Rtools release.
Restart the terminal after installation. Use a short writable path such
as `D:\\temp\\agriLandSuit_1.0.0`.

Verify from a fresh R session:

``` r

R.version.string
Sys.which(c("R", "Rscript", "make", "gcc"))
```

## 2. R-only dependency pass

First validate that the package does not require Python. Install the
core and documentation/test dependencies, but leave `reticulate`
uninstalled in this first isolated library if practical.

``` r

install.packages(c(
  "terra", "digest", "testthat", "roxygen2", "knitr", "rmarkdown",
  "jsonlite", "yaml", "units", "targets", "geotargets"
))
```

## 3. Static assembly validation

From PowerShell or Command Prompt:

``` bat
python D:\temp\agriLandSuit_1.0.0\agriLandSuit\tools\static_validate.py
python D:\temp\agriLandSuit_1.0.0\agriLandSuit\tools\validate_core_reference_1.0.0.py
```

Expected: static validation passes with 82 exports and zero removed
0.9.0 exports; numeric reference validation passes.

## 4. Regenerate documentation

``` r

pkg <- "D:/temp/agriLandSuit_1.0.0/agriLandSuit"
roxygen2::roxygenise(pkg)
```

Re-run `tools/static_validate.py` afterwards. Any unexpected
NAMESPACE/Rd difference must be investigated before building.

## 5. Run the complete R-only tests

``` r

testthat::test_local(pkg, reporter = "progress")
```

All non-Python tests must pass. Optional Python tests should skip when
`reticulate` or the required Python modules are unavailable.

## 6. Render all vignettes

``` r

vigs <- list.files(file.path(pkg, "vignettes"), pattern = "\\.Rmd$", full.names = TRUE)
stopifnot(length(vigs) == 10L)
for (v in vigs) rmarkdown::render(v, quiet = FALSE)
```

## 7. Optional Python parity pass

Python is optional. For the active 1.0.0 optional backends, install
`numpy`, `scikit-fuzzy`, and `pymcdm` in a dedicated environment, then
install/load `reticulate` and verify:

``` r

reticulate::py_config()
python_backend_status(c("fuzzy", "mcda", "sampling"))
testthat::test_local(pkg, reporter = "progress")
```

The public R API and result classes must remain unchanged regardless of
backend.

## 8. Build and CRAN-style check

From the parent directory:

``` bat
R CMD build agriLandSuit
R CMD check --as-cran agriLandSuit_1.0.0.tar.gz
```

Open `agriLandSuit.Rcheck/00check.log`. Do not certify the release with
any ERROR or WARNING. Every NOTE must be understood and documented.

## 9. Install and smoke-test the built tarball

``` bat
R CMD INSTALL --preclean agriLandSuit_1.0.0.tar.gz
```

Then in a new R session:

``` r

library(agriLandSuit)
packageVersion("agriLandSuit")
python_backend_status()
```

Run the complete workflow vignette and a reproducibility/fingerprint
smoke test.

## 10. Final local freeze

After a clean local check:

``` powershell
Get-FileHash .\agriLandSuit_1.0.0.tar.gz -Algorithm SHA256
Rscript -e "writeLines(capture.output(sessionInfo()), 'SESSION_INFO_1.0.0.txt')"
```

Record the checksum of the **locally built R tarball** separately. It is
the checksum of the certified build and must not be replaced by the
source-snapshot checksum assembled in ChatGPT.
