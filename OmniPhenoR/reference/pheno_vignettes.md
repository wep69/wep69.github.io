# List installed/precompiled package vignettes

List installed/precompiled package vignettes

## Usage

``` r
pheno_vignettes(pattern = NULL)
```

## Arguments

- pattern:

  Optional regular expression for vignette id/title/topic.

## Value

Vignette artifact registry.

## Examples

``` r
pheno_vignettes()
#> # A tibble: 44 × 18
#>    artifact_id   vignette title source_script created_on package_version backend
#>    <chr>         <chr>    <chr> <chr>         <chr>      <chr>           <chr>  
#>  1 v00-foundati… v00-fou… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  2 v01-rgb-indi… v01-rgb… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  3 v02-texture-… v02-tex… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  4 v03-leaf-hea… v03-lea… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  5 v04-validati… v04-val… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  6 v05-deep-lea… v05-dee… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#>  7 v06-semantic… v06-sem… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#>  8 v07-image-cl… v07-ima… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#>  9 v08-training… v08-tra… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 10 v09-model-re… v09-mod… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> # ℹ 34 more rows
#> # ℹ 11 more variables: backend_version <lgl>, model_id <lgl>, model_hash <lgl>,
#> #   input_hash <lgl>, seed <lgl>, source_hash <chr>, output_file <chr>,
#> #   output_hash <chr>, precompiled <lgl>, render_mode <chr>, notes <chr>
pheno_vignettes("YOLO|Python")
#> # A tibble: 4 × 18
#>   artifact_id    vignette title source_script created_on package_version backend
#>   <chr>          <chr>    <chr> <chr>         <chr>      <chr>           <chr>  
#> 1 v13-python-ba… v13-pyt… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 2 v17-yolo-work… v17-yol… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 3 v19-coco-and-… v19-coc… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 4 v22-python-en… v22-pyt… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> # ℹ 11 more variables: backend_version <lgl>, model_id <lgl>, model_hash <lgl>,
#> #   input_hash <lgl>, seed <lgl>, source_hash <chr>, output_file <chr>,
#> #   output_hash <chr>, precompiled <lgl>, render_mode <chr>, notes <chr>
pheno_vignettes("object|instance")
#> # A tibble: 3 × 18
#>   artifact_id    vignette title source_script created_on package_version backend
#>   <chr>          <chr>    <chr> <chr>         <chr>      <chr>           <chr>  
#> 1 v15-object-de… v15-obj… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 2 v16-instance-… v16-ins… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 3 v20-object-to… v20-obj… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> # ℹ 11 more variables: backend_version <lgl>, model_id <lgl>, model_hash <lgl>,
#> #   input_hash <lgl>, seed <lgl>, source_hash <chr>, output_file <chr>,
#> #   output_hash <chr>, precompiled <lgl>, render_mode <chr>, notes <chr>
```
