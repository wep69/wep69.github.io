# Save a registered model record

Save a registered model record

## Usage

``` r
pheno_model_save(model, path, include_model = TRUE)
```

## Arguments

- model:

  Model identifier or `pheno_model_record`.

- path:

  Destination RDS path.

- include_model:

  Include the in-memory model object. Set `FALSE` for a lightweight
  metadata-only record.

## Value

Normalized output path invisibly.

## Examples

``` r
pheno_model_register("save_example", "classification", "cnn_small", overwrite = TRUE)
f <- tempfile(fileext = ".rds"); pheno_model_save("save_example", f)
pheno_model_load(f, register = FALSE)$model_id
#> [1] "save_example"
unlink(f)
```
