# Export lightweight JSON-LD-like experiment metadata

This function creates a compact JSON-LD document using schema.org terms
for dataset-level discovery. It is not a complete domain ontology
mapping.

## Usage

``` r
pheno_to_jsonld(experiment, path = NULL)
```

## Arguments

- experiment:

  `pheno_experiment`.

- path:

  Optional output path.

## Value

JSON-LD text or path invisibly.

## Examples

``` r
if (FALSE) pheno_to_jsonld(pheno_experiment("demo","Demo dataset")) # \dontrun{}
if (FALSE) pheno_to_jsonld(pheno_experiment("demo"),tempfile(fileext=".jsonld")) # \dontrun{}
if (FALSE) grepl("Dataset",pheno_to_jsonld(pheno_experiment("demo"))) # \dontrun{}
```
