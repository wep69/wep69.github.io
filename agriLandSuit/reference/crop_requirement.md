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
  notes = NULL
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

## Value

\`agri_crop_requirement\`.
