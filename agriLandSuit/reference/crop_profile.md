# Create a versioned crop profile

Create a versioned crop profile

## Usage

``` r
crop_profile(
  profile_id,
  scientific_name,
  common_name = NULL,
  cultivar = NULL,
  management = c("rainfed", "irrigated", "any"),
  cycle_days = NULL,
  region = NULL,
  requirements = list(),
  profile_version = "0.1",
  metadata = list(),
  validate = TRUE
)
```

## Arguments

- profile_id:

  Stable profile identifier.

- scientific_name:

  Scientific name.

- common_name:

  Optional common name.

- cultivar:

  Optional cultivar/genotype.

- management:

  rainfed, irrigated, or any.

- cycle_days:

  Optional crop cycle length.

- region:

  Optional region for which requirements are intended.

- requirements:

  List of \`agri_crop_requirement\` objects.

- profile_version:

  Version of the agronomic requirement profile.

- metadata:

  Additional named metadata.

- validate:

  Logical.

## Value

\`agri_crop_profile\`.

## Examples

``` r
crop <- crop_profile("demo_maize", "Zea mays", common_name = "maize", requirements = list(
  crop_requirement("Pseason", "climate", "mm", "range", c(400, 600, 1200, 1800), source = "illustrative", source_id = "demo-rain"),
  crop_requirement("LGP", "water", "day", "increasing", c(90, 150), source = "illustrative", source_id = "demo-lgp"),
  crop_requirement("pH", "soil", "pH", "range", c(4.5, 5.5, 7.5, 8.5), source = "illustrative", source_id = "demo-ph"),
  crop_requirement("slope", "terrain", "degree", "decreasing", c(5, 20), source = "illustrative", source_id = "demo-slope")))
crop
#> <agri_crop_profile> demo_maize 
#>  crop        : Zea mays  
#>  management  : rainfed 
#>  requirements: 4 
#>  profile ver. : 0.1 
as.data.frame(crop)
#>   profile_id scientific_name cultivar management criterion  domain   unit
#> 1 demo_maize        Zea mays     <NA>    rainfed   Pseason climate     mm
#> 2 demo_maize        Zea mays     <NA>    rainfed       LGP   water    day
#> 3 demo_maize        Zea mays     <NA>    rainfed        pH    soil     pH
#> 4 demo_maize        Zea mays     <NA>    rainfed     slope terrain degree
#>     response hard_constraint       source  source_id
#> 1      range           FALSE illustrative  demo-rain
#> 2 increasing           FALSE illustrative   demo-lgp
#> 3      range           FALSE illustrative    demo-ph
#> 4 decreasing           FALSE illustrative demo-slope
```
