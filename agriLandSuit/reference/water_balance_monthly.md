# Monthly soil water balance (Thornthwaite-Mather bucket)

Storage is updated as \`S\[t\] = min(awc, max(0, S\[t-1\] + P\[t\] -
PET\[t\]))\`, starting from \`s0\` (half of \`awc\` by default). The
storage available at the start of each month (\`storage_start\`) is the
quantity used by the FAO growing-period test in \`growing_period()\`.

## Usage

``` r
water_balance_monthly(P, PET, awc = 100, s0 = NULL)
```

## Arguments

- P, PET:

  Precipitation and potential evapotranspiration (mm per month); vectors
  or matrices with units in rows and months in columns.

- awc:

  Available water capacity (mm), one value or one per unit.

- s0:

  Initial storage (mm); default \`awc / 2\`.

## Value

An \`agri_water_balance\` list with matrices \`storage_start\`,
\`storage_end\`, \`aet\`, \`deficit\` and \`surplus\`.

## Examples

``` r
wb <- water_balance_monthly(P = c(150, 120, 60, 10, 0, 0), PET = c(110, 105, 100, 95, 90, 85), awc = 100)
wb$storage_end
#>      [,1] [,2] [,3] [,4] [,5] [,6]
#> [1,]   90  100   60    0    0    0
wb$deficit
#>      [,1] [,2] [,3] [,4] [,5] [,6]
#> [1,]    0    0    0   25   90   85
```
