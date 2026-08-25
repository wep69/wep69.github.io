# OmniPhenoR 0.1.0 freeze validation

Validation date: 2026-08-23/24 (UTC rollover during execution)

## Documentation round

The focused vignette set was validated together with the long-form
tutorial:

- v00-foundations-to-advanced-tutorial.Rmd
- v01-rgb-indices.Rmd
- v02-texture-analysis.Rmd
- v03-leaf-health-workflow.Rmd
- v04-validation-reproducibility.Rmd

All five R Markdown vignettes rendered successfully against an installed
copy of OmniPhenoR 0.1.0.

## Source documentation and tests

- roxygen2 documentation regenerated.
- testthat suite executed successfully before build.
- R CMD check executed the package tests successfully from the source
  tarball.
- Examples: OK.
- Vignette dependencies: OK.
- Vignette rebuild: OK.
- PDF manual: OK under –as-cran.
- HTML manual: OK under –as-cran.

## Corrected validation issue

The first –as-cran pass reported a NOTE because local validation log
files were being included at the package top level. The files were
excluded through .Rbuildignore and the package was rebuilt from source.
The second –as-cran check no longer reports this NOTE.

## Final checks

### Standard source-package check

`R CMD check --no-manual OmniPhenoR_finalcheck_0.1.0.tar.gz`

Status: **OK**

### CRAN-style source-package check

`R CMD check --as-cran OmniPhenoR_0.1.0.tar.gz`

Status: **1 NOTE**

The only remaining NOTE is the CRAN incoming-feasibility administrative
message:

`New submission`

There are **0 ERRORs and 0 WARNINGs** and no package-code/documentation
NOTE remains. The remaining NOTE cannot be removed by changing package
source without changing the fact that this is a new CRAN submission.

## Frozen tarball

Path: `D:/temp/OmniPhenoR_0.1.0.tar.gz`

SHA-256:

`4e7970127cb8e4f1d2e521d949909d21e3ddfe915bff9bbcd4b84b9595ed12a6`

## Freeze decision

OmniPhenoR 0.1.0 is frozen as the validated Windows source release
candidate. Any subsequent source-code, documentation, vignette,
dependency, metadata, or test change invalidates this hash and requires
a new build and full check cycle.
