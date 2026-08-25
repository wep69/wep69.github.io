# Describe a built-in phenotyping workflow

Built-in workflows are transparent presets, not opaque automated
decisions. Each preset returns ordered operations and defaults so they
can be inspected or modified before execution. Version 0.2.0 adds a
model-ready leaf-health workflow while keeping all 0.1.0 presets
unchanged in meaning.

## Usage

``` r
pheno_workflow(
  name = c("leaf_area", "leaf_health", "leaf_texture", "canopy_rgb", "leaf_health_dl")
)
```

## Arguments

- name:

  Built-in workflow name.

## Value

A `pheno_workflow` list.

## Examples

``` r
pheno_workflow("leaf_area")
#> <pheno_workflow> leaf_area 
#>   steps: segment -> morphology 
#>   version: 0.2.0 
pheno_workflow("leaf_health")
#> <pheno_workflow> leaf_health 
#>   steps: segment -> rgb -> disease -> texture 
#>   version: 0.2.0 
pheno_workflow("leaf_health_dl")
#> <pheno_workflow> leaf_health_dl 
#>   steps: segment -> rgb -> texture -> uncertainty 
#>   version: 0.2.0 
```
