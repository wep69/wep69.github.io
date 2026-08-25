# Area under the disease progress curve

Area under the disease progress curve

## Usage

``` r
pheno_audpc(time, severity, relative = FALSE, severity_max = NULL)
```

## Arguments

- time, severity:

  Assessment time and severity.

- relative:

  Return relative AUDPC.

- severity_max:

  Maximum severity scale.

## Value

Numeric scalar.

## Examples

``` r
pheno_audpc(c(0,7,14),c(0,10,40))
#> [1] 210
pheno_audpc(c(0,7,14),c(0,.1,.4),relative=TRUE,severity_max=1)
#> [1] 0.15
pheno_audpc(c(0,5,12,20),c(1,8,22,45))
#> [1] 395.5
```
