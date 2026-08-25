# Fit a compact single-trajectory growth model

Fit a compact single-trajectory growth model

## Usage

``` r
pheno_growth_fit(
  time,
  value,
  model = c("logistic", "gompertz", "linear", "quadratic", "spline")
)
```

## Arguments

- time, value:

  Time and phenotype.

- model:

  `linear`, `quadratic`, `logistic`, `gompertz`, or `spline`.

## Value

`pheno_growth_fit`.

## Examples

``` r
t<-0:10;y<-100/(1+exp(-(t-5)))
pheno_growth_fit(t,y,"logistic")
#> Warning: number of iterations exceeded maximum of 50
#> <pheno_growth_fit> logistic 
#>   n: 11 
pheno_growth_fit(t,y,"gompertz")
#> <pheno_growth_fit> gompertz 
#>   n: 11 
pheno_growth_fit(t,y,"spline")
#> <pheno_growth_fit> spline 
#>   n: 11 
```
