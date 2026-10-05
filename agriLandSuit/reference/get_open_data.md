# Download open environmental and crop data

Helpers for the open datasets used in national suitability studies: \*
\`get_chirps()\`: CHIRPS v3 monthly rainfall GeoTIFFs (Climate Hazards
Center), read remotely and cropped; \* \`get_terraclimate()\`:
TerraClimate monthly variables (Abatzoglou et al., 2018), historical or
the +2 degC and +4 degC pattern-scaled scenarios, yearly global NetCDF
files downloaded, cropped and deleted; \* \`get_soilgrids()\`: SoilGrids
2.0 aggregated 1 km layers (ISRIC) warped remotely to geographic
coordinates; \* \`get_wdpa()\`: World Database on Protected Areas
country shapefile (most recent of the last few monthly releases); \*
\`get_mapspam()\`: MapSPAM 2020 v2.2 (global) or 2017 v2.1 (sub-Saharan
Africa) yield and harvested area for selected crops; \*
\`get_worldclim_cmip6()\`: WorldClim 2.1 historical and CMIP6 monthly
climatologies through \`geodata\`; \* \`get_gadm()\` and
\`get_elevation()\`: administrative boundaries and 30 arc-second
elevation with slope, through \`geodata\`; \* \`get_gwl_table()\`: CMIP6
global warming level crossing years (Hauser et al., doi
10.5281/zenodo.3591806) from GitHub.

## Usage

``` r
get_chirps(
  years,
  bbox,
  dest,
  months = 1:12,
  base_url = "https://data.chc.ucsb.edu/products/CHIRPS/v3.0/monthly/",
  overwrite = FALSE,
  quiet = TRUE
)

get_terraclimate(
  vars,
  years,
  bbox,
  dest,
  scenario = c("historical", "plus2C", "plus4C"),
  stack = TRUE,
  server = c(historical = "https://climate.northwestknowledge.net/TERRACLIMATE-DATA/",
    thredds =
    "http://thredds.northwestknowledge.net:8080/thredds/fileServer/TERRACLIMATE_ALL/"),
  overwrite = FALSE,
  quiet = TRUE
)

get_soilgrids(
  vars = c("phh2o", "sand", "clay", "soc", "bdod", "cec"),
  bbox,
  dest,
  depths = c("0-5cm", "5-15cm", "15-30cm"),
  res = 0.008333333,
  base_url = "https://files.isric.org/soilgrids/latest/data_aggregated/1000m/",
  overwrite = FALSE
)

get_wdpa(
  iso3,
  dest,
  months_back = 3L,
  date = Sys.Date(),
  base_url = "https://d1gam3xoknrgr2.cloudfront.net/current/",
  quiet = TRUE
)

get_mapspam(
  year = c(2020, 2017),
  bbox,
  dest,
  crops = c("MAIZ", "SORG", "CASS", "GROU", "COWP", "BEAN"),
  technologies = c("A", "R", "I"),
  urls = NULL,
  quiet = TRUE
)

get_worldclim_cmip6(
  gcms,
  ssps = c("245", "585"),
  periods = c("2021-2040", "2041-2060"),
  bbox,
  dest,
  wc_vars = c("tmin", "tmax", "prec"),
  wc_res = 10,
  historical = TRUE
)

get_gadm(iso3, dest, level = 1L)

get_elevation(iso3, dest)

get_gwl_table(dest, repo = "mathause/cmip_warming_levels", quiet = TRUE)
```

## Arguments

- years:

  Years to download.

- bbox:

  Window as \`c(xmin, xmax, ymin, ymax)\`, \`SpatExtent\`, raster or
  vector.

- dest:

  Destination folder (created if needed).

- months:

  Months to download.

- base_url:

  Root of the product on the server.

- overwrite:

  Re-download existing files.

- quiet:

  Suppress download progress.

- vars:

  TerraClimate variables (\`ppt\`, \`pet\`, \`tmax\`, \`tmin\`, ...) or
  SoilGrids properties.

- scenario:

  \`historical\`, \`plus2C\` or \`plus4C\`.

- stack:

  Also write one multi-year stack per variable.

- server:

  URL roots for historical files and for the THREDDS file server.

- depths:

  SoilGrids depth intervals.

- res:

  Output resolution in degrees.

- iso3:

  ISO 3166-1 alpha-3 country code.

- months_back:

  Number of monthly WDPA releases tried, newest first.

- date:

  Reference date for the newest release.

- year:

  MapSPAM release year (2020 or 2017).

- crops:

  MapSPAM crop codes (for example \`MAIZ\`, \`SORG\`, \`CASS\`).

- technologies:

  Production systems: \`A\` all, \`R\` rainfed, \`I\` irrigated.

- urls:

  Named URLs of the zipped GeoTIFF archives (\`yield\`, \`harv\`).

- gcms, ssps, periods:

  CMIP6 models, pathways (\`"245"\`, \`"585"\`) and periods
  (\`"2021-2040"\`, ...).

- wc_vars:

  WorldClim variables (\`tmin\`, \`tmax\`, \`prec\`).

- wc_res:

  WorldClim resolution in minutes (10, 5, 2.5).

- historical:

  Also download the WorldClim 2.1 historical climatology.

- level:

  Administrative level.

- repo:

  GitHub repository holding the warming-level tables.

## Value

Path(s) of the files written, invisibly.

## Details

Check the licence and citation of each dataset before redistribution.

## Examples

``` r
if (FALSE) { # \dontrun{
bb <- c(30, 41.5, -27.2, -10.2)
get_terraclimate("pet", 1991:1992, bb, dest = "data_raw")
get_gwl_table("data_raw")
} # }
```
