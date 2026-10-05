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

## Examples

``` r
# \donttest{
python_backend_status("fuzzy")
#>    group          pip  import required_0_1_0 used_0_2_0 used_0_3_0 used_0_4_0
#> 11 fuzzy        numpy   numpy          FALSE       TRUE       TRUE       TRUE
#> 12 fuzzy scikit-fuzzy skfuzzy          FALSE       TRUE       TRUE       TRUE
#>    used_0_5_0 used_0_6_0 used_0_7_0 active_1_0_0 reticulate available
#> 11       TRUE       TRUE       TRUE         TRUE       TRUE      TRUE
#> 12       TRUE       TRUE       TRUE         TRUE       TRUE     FALSE
# }
```
