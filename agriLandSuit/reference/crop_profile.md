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
