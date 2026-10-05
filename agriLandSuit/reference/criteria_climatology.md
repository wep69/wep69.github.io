# Climatology of yearly criteria

Summarises an \`agri_climate_criteria\` object across seasons. Defaults
follow common practice: median growing period, mean rainfall and
temperature, and the coefficient of variation (CV) of seasonal rainfall.

## Usage

``` r
criteria_climatology(
  x,
  stats = list(LGP = "median", Pseason = "mean", Pannual = "mean", Tseason = "mean",
    Twarm = "mean"),
  cv = c(CV = "Pseason"),
  from = NULL,
  as_raster = TRUE
)
```

## Arguments

- x:

  An \`agri_climate_criteria\` object.

- stats:

  Named list; names are criteria and values are a function or the name
  of one (\`"mean"\`, \`"median"\`, ...). Additional entries such as
  \`P80 = function(v) quantile(v, 0.2)\` are allowed with \`source\`
  given in \`from\`.

- cv:

  Named character vector; names are output names and values are the
  criteria whose CV is computed.

- from:

  Optional named character vector mapping extra output names in
  \`stats\` to source criteria.

- as_raster:

  Return a \`SpatRaster\` when \`x\` came from raster input.

## Value

Numeric matrix (units x statistics) or \`SpatRaster\`.

## Examples

``` r
dates <- seq(as.Date("1991-01-01"), by = "month", length.out = 48)
m <- as.integer(format(dates, "%m"))
f <- rep(c(1, 0.7, 1.2, 0.9), each = 12)
P <- rbind(A = ifelse(m %in% c(11, 12, 1:3), 180, 10) * f, B = ifelse(m %in% c(12, 1:2), 150, 5) * rev(f))
T <- rbind(A = 24 + 2 * cos((m - 1) / 6 * pi), B = 26 + 3 * cos((m - 1) / 6 * pi))
E <- pet_monthly(tmean = T, lat = c(-15, -24), month = m)
cc <- climate_criteria(P, E, tmean = T, dates = dates)
criteria_climatology(cc)
#>      LGP  Pseason  Pannual  Tseason Twarm        CV
#> A 181.25 861.3333 918.6667 25.49282    26 0.1172907
#> B  90.25 444.3333 472.6667 28.23923    29 0.1320676
criteria_climatology(cc, stats = list(LGP = "median", P80 = function(v) quantile(v, 0.2)),
                     from = c(P80 = "Pseason"), cv = c(CV = "Pseason"))
#>      LGP   P80        CV
#> A 181.25 811.8 0.1172907
#> B  90.25 409.2 0.1320676
head(as.data.frame(cc))
#>   unit year    LGP Pseason Pannual  Tseason Twarm PETseason
#> 1    A 1992 181.25   745.0   799.0 25.49282    26   738.772
#> 2    B 1992  90.25   511.5   541.5 28.23923    29  1039.886
#> 3    A 1993 181.25   912.0   964.0 25.49282    26   738.772
#> 4    B 1993  31.00   403.0   434.0 28.23923    29  1039.886
#> 5    A 1994 181.25   927.0   993.0 25.49282    26   738.772
#> 6    B 1994  90.25   418.5   442.5 28.23923    29  1039.886
```
