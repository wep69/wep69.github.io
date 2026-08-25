# Pack a portable research-data bundle

Pack a portable research-data bundle

## Usage

``` r
pheno_pack(
  experiment,
  path,
  profile = c("metadata", "analysis", "archive", "complete"),
  overwrite = FALSE
)
```

## Arguments

- experiment:

  `pheno_experiment`.

- path:

  Output directory.

- profile:

  metadata, analysis, archive, or complete.

- overwrite:

  Overwrite existing directory.

## Value

Output path invisibly.

## Examples

``` r
p<-file.path(tempdir(),"phenopack_demo")
pheno_pack(pheno_experiment("demo"),p,"metadata",overwrite=TRUE);dir.exists(p)
#> [1] TRUE
p2<-file.path(tempdir(),"phenopack_archive")
pheno_pack(pheno_experiment("demo"),p2,"archive",overwrite=TRUE)
list.files(p)
#> [1] "CITATION.cff"     "README.md"        "checksums.sha256" "metadata"        
#> [5] "tables"          
```
