# Edit or subset a crop profile

\`crop_profile_update()\` changes the limits of one requirement, either
by replacing them, multiplying selected limits or shifting them by a
constant, and records the change in the profile metadata.
\`crop_profile_subset()\` keeps or drops requirements, for example to
remove the rainfall CV from yearly ensemble members.

## Usage

``` r
crop_profile_update(
  profile,
  criterion,
  limits = NULL,
  multiply = NULL,
  shift = NULL,
  index = NULL,
  suffix = NULL
)

crop_profile_subset(profile, keep = NULL, drop = NULL, suffix = NULL)
```

## Arguments

- profile:

  An \`agri_crop_profile\`.

- criterion:

  Criterion to edit.

- limits:

  Replacement limits.

- multiply:

  Multiplicative factor applied to the limits in \`index\`.

- shift:

  Additive shift applied to the limits in \`index\`.

- index:

  Limit positions edited by \`multiply\` or \`shift\`; default all.

- suffix:

  Text appended to the requirement \`source_id\` and profile ID.

- keep, drop:

  Criteria to keep or drop (give one of them).

## Value

A new \`agri_crop_profile\`.

## Examples

``` r
p <- crop_profile_library("maize")$maize
p2 <- crop_profile_update(p, "Twarm", shift = 1, suffix = "_heat_plus1")
crop_profile_table(p2)[crop_profile_table(p2)$criterion == "Twarm", c("criterion", "limits", "source_id")]
#>   criterion limits             source_id
#> 5     Twarm 31, 35 HEAT-maize_heat_plus1
p3 <- crop_profile_update(p, "Pseason", multiply = 1.1, index = 2)
p3$metadata$edits
#> [[1]]
#> [[1]]$criterion
#> [1] "Pseason"
#> 
#> [[1]]$from
#> [1]  400  600 1200 1800
#> 
#> [[1]]$to
#> [1]  400  660 1200 1800
#> 
#> 
crop_profile_subset(p, drop = c("CV", "pH"))
#> <agri_crop_profile> mz_maize_rainfed 
#>  crop        : Zea mays  
#>  management  : rainfed 
#>  requirements: 7 
#>  profile ver. : 1.1-illustrative 
```
