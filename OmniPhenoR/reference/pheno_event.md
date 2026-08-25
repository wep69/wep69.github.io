# Create an experimental event table

Create an experimental event table

## Usage

``` r
pheno_event(event, time, id = NULL, type = NULL, metadata = NULL)
```

## Arguments

- event:

  Event name or vector of event names.

- time:

  Event time(s).

- id:

  Optional subject/plot/environment identifier.

- type:

  Optional event type.

- metadata:

  Optional data frame or named list of event metadata.

## Value

A `pheno_events` data frame.

## Examples

``` r
pheno_event("irrigation_stop",14)
#>             event time   id type
#> 1 irrigation_stop   14 <NA> <NA>
pheno_event(c("stress_start","rewatering"),c(14,24),type=c("stress","recovery"))
#>          event time   id     type
#> 1 stress_start   14 <NA>   stress
#> 2   rewatering   24 <NA> recovery
pheno_event("inoculation",7,id="plot_A",metadata=list(pathogen="demo"))
#>         event time     id type pathogen
#> 1 inoculation    7 plot_A <NA>     demo
```
