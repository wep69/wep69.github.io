# Create a row-oriented experimental event registry

Create a row-oriented experimental event registry

## Usage

``` r
pheno_events(data, id, time, event = "event")
```

## Arguments

- data:

  Event data frame.

- id:

  Identity columns.

- time:

  Event time column.

- event:

  Event-name column.

## Value

`pheno_events`.

## Examples

``` r
pheno_events(data.frame(plant=1:2,day=5,event="stress"),"plant","day")
#>   plant day  event
#> 1     1   5 stress
#> 2     2   5 stress
pheno_events(data.frame(plot=c("A","B"),day=c(10,12),event="inoculation"),"plot","day")
#>   plot day       event
#> 1    A  10 inoculation
#> 2    B  12 inoculation
pheno_events(data.frame(plant=1,day=c(5,8),event=c("stress","rewater")),"plant","day")
#>   plant day   event
#> 1     1   5  stress
#> 2     1   8 rewater
```
