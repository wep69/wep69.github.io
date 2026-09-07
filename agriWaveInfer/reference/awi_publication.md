# Publication and reproducibility utilities

Creates human-readable reports, portable analysis bundles, and
publication-readiness checks without claiming causal identification or
runtime certification.

## Usage

``` r
awi_report(x, file = NULL, format = c("markdown", "text"), overwrite = FALSE)

awi_reproducibility_bundle(x, path, archive = c("zip", "none"),
  overwrite = FALSE, include_intermediate = TRUE)

awi_publication_check(path = ".", expected_version = NULL)
```

## Arguments

- x:

  An agriWaveInfer result object.

- file:

  Optional report output file.

- format:

  Report format.

- overwrite:

  Whether an existing output may be replaced.

- path:

  Bundle directory or package source directory, depending on the
  function.

- archive:

  Whether to create a ZIP bundle.

- include_intermediate:

  Whether workflow intermediate objects are saved.

- expected_version:

  Expected package version for metadata reconciliation; defaults to
  DESCRIPTION.

## Value

`awi_report()` returns an `awi_report`; `awi_reproducibility_bundle()`
returns a bundle descriptor; `awi_publication_check()` returns
source-tree checks.
