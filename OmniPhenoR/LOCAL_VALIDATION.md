# OmniPhenoR 1.0.0 — Detailed Local Validation Protocol

## Purpose

This protocol is the authoritative local procedure for validating the
five historical OmniPhenoR generations together and freezing the
consolidated 1.0.0 release.

The validation must distinguish:

- **historical evidence**: 0.1.0 through 0.5.0;
- **source-level validation**: the consolidated 1.0.0 tree;
- **release-artifact validation**: the exact `OmniPhenoR_1.0.0.tar.gz`;
- **optional backend validation**: Python, torch,
  PlantCV/YOLO/LeafMachine2, Arrow, DuckDB, and mixed-model backends.

Do not call the release frozen until the exact final tarball has passed
the required checks and its SHA-256 has been recorded.

------------------------------------------------------------------------

# 1. Recommended directory layout

Create:

``` text
D:\temp\OmniPhenoR_validation\
├── historical\
│   ├── OmniPhenoR_0.1.0.tar.gz
│   ├── OmniPhenoR_0.2.0_clean_rc.tar.gz
│   ├── OmniPhenoR_0.3.0_source\
│   ├── OmniPhenoR_0.4.0_source\
│   └── OmniPhenoR_0.5.0_source\
├── OmniPhenoR\
│   └── [contents of OmniPhenoR_1.0.0_source.zip]
├── libraries\
│   ├── v010\
│   ├── v020\
│   ├── v030\
│   ├── v040\
│   ├── v050\
│   └── v100\
└── checks\
```

Keep historical versions isolated. Do not overwrite the frozen 0.1.0
tarball.

------------------------------------------------------------------------

# 2. Software prerequisites

Recommended:

``` text
R >= 4.3.0
Rtools matching the installed R version on Windows
Pandoc
Git
```

For the complete developer validation install:

``` r

install.packages(c(
  "roxygen2",
  "testthat",
  "knitr",
  "rmarkdown",
  "pkgload",
  "devtools",
  "remotes",
  "pak"
))
```

Optional backend packages should be installed separately so failures in
optional infrastructure do not obscure the core check.

The core package should remain installable without Python, CUDA, Arrow,
DuckDB, or mixed-model packages.

------------------------------------------------------------------------

# 3. Confirm R and Rtools

In PowerShell:

``` powershell
R --version
R CMD config CC
```

In R:

``` r

R.version.string
Sys.which(c("make", "gcc", "pandoc"))
```

If R is not on `PATH`, locate it explicitly, for example:

``` powershell
$R = "C:\Program Files\R\R-4.6.0\bin\R.exe"
& $R --version
```

Use your actual installed version.

------------------------------------------------------------------------

# 4. Verify historical immutable artifacts

The known 0.1.0 frozen artifact is:

``` text
OmniPhenoR_0.1.0.tar.gz
SHA-256
4e7970127cb8e4f1d2e521d949909d21e3ddfe915bff9bbcd4b84b9595ed12a6
```

The cleaned 0.2.0 release-candidate artifact currently supplied is:

``` text
OmniPhenoR_0.2.0_clean_rc.tar.gz
SHA-256
06679a79dc948016768aa3ff6ed0130ffe7ffc38538b9e715751760775488b30
```

Check:

``` powershell
Get-FileHash .\historical\OmniPhenoR_0.1.0.tar.gz -Algorithm SHA256
Get-FileHash .\historical\OmniPhenoR_0.2.0_clean_rc.tar.gz -Algorithm SHA256
```

If a historical immutable artifact has a different hash, stop and
determine why before using it as regression evidence.

The precompiled-vignette retrofit sources for 0.1.0/0.2.0 are separate
source snapshots and must not be confused with the original frozen
tarballs.

------------------------------------------------------------------------

# 5. Historical version checks

The expected documentation progression is:

``` text
0.1.0:  5 Rmd vignettes
0.2.0: 13 Rmd vignettes
0.3.0: 23 Rmd vignettes
0.4.0: 33 Rmd vignettes
0.5.0: 43 Rmd vignettes
1.0.0: 44 Rmd vignettes
```

