# List known pretrained-model integration templates

OmniPhenoR does not bundle third-party model weights. The registry
records integration templates and licensing cautions;
`available = FALSE` means that the user must provide/obtain weights
under appropriate terms.

## Usage

``` r
pheno_pretrained_models()
```

## Value

A tibble.

## Examples

``` r
pheno_pretrained_models()
#> # A tibble: 4 × 7
#>   model_id               task            backend bundled available license note 
#>   <chr>                  <chr>           <chr>   <lgl>   <lgl>     <chr>   <chr>
#> 1 user_yolo_detection    object_detecti… ultral… FALSE   FALSE     AGPL-3… No w…
#> 2 user_yolo_segmentation instance_segme… ultral… FALSE   FALSE     AGPL-3… No w…
#> 3 leafmachine2_models    herbarium_phen… leafma… FALSE   FALSE     GPL-3.… Use …
#> 4 external_maskrcnn      instance_segme… maskrc… FALSE   FALSE     model-… Regi…
subset(pheno_pretrained_models(), task == "object_detection")
#> # A tibble: 1 × 7
#>   model_id            task             backend   bundled available license note 
#>   <chr>               <chr>            <chr>     <lgl>   <lgl>     <chr>   <chr>
#> 1 user_yolo_detection object_detection ultralyt… FALSE   FALSE     AGPL-3… No w…
subset(pheno_pretrained_models(), backend == "ultralytics_yolo")
#> # A tibble: 2 × 7
#>   model_id               task            backend bundled available license note 
#>   <chr>                  <chr>           <chr>   <lgl>   <lgl>     <chr>   <chr>
#> 1 user_yolo_detection    object_detecti… ultral… FALSE   FALSE     AGPL-3… No w…
#> 2 user_yolo_segmentation instance_segme… ultral… FALSE   FALSE     AGPL-3… No w…
```
