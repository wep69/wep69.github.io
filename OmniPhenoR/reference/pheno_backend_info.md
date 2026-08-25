# Inspect one backend

Inspect one backend

## Usage

``` r
pheno_backend_info(backend_id)
```

## Arguments

- backend_id:

  Backend identifier.

## Value

One-row tibble.

## Examples

``` r
pheno_backend_info("plantcv")
#> # A tibble: 1 × 8
#>   backend_id language package task                 output_class   license status
#>   <chr>      <chr>    <chr>   <chr>                <chr>          <chr>   <chr> 
#> 1 plantcv    Python   plantcv plant image analysis pheno_predict… MPL-2.0 exper…
#> # ℹ 1 more variable: install_automatic <lgl>
pheno_backend_info("ultralytics_yolo")
#> # A tibble: 1 × 8
#>   backend_id       language package     task         output_class license status
#>   <chr>            <chr>    <chr>       <chr>        <chr>        <chr>   <chr> 
#> 1 ultralytics_yolo Python   ultralytics detection/i… pheno_detec… AGPL-3… exper…
#> # ℹ 1 more variable: install_automatic <lgl>
pheno_backend_info("native")
#> # A tibble: 1 × 8
#>   backend_id language package task                  output_class  license status
#>   <chr>      <chr>    <chr>   <chr>                 <chr>         <chr>   <chr> 
#> 1 native     R        NA      classical phenotyping pheno_mask/p… MIT (O… recom…
#> # ℹ 1 more variable: install_automatic <lgl>
```