## 5.1 Check 0.1.0 exact tarball

``` powershell
& $R CMD check --as-cran .\historical\OmniPhenoR_0.1.0.tar.gz
```

The historical evidence previously recorded for this exact artifact was:

``` text
0 ERROR
0 WARNING
1 NOTE: New submission
```

Re-running on a newer R version may reveal new CRAN-policy or dependency
notes. Record them separately from regressions caused by package source.

## 5.2 Check 0.2.0 cleaned release candidate

``` powershell
& $R CMD check --as-cran .\historical\OmniPhenoR_0.2.0_clean_rc.tar.gz
```

The package source should no longer include validation scratch files.

## 5.3 Build/check 0.3.0, 0.4.0, and 0.5.0 source snapshots

For each source directory:

``` powershell
Set-Location D:\temp\OmniPhenoR_validation\historical
& $R CMD build .\OmniPhenoR_0.3.0_source
& $R CMD check --as-cran .\OmniPhenoR_0.3.0.tar.gz

& $R CMD build .\OmniPhenoR_0.4.0_source
& $R CMD check --as-cran .\OmniPhenoR_0.4.0.tar.gz

& $R CMD build .\OmniPhenoR_0.5.0_source
& $R CMD check --as-cran .\OmniPhenoR_0.5.0.tar.gz
```

These development snapshots were not frozen as CRAN-ready artifacts in
the construction environment. Treat new runtime findings as information
to feed into 1.0.0, not as proof that the historical snapshot was
already released.

------------------------------------------------------------------------

# 6. Cross-version API regression check

The integrated static audit found:

``` text
0.1 -> 0.2: no removed exports
0.2 -> 0.3: no removed exports
0.3 -> 0.4: no removed exports
0.4 -> 0.5: 8 removed exports and 3 removed S3 print methods
```

Version 1.0.0 restores the lost API.

After installing each version into its own library, save its exports:

``` r

writeLines(
  sort(getNamespaceExports("OmniPhenoR")),
  "exports.txt"
)
```

For 1.0.0, verify the supplied compatibility ledger:

``` r
ledger <- read.csv(
  system.file(
    "metadata",
    "api_compatibility_1.0.csv",
    package = "OmniPhenoR"
  )
)

stopifnot(
  all(ledger$function %in% getNamespaceExports("OmniPhenoR"))
)
```

The consolidated namespace should expose **223 functions**.

------------------------------------------------------------------------

# 7. Prepare a clean 1.0.0 source tree

Extract:

``` text
OmniPhenoR_1.0.0_source.zip
```

so that:

``` text
D:\temp\OmniPhenoR_validation\OmniPhenoR\DESCRIPTION
```

exists.

Delete accidental development artifacts if present:

``` powershell
Get-ChildItem .\OmniPhenoR -Recurse -Include `
  validation_*.log,validation_*.out,validation_*.err,validation_*.txt
```

The result should be empty.

Do not delete:

``` text
vignettes\
inst\doc\
inst\extdata\
inst\metadata\
data-raw\
```

------------------------------------------------------------------------

# 8. Validate DESCRIPTION and dependency policy

In R:

``` r

d <- read.dcf("DESCRIPTION")
d[1, c("Package", "Version", "Depends", "Imports", "Suggests")]
```

Expected:

``` text
Package: OmniPhenoR
Version: 1.0.0
```

Core imports should remain light. Optional heavy infrastructure belongs
in `Suggests`.

------------------------------------------------------------------------

# 9. Regenerate roxygen documentation

From the 1.0.0 package root:

``` r

roxygen2::roxygenise(
  load_code = roxygen2::load_source
)
```

Then inspect changes:

``` powershell
git diff -- NAMESPACE man DESCRIPTION
```

If roxygen materially changes `NAMESPACE` or `man/`, the source snapshot
was not internally synchronized. Review and keep the regenerated
authoritative files, then repeat all subsequent checks.

Critical targets:

``` text
223 exported functions
20 S3 methods
0 exported functions without documentation
```

------------------------------------------------------------------------

# 10. Run the full source-level test suite

``` r

