# Inspect the optional Python backend

Python is never required for package installation or the default R
engine. This function reports whether \`reticulate\` and optional Python
modules are available without installing or modifying the user's Python
environment.

## Usage

``` r
python_backend_status(group = NULL)
```

## Arguments

- group:

  Optional module groups: spatial, scalable, fuzzy, mcda, uncertainty,
  sampling.

## Value

A data.frame describing module availability.
