# Add time since a registered event

Add time since a registered event

## Usage

``` r
pheno_time_since_event(x, events, event_name, output = "time_since_event")
```

## Arguments

- x:

  `pheno_series`.

- events:

  `pheno_events`.

- event_name:

  Event name.

- output:

  New column.

## Value

`pheno_series`.

## Examples

``` r
s<-pheno_series(data.frame(plant=1,day=1:8,trait="a",value=1:8),"plant","day","trait","value")
e<-pheno_events(data.frame(plant=1,day=5,event="stress"),"plant","day")
pheno_time_since_event(s,e,"stress")
#> <pheno_series>
#>   rows: 8 
#>   subjects: 1 
#>   traits: 1 
#>   time range: 1 to 8 
pheno_time_since_event(s,e,"stress","days_from_stress")
#> <pheno_series>
#>   rows: 8 
#>   subjects: 1 
#>   traits: 1 
#>   time range: 1 to 8 
head(pheno_time_since_event(s,e,"stress"))
#> <pheno_series>
#>   rows: 6 
#>   subjects: 1 
#>   traits: 1 
#>   time range: 1 to 6 
```
