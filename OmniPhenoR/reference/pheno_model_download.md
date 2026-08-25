# Explicitly download a model or auxiliary file

This helper is never called during package load, examples, vignettes, or
checks. The caller must explicitly request the download and, when a
license notice is supplied, explicitly acknowledge it.

## Usage

``` r
pheno_model_download(
  url,
  destfile,
  sha256 = NULL,
  license = NULL,
  accept_license = FALSE,
  overwrite = FALSE
)
```

## Arguments

- url:

  Source URL.

- destfile:

  Destination path.

- sha256:

  Optional expected SHA-256.

- license:

  Optional license notice.

- accept_license:

  Explicit acknowledgement when `license` is supplied.

- overwrite:

  Replace existing file.

## Value

Destination path invisibly.

## Examples

``` r
# Network operation deliberately not run in examples.
if (FALSE) pheno_model_download("https://example.org/model.bin", tempfile(),
  accept_license = TRUE)
if (FALSE) pheno_model_download("https://example.org/model.bin", tempfile(),
  sha256 = "expected_hash")
if (FALSE) pheno_model_download("https://example.org/model.bin", tempfile(),
  license = "model license", accept_license = TRUE)
```
