# PlantCV backend status or dispatcher

PlantCV backend status or dispatcher

## Usage

``` r
pheno_plantcv(x = NULL, task = c("status", "segment"), ...)
```

## Arguments

- x:

  Optional image input.

- task:

  `status` or `segment`.

- ...:

  Passed to
  [`pheno_plantcv_segment()`](https://wep69.github.io/OmniPhenoR/reference/pheno_plantcv_segment.md).

## Value

Status table or `pheno_prediction`.

## References

Schuhl H et al. (2026). PlantCV v4: Image analysis software for
high-throughput plant phenotyping. *Plant Phenome Journal* 9:e70065.
[doi:10.1002/ppj2.70065](https://doi.org/10.1002/ppj2.70065) .

## Examples

``` r
pheno_plantcv(task="status")
#> # A tibble: 1 × 6
#>   backend module  available version initialized note                            
#>   <chr>   <chr>   <lgl>     <chr>   <lgl>       <chr>                           
#> 1 plantcv plantcv NA        NA      FALSE       Python not initialized; module …
mock<-function(z) pheno_segment(z,"ExG");
    pheno_plantcv(pheno_data("leaf_rgb"),"segment",adapter=mock)
#> <pheno_prediction>
#>   engine: plantcv_adapter  device: cpu 
#>   classes: background, foreground 
#>   mask dimensions: 96 x 128 
pheno_plantcv(pheno_data("leaf_rgb"), "segment", adapter = function(z) pheno_data("leaf_mask"))
#> <pheno_prediction>
#>   engine: plantcv_adapter  device: cpu 
#>   classes: background, foreground 
#>   mask dimensions: 96 x 128 
```
