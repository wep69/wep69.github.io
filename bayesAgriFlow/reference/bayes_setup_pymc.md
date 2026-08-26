# Inspect or explicitly create the optional PyMC environment

Inspect the optional Python backend and, only when explicitly requested,
create a reticulate virtual environment with PyMC and PyMC-BART.

## Usage

``` r
bayes_setup_pymc(install = FALSE, envname = "bayesAgriFlow-pymc",
                 python = NULL, packages = c("pymc>=6", "pymc-bart>=0.12",
                 "arviz>=1.1", "numpy"))
```

## Arguments

- install:

  Logical; installation occurs only when TRUE.

- envname:

  Name of the reticulate virtual environment.

- python:

  Optional Python executable used to create the environment.

- packages:

  Character vector of Python package requirements for the environment.

## Value

An object or summary described in the function documentation.

## Details

This interface is part of bayesAgriFlow 1.0.0. Optional brms, Stan, and
Python backends are never installed or invoked silently. Scientific
interpretation should be based on posterior uncertainty, model
diagnostics, and the stated estimand rather than on a single index.

## Examples

``` r
# Example 1
bayes_setup_pymc()
#> $available
#> [1] FALSE
#> 
#> $reticulate
#> [1] TRUE
#> 
#> $python
#> [1] "H:/uv/AppDataLocalUv/cache/archive-v0/MfuOKTFtveE-Nd3l_RFiM/Scripts/python.exe"
#> 
#> $python_version
#> [1] "3.12.10 (tags/v3.12.10:0cc8128, Apr  8 2025, 12:21:36) [MSC v.1943 64 bit (AMD64)]"
#> 
#> $pymc
#> [1] FALSE
#> 
#> $pymc_version
#> [1] NA
#> 
#> $pymc_bart
#> [1] FALSE
#> 
#> $pymc_bart_version
#> [1] NA
#> 
#> $arviz
#> [1] FALSE
#> 
#> $arviz_version
#> [1] NA
#> 
#> $numpy
#> [1] TRUE
#> 
#> $numpy_version
#> [1] "2.5.2"
#> 

# Example 2
bayes_setup_pymc(install = FALSE)$available
#> [1] FALSE

# Example 3
if (FALSE) bayes_setup_pymc(install = TRUE)
```
