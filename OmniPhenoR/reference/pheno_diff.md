# Compare experiment snapshots

Compare experiment snapshots

## Usage

``` r
pheno_diff(old, new)
```

## Arguments

- old, new:

  `pheno_snapshot` objects.

## Value

List of file and table differences.

## Examples

``` r
e<-pheno_experiment("demo");a<-pheno_snapshot(e,"a");b<-pheno_snapshot(e,"b");pheno_diff(a,b)
#> $summary
#> # A tibble: 1 × 3
#>   added removed changed
#>   <int>   <int>   <int>
#> 1     0       0       0
#> 
#> $added
#> character(0)
#> 
#> $removed
#> character(0)
#> 
#> $changed
#> character(0)
#> 
pheno_diff(a,b)$summary
#> # A tibble: 1 × 3
#>   added removed changed
#>   <int>   <int>   <int>
#> 1     0       0       0
names(pheno_diff(a,b))
#> [1] "summary" "added"   "removed" "changed"
```
