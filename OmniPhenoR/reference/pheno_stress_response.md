# Derive stress and recovery traits

Derive stress and recovery traits

## Usage

``` r
pheno_stress_response(
  data,
  time,
  value,
  stress_start,
  recovery_start,
  group = NULL
)
```

## Arguments

- data:

  Long-form data frame.

- time:

  Time column.

- value:

  Response column.

- stress_start:

  Start of stress.

- recovery_start:

  Start of recovery/rewatering.

- group:

  Optional grouping columns.

## Value

A tibble with baseline, stress minimum/maximum deviation, recovery
fraction and resilience index.

## Examples

``` r
d <- pheno_data("drought_recovery")
pheno_stress_response(d,"day","canopy_cover",14,24,group="plant_id")
#> # A tibble: 10 × 6
#>    series baseline stress_extreme response recovery_fraction resilience
#>    <chr>     <dbl>          <dbl>    <dbl>             <dbl>      <dbl>
#>  1 R01       0.837          0.890   0.0534            -0.802      1    
#>  2 R02       0.842          0.892   0.0506            -0.507      1    
#>  3 R03       0.839          0.890   0.0513            -0.501      1    
#>  4 R04       0.839          0.891   0.0516            -0.616      1    
#>  5 R05       0.840          0.897   0.0564            -0.385      1    
#>  6 R06       0.838          0.674  -0.164              1.09       0.839
#>  7 R07       0.838          0.668  -0.170              1.01       0.849
#>  8 R08       0.839          0.669  -0.169              0.982      0.843
#>  9 R09       0.838          0.666  -0.172              1.17       0.852
#> 10 R10       0.839          0.675  -0.164              1.02       0.849
pheno_stress_response(d,"day","green_fraction",14,24,group=c("treatment","plant_id"))
#> # A tibble: 10 × 6
#>    series      baseline stress_extreme response recovery_fraction resilience
#>    <chr>          <dbl>          <dbl>    <dbl>             <dbl>      <dbl>
#>  1 control.R01    0.887          0.944   0.0571           -0.0997      1    
#>  2 control.R02    0.889          0.940   0.0503           -0.206       1    
#>  3 control.R03    0.890          0.941   0.0510           -0.181       1    
#>  4 control.R04    0.893          0.944   0.0510           -0.114       1    
#>  5 control.R05    0.893          0.947   0.0538           -0.0581      1    
#>  6 stress.R06     0.888          0.692  -0.195             1.08        0.853
#>  7 stress.R07     0.892          0.714  -0.178             1.12        0.847
#>  8 stress.R08     0.889          0.713  -0.176             0.980       0.853
#>  9 stress.R09     0.890          0.701  -0.189             1.08        0.857
#> 10 stress.R10     0.893          0.715  -0.179             0.928       0.850
pheno_stress_response(subset(d,treatment=="stress"),"day","canopy_cover",14,24,group="plant_id")
#> # A tibble: 5 × 6
#>   series baseline stress_extreme response recovery_fraction resilience
#>   <chr>     <dbl>          <dbl>    <dbl>             <dbl>      <dbl>
#> 1 R06       0.838          0.674   -0.164             1.09       0.839
#> 2 R07       0.838          0.668   -0.170             1.01       0.849
#> 3 R08       0.839          0.669   -0.169             0.982      0.843
#> 4 R09       0.838          0.666   -0.172             1.17       0.852
#> 5 R10       0.839          0.675   -0.164             1.02       0.849
```
