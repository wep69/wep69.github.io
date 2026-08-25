# Load a phenotyping model record

Load a phenotyping model record

## Usage

``` r
pheno_model_load(path, register = TRUE, overwrite = FALSE)
```

## Arguments

- path:

  RDS file created by
  [`pheno_model_save()`](https://wep69.github.io/OmniPhenoR/reference/pheno_model_save.md).

- register:

  Register the loaded record in the current session.

- overwrite:

  Replace an existing identifier when registering.

## Value

A `pheno_model_record`.

## Examples

``` r
pheno_model_register("load_example", "classification", "cnn_small", overwrite = TRUE)
f <- tempfile(fileext = ".rds"); pheno_model_save("load_example", f)
z <- pheno_model_load(f, register = FALSE); z$architecture
#> [1] "cnn_small"
unlink(f)
```
