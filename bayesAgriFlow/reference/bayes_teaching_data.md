# Load a frozen simulated agronomic teaching dataset

These datasets were simulated for teaching, examples, and numerical
validation. They are not empirical field evidence.

## Usage

``` r
bayes_teaching_data(name = c(
  "crd", "rcbd", "latin_square", "factorial", "split_plot", "dose",
  "ancova", "qual_quant", "mixed", "repeated", "split_split", "strip_plot",
  "glmm_count", "glmm_binomial", "heterogeneous", "nonlinear", "smooth",
  "gp", "bart", "spatial", "gxe", "multivariate", "measurement_error",
  "missing", "censored", "ordinal", "mixture", "distributional",
  "model_selection", "decision", "voi"
))
```

## Arguments

- name:

  One of the frozen teaching datasets.

## Value

A data frame.

## Examples

``` r
# Example 1: completely randomized experiment
head(bayes_teaching_data("crd"))
#>   treatment replicate    yield
#> 1   Control         1 6.388969
#> 2   Control         2 6.146232
#> 3   Control         3 5.854985
#> 4   Control         4 6.708967
#> 5   Control         5 5.931464
#> 6   Control         6 5.665910
# Example 2: repeated-measures experiment
head(bayes_teaching_data("repeated"))
#>   plot_id block treatment time   height
#> 1     P01    B1   Control    0 20.52970
#> 2     P01    B1   Control    7 23.93143
#> 3     P01    B1   Control   14 27.89306
#> 4     P01    B1   Control   21 32.76466
#> 5     P01    B1   Control   28 37.18323
#> 6     P02    B2   Control    0 17.55035
# Example 3: model-selection and decision-analysis teaching data
head(bayes_teaching_data("model_selection"))
#>   plot block  nitrogen    water    soil_p organic_matter canopy_index
#> 1    1    B1 102.86227 406.6799 16.948274       1.590248    0.8037581
#> 2    2    B2 123.92745 338.3007 18.571848       2.537122    0.8330822
#> 3    3    B3 104.59841 255.6107 26.201023       2.639140    0.6324103
#> 4    4    B4  98.28942 400.4337 19.829192       2.536973    0.6971658
#> 5    5    B5  30.01891 449.3719  5.891123       3.245140    0.7040923
#> 6    6    B6 130.46247 267.0637 21.196300       2.337816    0.7952099
#>   correlated_proxy     noise1     noise2    yield
#> 1        0.6122747  0.2761177 -0.6192068 9.461030
#> 2        0.5865585  1.1983962 -0.3095302 9.694092
#> 3        0.5671191 -1.9658373  0.4427608 9.247044
#> 4        0.2794007 -1.0997126  0.5231274 9.683610
#> 5        0.1928061 -1.4979378 -0.9674537 8.637404
#> 6        0.5732862  1.8406476 -1.0379576 9.602675
```
