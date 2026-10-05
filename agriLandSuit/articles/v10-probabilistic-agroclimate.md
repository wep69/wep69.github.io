# Probabilistic Suitability from Monthly Climate Series

## Scope

Version 1.1.0 adds the steps that turn monthly station or gridded
climate into yearly agroclimatic criteria, an interannual ensemble of
suitability scores and decision metrics such as the probability of an
unsuitable season. The workflow below uses two synthetic stations; the
same calls apply to matrices of raster cells or to multi-layer
`SpatRaster` objects. The values are illustrative.

## From monthly series to yearly criteria

``` r

set.seed(10)
dates <- seq(as.Date("1991-01-01"), by = "month", length.out = 12 * 11)
m <- as.integer(format(dates, "%m"))
wet <- m %in% c(11, 12, 1, 2, 3)
P <- rbind(humid = ifelse(wet, rgamma(length(m), 9, 0.05), rgamma(length(m), 1, 0.1)),
           dry   = ifelse(wet, rgamma(length(m), 4, 0.03), rgamma(length(m), 1, 0.12)))
T <- rbind(humid = 24 + 3 * cos((m - 1) / 6 * pi), dry = 26 + 4 * cos((m - 1) / 6 * pi))
P[1, 30] <- NA                          # one missing month
P <- fill_monthly_gaps(P, m)            # explicit, recorded gap filling
attr(P, "n_filled")
#> humid   dry 
#>     1     0
E <- pet_monthly(tmean = T, lat = c(-15, -24), month = m, method = "thornthwaite")
cc <- climate_criteria(P, E, tmean = T, dates = dates, awc = 100)
cc
#> <agri_climate_criteria> 2 units x 10 seasons ( 1992 - 2001 )
#>  criteria: LGP, Pseason, Pannual, Tseason, Twarm, PETseason
round(criteria_climatology(cc), 2)
#>          LGP Pseason Pannual Tseason Twarm   CV
#> humid 181.25  926.83 1013.11   26.24    27 0.21
#> dry    90.62  687.31  741.93   28.99    30 0.25
```

The growing period (`LGP`) follows the FAO concept with a monthly
Thornthwaite-Mather bucket: a month is humid when rainfall plus the
water stored at its start reaches half of PET. The agricultural year
runs from July to June and is labelled by the year of January.

## Crop profiles and the interannual ensemble

``` r

lib <- crop_profile_library(c("maize", "sorghum"), domains = c("climate", "water"))
crop_profile_table(lib)[, c("crop", "criterion", "response", "limits")]
#>      crop criterion   response               limits
#> 1   maize       LGP increasing              90, 150
#> 2   maize   Pseason      range 400, 600, 1200, 1800
#> 3   maize        CV decreasing            0.2, 0.45
#> 4   maize   Tseason      range       10, 18, 33, 47
#> 5   maize     Twarm decreasing               30, 34
#> 6 sorghum       LGP increasing              75, 120
#> 7 sorghum   Pseason      range 300, 500, 1000, 3000
#> 8 sorghum        CV decreasing           0.25, 0.55
#> 9 sorghum   Tseason      range        8, 27, 35, 40
ens <- lapply(lib, function(p) ensemble_from_years(cc, p, drop = "CV"))
round(suit_risk(ens$maize, probs = numeric()), 3)
#>       P_N P_S2plus  mean    sd
#> humid 0.0      1.0 0.982 0.048
#> dry   0.6      0.4 0.304 0.420
```

