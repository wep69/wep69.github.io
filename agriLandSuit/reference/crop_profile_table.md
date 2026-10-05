# Tabulate crop profiles

Tabulate crop profiles

## Usage

``` r
crop_profile_table(profiles)
```

## Arguments

- profiles:

  A crop profile or a (named) list of profiles.

## Value

Long data frame with one row per requirement (crop, profile_id,
criterion, domain, unit, response, limits, limit1 to limit4, source,
source_id), suitable for a supplementary table.

## Examples

``` r
crop_profile_table(crop_profile_library(c("maize", "bean")))[, c("crop", "criterion", "response", "limits")]
#>     crop criterion   response               limits
#> 1  maize       LGP increasing              90, 150
#> 2  maize   Pseason      range 400, 600, 1200, 1800
#> 3  maize        CV decreasing            0.2, 0.45
#> 4  maize   Tseason      range       10, 18, 33, 47
#> 5  maize     Twarm decreasing               30, 34
#> 6  maize        pH      range   4.5, 5.5, 7.5, 8.5
#> 7  maize       AWC increasing              40, 100
#> 8  maize       SOC increasing                4, 10
#> 9  maize     slope decreasing                2, 10
#> 10  bean       LGP increasing              80, 110
#> 11  bean   Pseason      range  300, 400, 800, 1500
#> 12  bean        CV decreasing            0.25, 0.5
#> 13  bean   Tseason      range        8, 16, 24, 30
#> 14  bean     Twarm decreasing               26, 30
#> 15  bean        pH      range       4.5, 5.5, 7, 8
#> 16  bean       AWC increasing              40, 100
#> 17  bean       SOC increasing                4, 10
#> 18  bean     slope decreasing                2, 10
```
