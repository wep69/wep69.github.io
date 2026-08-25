# Describe or explicitly declare Python requirements

`action = "check"` is read-only. `action = "declare"` calls
[`reticulate::py_require()`](https://rstudio.github.io/reticulate/reference/py_require.html)
and therefore may cause reticulate to resolve an environment later when
Python is first initialized. Nothing is installed or downloaded unless
the user explicitly chooses `action = "declare"` and then initializes
Python.

## Usage

``` r
pheno_python_require(
  backend = c("plantcv", "yolo", "ultralytics", "opencv", "generic"),
  action = c("check", "declare"),
  packages = NULL,
  python_version = NULL
)
```

## Arguments

- backend:

  `plantcv`, `yolo`, `ultralytics`, `opencv`, or `generic`.

- action:

  `check` or explicit `declare`.

- packages:

  Optional package requirement override.

- python_version:

  Optional Python-version requirement override.

## Value

Requirement metadata invisibly for declaration, visibly for checks.

## Examples

``` r
pheno_python_require("plantcv")
#> $backend
#> [1] "plantcv"
#> 
#> $packages
#> [1] "plantcv>=4.11,<5"
#> 
#> $python_version
#> [1] ">=3.11,<3.14"
#> 
#> $reticulate_available
#> [1] TRUE
#> 
#> $action
#> [1] "check"
#> 
pheno_python_require("yolo", action = "check")
#> $backend
#> [1] "yolo"
#> 
#> $packages
#> [1] "ultralytics>=8"
#> 
#> $python_version
#> [1] ">=3.8"
#> 
#> $reticulate_available
#> [1] TRUE
#> 
#> $action
#> [1] "check"
#> 
if (FALSE) pheno_python_require("opencv", action = "declare")
```
