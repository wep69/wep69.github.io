# Validate suitability against observed crop data

Compares one or more suitability layers with gridded crop statistics
such as MapSPAM yield and harvested area. Suitability is averaged onto
the reference grid. Reported statistics: \* Spearman correlation between
each suitability layer and yield, on cells with harvested area of at
least \`min_area\` and positive yield, with a percentile confidence
interval from a spatial block bootstrap (blocks of \`block_size\`
degrees, resampled with replacement); \* Spearman correlation between
the first layer and harvested area, on all complete cells; \*
enrichment: share of harvested area on land with score \>= \`threshold\`
divided by the share of land with score \>= \`threshold\` (values above
1 mean the crop is concentrated on land rated suitable).

## Usage

``` r
validate_suitability(
  suit,
  yield,
  area = NULL,
  min_area = 100,
  block_size = 1,
  B = 2000L,
  conf = 0.9,
  threshold = 0.5,
  seed = NULL,
  resample_method = "average"
)
```

## Arguments

- suit:

  \`SpatRaster\` with one or more suitability layers (scores in \[0,
  1\]).

- yield:

  \`SpatRaster\` with yield (one layer).

- area:

  Optional \`SpatRaster\` with harvested area (ha), same grid as
  \`yield\`.

- min_area:

  Minimum harvested area (ha) for the yield correlation.

- block_size:

  Block size (map units, degrees for geographic grids).

- B:

  Bootstrap replicates.

- conf:

  Confidence level of the interval.

- threshold:

  Score defining suitable land for enrichment.

- seed:

  Optional seed; \`NULL\` uses the current random stream.

- resample_method:

  Method used to bring \`suit\` onto the yield grid.

## Value

An \`agri_suit_validation\` list with \`summary\` (one row) and \`data\`
(cell table).

## Details

Gridded crop statistics are partly allocated with suitability
information, so the test is independent in source, not fully
independent.

## Examples

``` r
r <- terra::rast(ncols = 12, nrows = 12, xmin = 30, xmax = 33, ymin = -20, ymax = -17, crs = "EPSG:4326")
set.seed(7)
s <- r; terra::values(s) <- runif(144); names(s) <- "maize"
y <- r; terra::values(y) <- 800 + 1500 * terra::values(s) + rnorm(144, 0, 200)
h <- r; terra::values(h) <- 300 * terra::values(s) + 50
v <- validate_suitability(s, y, h, min_area = 100, B = 199, seed = 1)
v$summary
#>   n_cells_yield n_blocks rho_yield_maize ci_lo_maize ci_hi_maize rho_area
#> 1           127        9       0.8979717   0.8504675   0.9214373        1
#>   ci_lo_area ci_hi_area share_area_suitable share_land_suitable enrichment
#> 1          1          1           0.6900467           0.5208333    1.32489
```