testthat::test_local(
  stop_on_failure = TRUE
)
```

Also run the supplied helper:

``` r

source("tools/validate_source_1_0.R")
```

The static source currently contains:

``` text
42 test files
131 test_that blocks
355 expect_* calls
```

The runtime number of PASS assertions printed by `testthat` may differ
from the static `expect_*` count because one expectation call can
execute repeatedly or conditionally. The release criterion is zero
failures and no unexplained warnings.

------------------------------------------------------------------------

# 11. Verify the consolidated block registry

After
[`pkgload::load_all()`](https://pkgload.r-lib.org/reference/load_all.html):

``` r

pkgload::load_all()
blocks <- pheno_blocks()

stopifnot(nrow(blocks) == 240L)
table(blocks$introduced)
```

Expected conceptual counts:

``` text
0.1.0   32
0.2.0   40
0.3.0   50
0.4.0   58
0.5.0   60
```

Do not reduce the 0.4.0 count to the simplified 55-block development
registry that appeared in 0.5.0.

------------------------------------------------------------------------

# 12. Core smoke tests without optional infrastructure

Start a clean R session with no Python initialization.

``` r

pkgload::load_all()

img <- pheno_data("leaf_rgb")

idx <- pheno_rgb_indices(
  img,
  c("ExG", "GLI", "NGRDI")
)

mask <- pheno_segment(img)
morph <- pheno_morphology(mask)

stopifnot(length(idx) > 0)
stopifnot(length(morph) > 0)
```

Then test representative layers:

``` r

pheno_texture(pheno_data("leaf_gray"))
pheno_qc(img)
pheno_blocks()
pheno_capabilities()
```

The core must not download anything or require Python.

------------------------------------------------------------------------

# 13. Longitudinal consolidation tests

The stable canonical grouped workflow:

``` r

d <- subset(
  pheno_data("growth_series"),
  trait == "leaf_area" &
  plant_id %in% c("P01", "P02")
)

s <- pheno_series(
  d,
  id = "plant_id",
  time = "day",
  trait = "trait",
  value = "value"
)

pheno_series_validate(s)

g <- pheno_growth(
  d,
  time = "day",
  value = "value",
  model = "logistic",
  group = "plant_id"
)

pheno_growth_traits(g)
```

Verify restored 0.4 exports:

``` r

restored <- c(
  "pheno_correlation_structures",
  "pheno_curve_compare",
  "pheno_event",
  "pheno_event_response",
  "pheno_growth",
  "pheno_model_diagnostics",
  "pheno_phenology",
  "pheno_series_qc"
)

stopifnot(
  all(restored %in% getNamespaceExports("OmniPhenoR"))
)
```

Verify 0.5 convenience entry points retained:

``` r

pheno_gdd(
  c(8, 9, 12),
  c(20, 22, 25),
  base = 10
)

gf <- pheno_growth_fit(
  0:10,
  100 / (1 + exp(-(0:10 - 5))),
  "logistic"
)

pheno_growth_predict(gf, seq(0, 10, by = 0.5))

pheno_audpc(
  c(0, 7, 14),
  c(0, 10, 40)
)
```

------------------------------------------------------------------------

# 14. Experiment/data-infrastructure tests

Use temporary directories only.

``` r

tmp <- tempfile("omniphenor-validation-")
dir.create(tmp)
on.exit(unlink(tmp, recursive = TRUE), add = TRUE)
```

Exercise:

``` r

pheno_checksum()
pheno_manifest()
pheno_verify_manifest()
pheno_schema()
pheno_schema_validate()
pheno_snapshot()
pheno_diff()
pheno_pack()
pheno_pack_open()
```

Use the package tests as the exact argument examples.

Confirm:

- paths remain relocatable;
- SHA-256 changes when file content changes;
- manifest verification detects tampering;
- JSON round-trip preserves expected metadata;
- pack/open round-trip retains identifiers and metadata.

------------------------------------------------------------------------

# 15. MIAPPE validation

The current implementation target is **MIAPPE 1.2**.

Run the package’s offline fixture tests first:

``` r

