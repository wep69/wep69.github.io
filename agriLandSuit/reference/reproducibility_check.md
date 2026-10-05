# Diagnose reproducibility against a run manifest

Diagnose reproducibility against a run manifest

## Usage

``` r
reproducibility_check(x = NULL, manifest = NULL, inputs = NULL, root = NULL)
```

## Arguments

- x:

  Optional current analysis object.

- manifest:

  Optional manifest object or file path.

- inputs:

  Optional current input files. If omitted, paths recorded in the
  manifest are rechecked when available.

- root:

  Optional project root used to resolve relative input paths stored in
  the manifest (see \`land_manifest(root = )\`). When \`NULL\` and the
  manifest stores relative paths, they are resolved against the folder
  of the manifest file, then against the working directory.

## Value

An \`agri_reproducibility\` object containing checks and overall status.

## Examples

``` r
d <- file.path(tempdir(), "agri_proj"); dir.create(file.path(d, "data"), recursive = TRUE, showWarnings = FALSE)
f <- file.path(d, "data", "inputs.csv"); utils::write.csv(data.frame(x = 1:3), f, row.names = FALSE)
m <- land_manifest(1:3, inputs = c(inputs = f), root = d)
reproducibility_check(1:3, m, root = d)
#> <agri_reproducibility> PASS 
#>               check status
#>            manifest   pass
#>  object_fingerprint   pass
#>        input:inputs   pass
#>     package_version   pass
#>               terra   pass
#>                                                                                                                                               detail
#>                                                                                                                  Schema: agriLandSuit-run-manifest/1
#>  current=194297f40fdd4a2ac1b5ec4da3e8710b0fcfcc724c19d0cc4a8cb189baa2667a reference=194297f40fdd4a2ac1b5ec4da3e8710b0fcfcc724c19d0cc4a8cb189baa2667a
#>                                                                               C:/Users/wep69/AppData/Local/Temp/Rtmpqq5Fu6/agri_proj/data/inputs.csv
#>                                                                                                                        current=1.1.0 reference=1.1.0
#>                                                                                                                                               1.9.50
```
