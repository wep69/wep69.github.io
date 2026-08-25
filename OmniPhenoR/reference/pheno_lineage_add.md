# Register data lineage

Register data lineage

## Usage

``` r
pheno_lineage_add(experiment, input_id, output_id, operation, details = "")
```

## Arguments

- experiment:

  `pheno_experiment`.

- input_id, output_id:

  Registered file/object identifiers.

- operation:

  Operation label.

- details:

  Optional details.

## Value

Updated experiment.

## Examples

``` r
e<-pheno_experiment("demo");e<-pheno_lineage_add(e,"raw1","mask1","segmentation");e$lineage
#>   input_id output_id    operation details               timestamp
#> 1     raw1     mask1 segmentation         2026-08-25 01:02:30 UTC
e<-pheno_lineage_add(e,"mask1","traits1","morphology",details="v1");nrow(e$lineage)
#> [1] 2
pheno_lineage(e)
#> # A tibble: 2 × 5
#>   input_id output_id operation    details timestamp              
#>   <chr>    <chr>     <chr>        <chr>   <chr>                  
#> 1 raw1     mask1     segmentation ""      2026-08-25 01:02:30 UTC
#> 2 mask1    traits1   morphology   "v1"    2026-08-25 01:02:30 UTC
```