testthat::test_file(
  "tests/testthat/test-miappe.R"
)
```

Then inspect:

``` r

pheno_miappe_mapping()
```

Validate a complete example and an intentionally incomplete example.

The validator must identify missing fields rather than inventing
metadata.

Before release, confirm the official MIAPPE current version again.

------------------------------------------------------------------------

# 16. BrAPI validation

The current target is **BrAPI v2.1**.

CRAN/core tests must use frozen fixtures or mocked responses only.

``` r

testthat::test_file(
  "tests/testthat/test-brapi.R"
)
```

Verify:

``` r

pheno_brapi_status()
pheno_brapi_capabilities()
```

without transmitting credentials or contacting a remote service during
package check.

Any live-server integration test belongs in a separate optional
validation run.

------------------------------------------------------------------------

# 17. FAIR and privacy validation

Run:

``` r

testthat::test_file(
  "tests/testthat/test-fair-publication.R"
)
```

Confirm that:

``` r

pheno_fair_audit()
```

returns evidence/status rather than claiming certification.

Confirm that:

``` r

pheno_privacy_audit()
```

can flag potentially sensitive metadata such as exact coordinates, local
absolute paths, credentials, or tokens where represented.

FAIR does not mean that all data must be public.

------------------------------------------------------------------------

# 18. Optional Arrow/Parquet validation

Only after the core suite passes:

``` r

install.packages("arrow")
```

Then test:

``` r

testthat::test_file(
  "tests/testthat/test-storage-schema.R"
)
```

Exercise a write/read round-trip and compare:

``` text
row count
column names
ID values
NA positions
dates
units
```

Arrow must remain optional.

------------------------------------------------------------------------

# 19. Optional DuckDB validation

Install:

``` r

install.packages(c("DBI", "duckdb"))
```

Exercise:

``` r

pheno_db()
pheno_db_register()
pheno_db_query()
pheno_query()
```

Close every connection after tests.

DuckDB must remain optional.

------------------------------------------------------------------------

# 20. Optional mixed-model validation

Install as needed:

``` r

install.packages(c(
  "nlme",
  "lme4",
  "glmmTMB",
  "emmeans",
  "mgcv"
))
```

Test representative longitudinal fits with:

``` r

pheno_repeated()
pheno_repeated_compare()
pheno_time_contrasts()
pheno_model_diagnostics()
```

Confirm that biological subject identity is respected.

------------------------------------------------------------------------

# 21. Optional R torch validation

Only after the core check passes.

``` r

library(torch)
torch::torch_manual_seed(404)
```

Run the dedicated 0.2/0.3 tests for:

``` text
dataset specification
group-aware split
augmentation
CNN
U-Net
prediction object
uncertainty
segmentation metrics
tiling
```

Do not require CUDA. CPU validation is mandatory; GPU validation is
additional.

------------------------------------------------------------------------

# 22. Optional Python/backend validation

Python validation is separate from CRAN/core validation.

Check:

``` r

pheno_python_status()
pheno_python_packages()
pheno_python_validate()
```

Then, only in an explicitly prepared environment, test the installed
adapters for the backends actually available.

Do not automatically install Python packages during package check.

Record:

``` text
Python version
executable
reticulate version
backend versions
CUDA version
GPU
model hashes
```

------------------------------------------------------------------------

# 23. Authoritatively rebuild all 44 vignettes

This step is mandatory for the 1.0 freeze.

The current source includes precompiled previews so ordinary
installation can be fast. For release validation, rebuild from Rmd.

From the package root:

``` r

old <- list.files(
  "inst/doc",
  pattern = "^v[0-9][0-9]-.*[.]html$",
  full.names = TRUE
)

unlink(old)

devtools::build_vignettes()
```

Verify:

``` r

rmd <- list.files(
  "vignettes",
  pattern = "^v[0-9][0-9]-.*[.]Rmd$"
)

html <- list.files(
  "inst/doc",
  pattern = "^v[0-9][0-9]-.*[.]html$"
)

length(rmd)
length(html)

