# Quantify disease severity within detected leaf instances

Quantify disease severity within detected leaf instances

## Usage

``` r
pheno_disease_instances(
  leaf_instances,
  lesion_instances = NULL,
  lesion_model = NULL,
  ...
)
```

## Arguments

- leaf_instances:

  `pheno_instances` representing leaves.

- lesion_instances:

  Optional lesion instances.

- lesion_model:

  Optional function used to segment lesions inside each leaf.

- ...:

  Passed to `lesion_model`.

## Value

A tibble with leaf-level area and lesion severity.

## Examples

``` r
leaf<-matrix(FALSE,30,30);leaf[3:25,4:26]<-TRUE;les<-matrix(FALSE,30,30);les[10:15,12:18]<-TRUE
pheno_disease_instances(pheno_instances(list(leaf),"leaf",1),
    pheno_instances(list(les),"lesion",1))
#> # A tibble: 1 × 5
#>   object_id class leaf_area_px lesion_area_px severity_percent
#>       <int> <chr>        <int>          <int>            <dbl>
#> 1         1 leaf           529             42             7.94
pheno_disease_instances(pheno_instances(list(leaf),"leaf",1),
    lesion_model=function(m){z<-matrix(FALSE,nrow(m),ncol(m));z[10:12,10:12]<-TRUE;z})
#> # A tibble: 1 × 5
#>   object_id class leaf_area_px lesion_area_px severity_percent
#>       <int> <chr>        <int>          <int>            <dbl>
#> 1         1 leaf           529              9             1.70
```
