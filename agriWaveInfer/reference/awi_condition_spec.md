# Numerical conditioning specification for multivariate wavelet inference

Creates an explicit numerical-conditioning contract used by partial and
multiple wavelet coherence calculations. Regularization is never
silently enabled.

## Usage

``` r
awi_condition_spec(
  method = c("none", "ridge", "svd"),
  condition_threshold = 1e+08,
  ridge = 1e-08,
  svd_floor = 1e-08,
  on_unstable = c("na", "error")
)
```

## Arguments

- method:

  Local spectral-matrix handling method. `"none"` never modifies an
  unstable matrix; `"ridge"` adds a small diagonal term; `"svd"` uses a
  singular-value floor.

- condition_threshold:

  Condition number above which a local matrix is flagged as unstable.

- ridge:

  Relative ridge coefficient used when `method = "ridge"`.

- svd_floor:

  Minimum singular value as a fraction of the largest singular value
  when `method = "svd"`.

- on_unstable:

  Whether an unresolved unstable local matrix returns `NA` or raises an
  error.

## Value

An S7 object of class `AwiConditionSpec`.
