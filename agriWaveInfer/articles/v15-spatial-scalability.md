# Memory-Aware Spatial Scalability

## Avoid the full four-dimensional cube by default

A naive spatial wavelet workflow can imply a coefficient object indexed
by location, time and scale. For large grids this becomes expensive even
before uncertainty fields and multiple drivers are considered. Version
0.8.0 therefore treats the requested spatial summary as the primary
product and retains complete time-frequency fields only when explicitly
requested.

## Declare a memory budget

``` r

sp <- awi_spatial_spec(
  chunk_size = 64,
  max_memory_mb = 1024,
  retain_fields = FALSE
)
```

The package estimates the active working set conservatively. If 64
locations would exceed the declared budget, the effective chunk size is
reduced automatically and recorded in `memory_plan`.

## Chunking must be numerically neutral

``` r

a <- awi_spatial_coherence(
  response, enso, coords,
  spatial_spec = awi_spatial_spec(chunk_size = 1)
)

b <- awi_spatial_coherence(
  response, enso, coords,
  spatial_spec = awi_spatial_spec(chunk_size = 32)
)

all.equal(a$summary$awc, b$summary$awc, tolerance = 1e-12)
```

Chunk size is an execution choice, not a scientific parameter. The test
suite therefore treats chunk invariance as a metamorphic property.

## Parallel execution is opt-in

``` r

sp_parallel <- awi_spatial_spec(
  chunk_size = 16,
  parallel = TRUE,
  workers = 2
)
```

Serial execution is the reference path. Parallel PSOCK execution is
explicitly requested and must reproduce the serial summaries during
local validation.

## Optional spatial ecosystems

`terra` is suitable for large raster/vector workflows and current CRAN
releases explicitly support large-file processing (Hijmans et al. 2026).
`stars` provides spatiotemporal array and data-cube structures (Pebesma
et al. 2026). Both remain optional so `agriWaveInfer` can be installed
without a mandatory compiled geospatial stack.

Hijmans, Robert J., Andrew Brown, A. Marcia Barbosa, Emanuele Cordano,
and Krzysztof Dyba. 2026. *Terra: Spatial Data Analysis*.
<https://doi.org/10.32614/CRAN.package.terra>.

Pebesma, Edzer, Michael Sumner, Etienne Racine, Adriano Fantini, David
Blodgett, and Krzysztof Dyba. 2026. *Stars: Spatiotemporal Arrays,
Raster and Vector Data Cubes*.
<https://doi.org/10.32614/CRAN.package.stars>.
