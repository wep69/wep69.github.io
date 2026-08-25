# Quantify a phenotype response around an experimental event

Quantify a phenotype response around an experimental event

## Usage

``` r
pheno_event_response(
  data,
  time,
  value,
  event_time,
  group = NULL,
  pre = 7,
  post = 14
)
```

## Arguments

- data:

  Long-form data frame.

- time:

  Time column.

- value:

  Response column.

- event_time:

  Event time.

- group:

  Optional grouping columns.

- pre:

  Width of baseline period before event.

- post:

  Width of post-event period.

## Value

A tibble with baseline, response magnitude, extreme time and recovery.

## Examples

``` r
d <- pheno_data("drought_recovery")
pheno_event_response(d,"day","canopy_cover",14,group="plant_id",pre=7,post=14)
#> # A tibble: 10 × 6
#>    series baseline response extreme_time extreme_value recovery
#>    <chr>     <dbl>    <dbl>        <dbl>         <dbl>    <dbl>
#>  1 R01       0.848   0.0677           28         0.915   0.0677
#>  2 R02       0.854   0.0586           28         0.913   0.0586
#>  3 R03       0.854   0.0521           28         0.906   0.0521
#>  4 R04       0.851   0.0641           26         0.915   0.0556
#>  5 R05       0.850   0.0586           28         0.909   0.0586
#>  6 R06       0.853  -0.284            24         0.569  -0.193 
#>  7 R07       0.849  -0.274            24         0.575  -0.176 
#>  8 R08       0.851  -0.281            24         0.570  -0.178 
#>  9 R09       0.849  -0.263            24         0.586  -0.172 
#> 10 R10       0.850  -0.271            24         0.579  -0.181 
pheno_event_response(d,"day","green_fraction",14,group=c("treatment","plant_id"))
#> # A tibble: 10 × 6
#>    series      baseline response extreme_time extreme_value recovery
#>    <chr>          <dbl>    <dbl>        <dbl>         <dbl>    <dbl>
#>  1 control.R01    0.899   0.0509           25         0.95    0.0509
#>  2 control.R02    0.901   0.0489           26         0.95    0.0489
#>  3 control.R03    0.903   0.0470           25         0.95    0.0470
#>  4 control.R04    0.905   0.0447           25         0.95    0.0447
#>  5 control.R05    0.901   0.0490           26         0.95    0.0490
#>  6 stress.R06     0.894  -0.282            24         0.612  -0.175 
#>  7 stress.R07     0.899  -0.276            24         0.622  -0.182 
#>  8 stress.R08     0.895  -0.284            24         0.612  -0.157 
#>  9 stress.R09     0.899  -0.292            24         0.607  -0.197 
#> 10 stress.R10     0.904  -0.275            24         0.629  -0.189 
pheno_event_response(subset(d,treatment=="stress"),"day","canopy_cover",14,group="plant_id")
#> # A tibble: 5 × 6
#>   series baseline response extreme_time extreme_value recovery
#>   <chr>     <dbl>    <dbl>        <dbl>         <dbl>    <dbl>
#> 1 R06       0.853   -0.284           24         0.569   -0.193
#> 2 R07       0.849   -0.274           24         0.575   -0.176
#> 3 R08       0.851   -0.281           24         0.570   -0.178
#> 4 R09       0.849   -0.263           24         0.586   -0.172
#> 5 R10       0.850   -0.271           24         0.579   -0.181
```
