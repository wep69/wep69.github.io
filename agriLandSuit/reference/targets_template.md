# Write a \`targets\`/\`geotargets\` workflow template

Write a \`targets\`/\`geotargets\` workflow template

## Usage

``` r
targets_template(path = "_targets.R", use_geotargets = TRUE, overwrite = FALSE)
```

## Arguments

- path:

  Destination \`\_targets.R\` path.

- use_geotargets:

  Include a commented \`tar_terra_rast()\` pattern and load
  \`geotargets\` in the template.

- overwrite:

  Allow replacement.

## Value

Output path invisibly.

## Examples

``` r
f <- tempfile(fileext = ".R")
targets_template(f, use_geotargets = FALSE)
head(readLines(f), 10)
#>  [1] "library(targets)"                                              
#>  [2] "library(agriLandSuit)"                                         
#>  [3] ""                                                              
#>  [4] "tar_option_set(packages = c(\"agriLandSuit\", \"terra\"))"     
#>  [5] ""                                                              
#>  [6] "list("                                                         
#>  [7] "  tar_target(config, list(project = \"agriLandSuit\")),"       
#>  [8] "  tar_target(run_manifest, land_manifest(parameters = config))"
#>  [9] ")"                                                             
#> [10] ""                                                              
```