stopifnot(length(rmd) == 44L)
stopifnot(length(html) == 44L)
```

Every figure must render locally without internet access.

No vignette may:

``` text
download a model
pip install
conda install
git clone
contact a live API
train a large model
require CUDA
```

during release rendering.

------------------------------------------------------------------------

# 24. Visually inspect representative vignettes

At minimum inspect:

``` text
v00-foundations-to-advanced-tutorial
v05-deep-learning-foundations
v13-python-backends
v23-longitudinal-phenotyping
v33-experiment-data-model
v36-miappe-interoperability
v38-fair-data-and-metadata
v43-omniphenor-1.0-integrated-workflow
```

Check:

- no missing figures;
- no broken code formatting;
- no clipped tables;
- equations render;
- references resolve;
- headings/toc are correct;
- heavy examples are clearly marked as frozen/optional when not
  executed.

------------------------------------------------------------------------

# 25. Recompute the vignette artifact ledger

After authoritative rendering, recompute:

``` text
source_hash
output_hash
```

in:

``` text
inst/metadata/vignette_artifacts.csv
```

Do not leave hashes pointing to preview HTML after the final render.

------------------------------------------------------------------------

# 26. Build the exact 1.0.0 tarball

Return to the parent directory:

``` powershell
Set-Location D:\temp\OmniPhenoR_validation

& $R CMD build .\OmniPhenoR
```

Expected artifact:

``` text
OmniPhenoR_1.0.0.tar.gz
```

Do not use the development `source_bundle.tar.gz` as if it were an
`R CMD build` artifact.

------------------------------------------------------------------------

# 27. Inspect tarball contents before check

``` powershell
tar -tzf .\OmniPhenoR_1.0.0.tar.gz > .\checks\tarball_contents.txt
```

Confirm that it contains:

``` text
DESCRIPTION
NAMESPACE
R/
man/
tests/
vignettes/
inst/doc/
inst/extdata/
inst/metadata/
```

Confirm that it does **not** contain:

``` text
*.Rcheck/
validation_*.log
validation_*.out
validation_*.err
validation_*.txt
.Rproj.user/
temporary caches
credentials
tokens
large external model weights
```

------------------------------------------------------------------------

# 28. Run CRAN-style check on the exact tarball

``` powershell
& $R CMD check --as-cran .\OmniPhenoR_1.0.0.tar.gz
```

Then also:

``` powershell
& $R CMD check .\OmniPhenoR_1.0.0.tar.gz
```

Release criterion:

``` text
0 ERROR
0 WARNING
0 package-caused NOTE
```

If CRAN reports only the administrative `New submission` NOTE, record it
separately.

Any source change after this point requires a complete rebuild/check
cycle.

------------------------------------------------------------------------

# 29. Install the exact checked tarball into a clean library

Create a clean library:

``` powershell
New-Item -ItemType Directory -Force .\libraries\v100
```

Install:

``` powershell
& $R CMD INSTALL `
  --library="D:\temp\OmniPhenoR_validation\libraries\v100" `
  .\OmniPhenoR_1.0.0.tar.gz
```

Start R with that library first:

``` r

.libPaths(c(
  "D:/temp/OmniPhenoR_validation/libraries/v100",
  .libPaths()
))

source(
  "D:/temp/OmniPhenoR_validation/OmniPhenoR/tools/validate_installed_1_0.R"
)
```

------------------------------------------------------------------------

# 30. Confirm precompiled vignettes are installed without rebuild

The most important documentation test:

``` r

library(OmniPhenoR)

v <- vignette(package = "OmniPhenoR")
v$results

stopifnot(nrow(v$results) >= 44L)

vignette(
  "v43-omniphenor-1.0-integrated-workflow",
  package = "OmniPhenoR"
)
```

Also test a source-directory installation with rebuilding disabled:

``` r

remotes::install_local(
  "D:/temp/OmniPhenoR_validation/OmniPhenoR",
  build_vignettes = FALSE,
  force = TRUE
)

vignette(package = "OmniPhenoR")
```

After publication on GitHub, test:

``` r

