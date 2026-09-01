# Diagnose reproducibility against a run manifest

Diagnose reproducibility against a run manifest

## Usage

``` r
reproducibility_check(x = NULL, manifest = NULL, inputs = NULL)
```

## Arguments

- x:

  Optional current analysis object.

- manifest:

  Optional manifest object or file path.

- inputs:

  Optional current input files. If omitted, paths recorded in the
  manifest are rechecked when available.

## Value

An \`agri_reproducibility\` object containing checks and overall status.
