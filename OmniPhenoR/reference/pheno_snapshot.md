# Create an experiment snapshot

Create an experiment snapshot

## Usage

``` r
pheno_snapshot(experiment, label = format(Sys.time(), "%Y%m%dT%H%M%S"))
```

## Arguments

- experiment:

  `pheno_experiment`.

- label:

  Optional label.

## Value

`pheno_snapshot`.

## Examples

``` r
pheno_snapshot(pheno_experiment("demo"))
#> $label
#> [1] "20260824T220316"
#> 
#> $experiment_id
#> [1] "exp_0e2396c11b88"
#> 
#> $study
#> [1] "demo"
#> 
#> $schema_version
#> [1] "0.5"
#> 
#> $files
#> # A tibble: 0 × 0
#> 
#> $n_files
#> [1] 0
#> 
#> $table_rows
#> integer(0)
#> 
#> $created
#> [1] "2026-08-25 01:03:16 UTC"
#> 
#> attr(,"class")
#> [1] "pheno_snapshot"
pheno_snapshot(pheno_experiment("demo"),"before_analysis")
#> $label
#> [1] "before_analysis"
#> 
#> $experiment_id
#> [1] "exp_8a899b2ada53"
#> 
#> $study
#> [1] "demo"
#> 
#> $schema_version
#> [1] "0.5"
#> 
#> $files
#> # A tibble: 0 × 0
#> 
#> $n_files
#> [1] 0
#> 
#> $table_rows
#> integer(0)
#> 
#> $created
#> [1] "2026-08-25 01:03:16 UTC"
#> 
#> attr(,"class")
#> [1] "pheno_snapshot"
s<-pheno_snapshot(pheno_experiment("demo"));s$study
#> [1] "demo"
```
