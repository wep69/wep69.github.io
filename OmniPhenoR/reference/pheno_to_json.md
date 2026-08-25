# Export experiment metadata as JSON

Export experiment metadata as JSON

## Usage

``` r
pheno_to_json(experiment, path = NULL, pretty = TRUE)
```

## Arguments

- experiment:

  `pheno_experiment`.

- path:

  Optional path; if omitted returns JSON text.

- pretty:

  Pretty-print.

## Value

JSON text invisibly or character string.

## Examples

``` r
if (FALSE) pheno_to_json(pheno_experiment("demo")) # \dontrun{}
if (FALSE) pheno_to_json(pheno_experiment("demo"),tempfile(fileext=".json")) # \dontrun{}
if (FALSE) pheno_from_json(pheno_to_json(pheno_experiment("demo")))  # \dontrun{}
```
