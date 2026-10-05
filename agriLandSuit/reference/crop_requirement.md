# Define one crop requirement

Define one crop requirement

## Usage

``` r
crop_requirement(
  criterion,
  domain = c("climate", "soil", "terrain", "water"),
  unit = "dimensionless",
  response = c("range", "increasing", "decreasing", "categorical"),
  limits = NULL,
  categories = NULL,
  hard_constraint = FALSE,
  uncertainty = NULL,
  source = NULL,
  source_id = NULL,
  evidence_level = NULL,
  notes = NULL,
  strict_unit = getOption("agriLandSuit.strict_units", FALSE)
)
```

## Arguments

- criterion:

  Stable criterion name, e.g. \`temperature_mean\`.

- domain:

  One of climate, soil, terrain, water.

- unit:

  Unit label; see \`unit_registry()\`.

- response:

  Response shape anticipated by suitability functions: range,
  increasing, decreasing, or categorical.

- limits:

  Numeric named vector. For \`range\`, recommended names are
  \`absolute_min\`, \`optimum_min\`, \`optimum_max\`, \`absolute_max\`.

- categories:

  Named numeric mapping for categorical requirements; names are source
  categories and values are suitability scores in \[0, 1\].

- hard_constraint:

  Whether this requirement may become an exclusion rule.

- uncertainty:

  Optional named list describing uncertainty without imposing a
  distribution.

- source:

  Optional bibliographic/provenance text.

- source_id:

  Optional DOI, URL, report ID, database version, or local reference
  key.

- evidence_level:

  Optional evidence descriptor.

- notes:

  Optional notes.

- strict_unit:

  Logical. When \`TRUE\`, a unit that is not in \`unit_registry()\` is
  an error; when \`FALSE\` (default) it is accepted with a warning that
  suggests the canonical label. The default can be set with
  \`options(agriLandSuit.strict_units = TRUE)\`.

## Value

\`agri_crop_requirement\`.

## Examples

``` r
crop_requirement("Pseason", "climate", "mm", "range", limits = c(400, 600, 1200, 1800),
                 source = "example", source_id = "ex-rain")
#> <agri_crop_requirement> Pseason 
#>  domain   : climate 
#>  response : range 
#>  unit     : mm 
#>  limits   : 400, 600, 1200, 1800 
# unregistered units warn with a suggestion, or fail when strict
try(crop_requirement("LGP", "water", "months", "increasing", limits = c(3, 5), strict_unit = TRUE))
#> Error : Unit `months` of criterion `LGP` is not in unit_registry(); did you mean `day`? Monthly counts used as growing-period length should be converted to days.
```
