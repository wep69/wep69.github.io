# Read crop profiles from a long table

Builds \`agri_crop_profile\` objects from a CSV or data frame with one
row per requirement. Without arguments it returns the six illustrative
rainfed profiles shipped in \`inst/extdata/crop_profile_library.csv\`
(maize, sorghum, cassava, groundnut, cowpea and common bean, with
climate, water, soil and terrain criteria). These thresholds are
author-adapted and must be verified and calibrated before use in
recommendations.

## Usage

``` r
crop_profile_library(crops = NULL, path = NULL, domains = NULL)
```

## Arguments

- crops:

  Optional crop names to return.

- path:

  Optional CSV path or data frame with columns \`crop\`, \`profile_id\`,
  \`scientific_name\`, \`criterion\`, \`domain\`, \`unit\`,
  \`response\`, \`limit1\` to \`limit4\`, and optionally
  \`common_name\`, \`management\`, \`cycle_days\`, \`region\`,
  \`profile_version\`, \`source\`, \`source_id\`.

- domains:

  Optional domains to keep (for example \`c("climate","water")\` for
  station analyses without soil data).

## Value

Named list of \`agri_crop_profile\` objects.

## Examples

``` r
lib <- crop_profile_library(c("maize", "cowpea"), domains = c("climate", "water"))
crop_profile_table(lib)
#>     crop        profile_id criterion  domain          unit   response
#> 1  maize  mz_maize_rainfed       LGP   water           day increasing
#> 2  maize  mz_maize_rainfed   Pseason climate            mm      range
#> 3  maize  mz_maize_rainfed        CV climate dimensionless decreasing
#> 4  maize  mz_maize_rainfed   Tseason climate          degC      range
#> 5  maize  mz_maize_rainfed     Twarm climate          degC decreasing
#> 6 cowpea mz_cowpea_rainfed       LGP   water           day increasing
#> 7 cowpea mz_cowpea_rainfed   Pseason climate            mm      range
#> 8 cowpea mz_cowpea_rainfed        CV climate dimensionless decreasing
#> 9 cowpea mz_cowpea_rainfed   Tseason climate          degC      range
#>                 limits limit1 limit2 limit3 limit4
#> 1              90, 150   90.0 150.00     NA     NA
#> 2 400, 600, 1200, 1800  400.0 600.00   1200   1800
#> 3            0.2, 0.45    0.2   0.45     NA     NA
#> 4       10, 18, 33, 47   10.0  18.00     33     47
#> 5               30, 34   30.0  34.00     NA     NA
#> 6              60, 100   60.0 100.00     NA     NA
#> 7 300, 450, 1000, 1700  300.0 450.00   1000   1700
#> 8             0.3, 0.6    0.3   0.60     NA     NA
#> 9       10, 20, 35, 40   10.0  20.00     35     40
#>                                                                                             source
#> 1                  FAO agro-ecological zones, length of growing period concept (FAO 1978; GAEZ v4)
#> 2                  FAO ECOCROP generic crop parameters (illustrative; verify against the database)
#> 3                              Author-defined reliability penalty on seasonal rainfall variability
#> 4                  FAO ECOCROP generic crop parameters (illustrative; verify against the database)
#> 5 Heat stress on maize in southern Africa (Cairns et al. 2013), monthly-mean proxy; author-defined
#> 6                  FAO agro-ecological zones, length of growing period concept (FAO 1978; GAEZ v4)
#> 7                  FAO ECOCROP generic crop parameters (illustrative; verify against the database)
#> 8                              Author-defined reliability penalty on seasonal rainfall variability
#> 9                  FAO ECOCROP generic crop parameters (illustrative; verify against the database)
#>             source_id
#> 1           LGP-maize
#> 2  ECOCROP-maize-rain
#> 3           REL-maize
#> 4  ECOCROP-maize-temp
#> 5          HEAT-maize
#> 6          LGP-cowpea
#> 7 ECOCROP-cowpea-rain
#> 8          REL-cowpea
#> 9 ECOCROP-cowpea-temp
```
