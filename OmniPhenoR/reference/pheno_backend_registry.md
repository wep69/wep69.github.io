# List registered analysis backends

List registered analysis backends

## Usage

``` r
pheno_backend_registry(task = NULL)
```

## Arguments

- task:

  Optional regular expression filtering task/backend text.

## Value

A tibble describing language, package, task, output, license, and
status.

## Examples

``` r
pheno_backend_registry()
#> # A tibble: 9 × 8
#>   backend_id        language package      task       output_class license status
#>   <chr>             <chr>    <chr>        <chr>      <chr>        <chr>   <chr> 
#> 1 native            R        NA           classical… pheno_mask/… MIT (O… recom…
#> 2 torch             R/C++    torch        segmentat… pheno_predi… BSD-st… tested
#> 3 plantcv           Python   plantcv      plant ima… pheno_predi… MPL-2.0 exper…
#> 4 ultralytics_yolo  Python   ultralytics  detection… pheno_detec… AGPL-3… exper…
#> 5 leafmachine2      Python   LeafMachine2 herbarium… pheno_detec… GPL-3.0 exper…
#> 6 maskrcnn_external external NA           instance … pheno_insta… backen… exper…
#> 7 python_function   R/Python reticulate   custom ad… canonical    adapte… tested
#> 8 coco              format   jsonlite     dataset i… pheno_detec… format  tested
#> 9 yolo_format       format   NA           dataset i… pheno_detec… format  tested
#> # ℹ 1 more variable: install_automatic <lgl>
pheno_backend_registry("detection")
#> # A tibble: 4 × 8
#>   backend_id       language package      task        output_class license status
#>   <chr>            <chr>    <chr>        <chr>       <chr>        <chr>   <chr> 
#> 1 ultralytics_yolo Python   ultralytics  detection/… pheno_detec… AGPL-3… exper…
#> 2 leafmachine2     Python   LeafMachine2 herbarium … pheno_detec… GPL-3.0 exper…
#> 3 coco             format   jsonlite     dataset in… pheno_detec… format  tested
#> 4 yolo_format      format   NA           dataset in… pheno_detec… format  tested
#> # ℹ 1 more variable: install_automatic <lgl>
subset(pheno_backend_registry(), language == "Python")
#> # A tibble: 3 × 8
#>   backend_id       language package      task        output_class license status
#>   <chr>            <chr>    <chr>        <chr>       <chr>        <chr>   <chr> 
#> 1 plantcv          Python   plantcv      plant imag… pheno_predi… MPL-2.0 exper…
#> 2 ultralytics_yolo Python   ultralytics  detection/… pheno_detec… AGPL-3… exper…
#> 3 leafmachine2     Python   LeafMachine2 herbarium … pheno_detec… GPL-3.0 exper…
#> # ℹ 1 more variable: install_automatic <lgl>
```
