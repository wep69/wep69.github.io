# Day length and extraterrestrial radiation

\`daylength()\` returns the astronomical day length (hours) and
\`extraterrestrial_radiation()\` the daily extraterrestrial radiation
(MJ m-2 day-1) following FAO-56 (Allen et al., 1998). When \`month\` is
given, the mid-month day of year is used (15, 46, 75, ...).

## Usage

``` r
daylength(lat, month = NULL, doy = NULL)

extraterrestrial_radiation(lat, month = NULL, doy = NULL)
```

## Arguments

- lat:

  Latitude in decimal degrees (negative in the southern hemisphere).

- month:

  Integer month(s).

- doy:

  Day(s) of year, alternative to \`month\`.

## Value

Numeric vector.

## Examples

``` r
daylength(-25, 1:12)
#>  [1] 13.39024 12.83694 12.14400 11.40315 10.77933 10.47125 10.61605 11.15208
#>  [9] 11.86851 12.60865 13.22906 13.52610
extraterrestrial_radiation(-15, 1)
#> [1] 40.81972
```
