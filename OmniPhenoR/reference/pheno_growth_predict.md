# Predict a compact growth fit

Predict a compact growth fit

## Usage

``` r
pheno_growth_predict(object, new_time)
```

## Arguments

- object:

  `pheno_growth_fit`.

- new_time:

  Prediction times.

## Value

Tibble.

## Examples

``` r
f<-pheno_growth_fit(0:10,100/(1+exp(-(0:10-5))),"logistic")
#> Warning: number of iterations exceeded maximum of 50
pheno_growth_predict(f,0:10)
#> # A tibble: 11 × 2
#>     time fitted
#>    <int>  <dbl>
#>  1     0  0.669
#>  2     1  1.80 
#>  3     2  4.74 
#>  4     3 11.9  
#>  5     4 26.9  
#>  6     5 50    
#>  7     6 73.1  
#>  8     7 88.1  
#>  9     8 95.3  
#> 10     9 98.2  
#> 11    10 99.3  
pheno_growth_predict(f,seq(0,10,.25))
#> # A tibble: 41 × 2
#>     time fitted
#>    <dbl>  <dbl>
#>  1  0     0.669
#>  2  0.25  0.858
#>  3  0.5   1.10 
#>  4  0.75  1.41 
#>  5  1     1.80 
#>  6  1.25  2.30 
#>  7  1.5   2.93 
#>  8  1.75  3.73 
#>  9  2     4.74 
#> 10  2.25  6.01 
#> # ℹ 31 more rows
pheno_growth_predict(f,c(2,5,8))
#> # A tibble: 3 × 2
#>    time fitted
#>   <dbl>  <dbl>
#> 1     2   4.74
#> 2     5  50   
#> 3     8  95.3 
```
