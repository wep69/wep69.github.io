# Spatial Wavelet Inference for Agroclimatic Systems

## Why a spatial layer?

Agroclimatic relationships rarely have the same magnitude, dominant
driver or lag across an entire region. Recent agricultural wavelet work
has explicitly mapped dominant teleconnections across agro-climatic
zones after BWC/MWC screening (Sankaran et al. 2026). `agriWaveInfer`
0.8.0 adds this spatial synthesis as a reproducible analytical layer
rather than leaving it to ad hoc post-processing.

## Represent response series by location

``` r

response <- list(
  pixel_001 = awi_series(time, ndvi_001, variable = "NDVI"),
  pixel_002 = awi_series(time, ndvi_002, variable = "NDVI"),
  pixel_003 = awi_series(time, ndvi_003, variable = "NDVI")
)

coords <- data.frame(
  id = names(response),
  x = c(-35.70, -35.65, -35.60),
  y = c(-6.97, -6.97, -6.97)
)
```

The `id` values are part of the scientific key. Coordinates are
reconciled to the response names before analysis.

## Compute a spatial coherence summary

``` r

sp <- awi_spatial_spec(
  chunk_size = 16,
  max_memory_mb = 512,
  retain_fields = FALSE,
  crs = "EPSG:4326"
)

z <- awi_spatial_coherence(
  response = response,
  driver = enso,
  coords = coords,
  period_range = c(24, 84),
  spatial_spec = sp
)

z$summary
```

Each location is summarized by AWC, PoSC when significance is available,
persistence, valid-domain coverage, representative phase lag, circular
concentration, and direction.

## Map coherence

``` r

awc <- awi_coherence_map(z, metric = "awc", output = "data.frame")
```

When coordinates form a regular grid, optional raster representations
are available through `terra` and `stars` (Hijmans et al. 2026; Pebesma
et al. 2026). These are representation backends. The tabular summary
remains canonical.

## Map the dominant driver

``` r

dm <- awi_driver_map(
  response = response,
  drivers = list(ENSO = enso, PDO = pdo, AMO = amo),
  coords = coords,
  method = "single",
  driver_spec = awi_driver_spec(metric = "awc"),
  spatial_spec = sp
)
```

`dominant_driver` means the strongest eligible multiscale association
under the declared metric. It is not a causal attribution.

## Map lag

``` r

lm <- awi_lag_map(z)
```

The lag is derived from circular wavelet phase and inherits the
phase-concentration and ambiguity safeguards of version 0.5.0. A lag map
is therefore not a map of causal response times.

Hijmans, Robert J., Andrew Brown, A. Marcia Barbosa, Emanuele Cordano,
and Krzysztof Dyba. 2026. *Terra: Spatial Data Analysis*.
<https://doi.org/10.32614/CRAN.package.terra>.

Pebesma, Edzer, Michael Sumner, Etienne Racine, Adriano Fantini, David
Blodgett, and Krzysztof Dyba. 2026. *Stars: Spatiotemporal Arrays,
Raster and Vector Data Cubes*.
<https://doi.org/10.32614/CRAN.package.stars>.

Sankaran, Adarsh, Fathima Fazal, Aleena Johnson, Venkataramana Sridhar,
Divyasree Prasad, and Aakhila Fathima Abdul Rasheed. 2026.
“Wavelet-Based Assessment of Climate Oscillation and Meteorological
Influences on Cereal Yield Variability Across India’s Agro-Climatic
Zones.” *Theoretical and Applied Climatology* 157: 147.
<https://doi.org/10.1007/s00704-026-06085-2>.
