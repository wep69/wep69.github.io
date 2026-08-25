# Write a reproducible Markdown phenotyping report skeleton

Write a reproducible Markdown phenotyping report skeleton

## Usage

``` r
pheno_report(
  x,
  file = "OmniPhenoR_report.md",
  title = "OmniPhenoR phenotyping report"
)
```

## Arguments

- x:

  A `pheno_pipeline`, `pheno_texture`, or `pheno_project` object.

- file:

  Output `.md` file.

- title:

  Report title.

## Value

Invisibly, the normalized file path.

## Examples

``` r
f <- tempfile(fileext = ".md")
p <- pheno_pipeline(pheno_data("leaf_rgb"), "leaf_area")
pheno_report(p, f)
file.exists(f)
#> [1] TRUE
f2 <- tempfile(fileext = ".md")
t2 <- pheno_texture(pheno_data("leaf_gray"), "first_order")
pheno_report(t2, f2, "Leaf texture")
readLines(f2, n = 4)
#> [1] "# Leaf texture"                        
#> [2] ""                                      
#> [3] "**Generated:** 2026-08-25 01:03:03 UTC"
#> [4] "**Object class:** `pheno_texture`"     
f3 <- tempfile(fileext = ".md")
pheno_report(pheno_project("trial"), f3, "Trial phenotyping")
file.info(f3)$size
#> [1] 1214
```
