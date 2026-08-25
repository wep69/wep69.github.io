# Create an experiment-level phenotyping object

`pheno_experiment()` is the 0.5.0 container above projects,
acquisitions, phenotypes, longitudinal series, files, registries, and
provenance. It is deliberately lightweight: large tables may remain
external in Parquet or DuckDB and be referenced by the experiment
manifest.

## Usage

``` r
pheno_experiment(
  study,
  title = study,
  root = NULL,
  metadata = list(),
  design = NULL,
  tables = list(),
  schema_version = "0.5"
)
```

## Arguments

- study:

  Study identifier.

- title:

  Human-readable title.

- root:

  Optional experiment root directory.

- metadata:

  Named list of general metadata.

- design:

  Optional experimental-design table.

- tables:

  Named list of in-memory tables.

- schema_version:

  Internal experiment schema version.

## Value

A `pheno_experiment`.

## Examples

``` r
pheno_experiment("soybean_drought", "Soybean drought imaging")
#> <pheno_experiment> soybean_drought 
#>   id: exp_60c4434883eb  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
pheno_experiment("maize_n", root = tempdir(), metadata = list(year = 2026))
#> <pheno_experiment> maize_n 
#>   id: exp_807f8a4471b8  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
pheno_experiment("bean_disease", design = data.frame(plot=1:3,trt=c("A","B","C")))
#> <pheno_experiment> bean_disease 
#>   id: exp_fdc922f02864  schema: 0.5 
#>   tables: 0  files: 0 
#>   variables: 0  germplasm: 0 
```