remotes::install_github(
  "wep69/OmniPhenoR",
  build_vignettes = FALSE,
  force = TRUE
)
```

The 44 vignettes must be available immediately.

------------------------------------------------------------------------

# 31. Test GitHub installation with pak

After the repository is published:

``` r

pak::pak("wep69/OmniPhenoR")
```

Confirm the installed package and vignettes are available.

Document whether `pak` used a source build or cached/binary artifact in
the validation report.

------------------------------------------------------------------------

# 32. Recheck reference metadata

Read:

``` text
REFERENCE_AUDIT_1.0.0.md
inst/metadata/reference_verification.csv
```

Programmatically verify:

``` r

refs <- read.csv(
  "inst/metadata/reference_verification.csv",
  stringsAsFactors = FALSE
)

stopifnot(!anyDuplicated(refs$key))
stopifnot(all(nzchar(refs$source_1)))
stopifnot(all(nzchar(refs$source_2)))
```

Living standards must be rechecked from their official sites on the
final release date.

------------------------------------------------------------------------

# 33. Final reproducibility-bundle smoke test

Using a small temporary experiment, generate:

``` r

pheno_manifest()
pheno_snapshot()
pheno_dataset_readme()
pheno_reproducibility_bundle()
```

Verify every file reference is relocatable and every checksum is
reproducible.

------------------------------------------------------------------------

# 34. Final source cleanliness check

PowerShell:

``` powershell
Get-ChildItem .\OmniPhenoR -Recurse |
  Where-Object {
    $_.Name -match "validation_.*\.(log|out|err|txt)$" -or
    $_.FullName -match "\.Rcheck" -or
    $_.FullName -match "\.Rproj\.user"
  }
```

Expected: no release contamination.

Search for obvious secrets:

``` powershell
Select-String `
  -Path .\OmniPhenoR\**\* `
  -Pattern "API_KEY|TOKEN|PASSWORD|SECRET" `
  -ErrorAction SilentlyContinue
```

Inspect every match manually. Documentation words such as “token” are
not automatically credentials.

------------------------------------------------------------------------

# 35. Create the final freeze record

Only after all required validation passes:

``` powershell
Get-FileHash `
  .\OmniPhenoR_1.0.0.tar.gz `
  -Algorithm SHA256
```

Create:

``` text
FREEZE_1.0.0.md
```

outside the source tarball or explicitly excluded through
`.Rbuildignore`.

Record:

``` text
release version
date
R version
platform
R CMD check status
R CMD check --as-cran status
test count/status
vignette count/status
optional backend matrix
tarball SHA-256
Git commit
reference-ledger status
MIAPPE/BrAPI target versions
```

------------------------------------------------------------------------

# 36. Absolute freeze rule

If any of the following changes after the final hash:

``` text
DESCRIPTION
NAMESPACE
R/
man/
tests/
vignettes/
inst/
data/
```

the existing release hash is invalid.

Repeat:

``` text
roxygen
→ tests
→ vignette render
→ build
→ check
→ install exact tarball
→ vignette availability
→ SHA-256
```

There are no exceptions for “small documentation fixes” if those files
are part of the release archive.

------------------------------------------------------------------------

# 37. Final release acceptance criteria

The 1.0.0 release is accepted locally only when all of these are true:

``` text
[ ] 223 public exports available
[ ] all five historical export sets are subsets of 1.0
[ ] 20 S3 methods available
[ ] 240 implementation blocks
[ ] full test suite passes
[ ] 44/44 Rmd vignettes render authoritatively
[ ] 44/44 installed vignettes available without rebuild
[ ] reference ledger has two sources per promoted record
[ ] MIAPPE target rechecked
[ ] BrAPI target rechecked
[ ] core check succeeds without Python/network/GPU
[ ] optional backend failures do not break core installation
[ ] exact tarball passes R CMD check
[ ] exact tarball passes R CMD check --as-cran
[ ] exact checked tarball installs into a clean library
[ ] final SHA-256 recorded only after all previous steps
```

Only then should `OmniPhenoR_1.0.0.tar.gz` be described as the frozen
release.