Each season is one member. The rainfall CV is removed from the members
because their spread already expresses interannual variability.
[`suit_risk()`](https://wep69.github.io/agriLandSuit/reference/suit_risk.md)
reports the probability of an unsuitable season, `P_N`, and of at least
moderate suitability, `P_S2plus`, with left-closed thresholds;
[`class_probability()`](https://wep69.github.io/agriLandSuit/reference/class_probability.md)
keeps the right-closed classes of
[`suit_classify()`](https://wep69.github.io/agriLandSuit/reference/suit_classify.md).

``` r

head(suit_calendar(ens$maize))
#>    unit member year     score class
#> 1 humid  Y1992 1992 1.0000000    S1
#> 2   dry  Y1992 1992 1.0000000    S1
#> 3 humid  Y1993 1993 1.0000000    S1
#> 4   dry  Y1993 1993 0.5041667    S2
#> 5 humid  Y1994 1994 0.9704572    S1
#> 6   dry  Y1994 1994 0.5041667    S2
```

## Sources of spread and sensitivity to thresholds

Members built for several soil water capacities share one design, so the
spread can be split between seasons and soil assumptions.

``` r

cc75 <- climate_criteria(P, E, tmean = T, dates = dates, awc = 75)
cc150 <- climate_criteria(P, E, tmean = T, dates = dates, awc = 150)
sc <- do.call(cbind, lapply(list(cc75, cc, cc150), function(x) ensemble_from_years(x, lib$maize)$draws))
e2 <- ensemble_from_scores(sc, design = ensemble_design(year = cc$years, AWC = c(75, 100, 150)))
round(uncertainty_decompose(e2)$shares, 3)
#>      year AWC
#> [1,]    1   0
#> [2,]    1   0
round(uncertainty_decompose(e2, method = "anova")$shares, 3)
#>      year AWC residual
#> [1,]    1   0        0
#> [2,]    1   0        0
```

``` r

clim <- as.data.frame(criteria_climatology(cc))
ts <- threshold_sensitivity(lib$maize, as.list(clim), yearly = cc, unit_names = rownames(clim))
head(ts[order(-ts$max_abs_change_PN), c("criterion", "limit_index", "factor", "max_abs_change", "max_abs_change_PN")])
#> <agri_threshold_sensitivity> 6 perturbations of 2 criteria 
#>  criterion limit_index factor max_abs_change max_abs_change_PN
#>        LGP           1    0.9    0.129076087                 0
#>        LGP           1    1.1    0.010416667                 0
#>        LGP           2    0.9    0.003472222                 0
#>        LGP           2    1.1    0.002083333                 0
#>    Pseason           1    0.9    0.000000000                 0
#>    Pseason           1    1.1    0.000000000                 0
```

## Stations in the formal raster workflow

``` r

st <- data.frame(station = rownames(clim), lon = c(35, 33), lat = c(-15, -24), clim)
lp <- land_points(st, climate = c("Pseason", "CV", "Tseason", "Twarm"), water = "LGP", id = "station",
                  units = c(climate.Pseason = "mm", climate.CV = "dimensionless", climate.Tseason = "degC",
                            climate.Twarm = "degC", water.LGP = "day"))
ls <- land_suitability(crop_criteria(lp, lib$maize), method = "limiting")
land_point_values(ls, lp)
#>   point    id  x   y suitability
#> 1     1 humid 35 -15  0.97182722
#> 2     2   dry 33 -24  0.01041667
```

## Further steps

[`change_factors()`](https://wep69.github.io/agriLandSuit/reference/change_factors.md),
[`apply_change_factors()`](https://wep69.github.io/agriLandSuit/reference/apply_change_factors.md)
and
[`scenario_ensemble()`](https://wep69.github.io/agriLandSuit/reference/scenario_ensemble.md)
add climate scenarios;
[`conditional_suitability()`](https://wep69.github.io/agriLandSuit/reference/conditional_suitability.md)
tests the influence of a climate mode;
[`validate_suitability()`](https://wep69.github.io/agriLandSuit/reference/validate_suitability.md)
compares maps with gridded crop statistics using a spatial block
bootstrap;
[`planting_window()`](https://wep69.github.io/agriLandSuit/reference/planting_window.md)
turns onset and cessation dates into planting windows; the `plot_*()`
functions and
[`save_publication_figure()`](https://wep69.github.io/agriLandSuit/reference/save_publication_figure.md)
produce figures, and the `get_*()` functions download the open datasets
used in national studies.
