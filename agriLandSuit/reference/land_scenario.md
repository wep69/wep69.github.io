# Create a land scenario

A scenario binds one validated \`agri_land_data\` object to explicit
scenario metadata. Scenario metadata are kept separate from crop
requirements and decision weights so the same crop model can be
evaluated consistently across baseline and future conditions.

## Usage

``` r
land_scenario(
  land,
  scenario_id,
  model = "unspecified",
  period = "unspecified",
  management = "unspecified",
  pathway = NULL,
  baseline = FALSE,
  metadata = list()
)
```

## Arguments

- land:

  An \`agri_land_data\` object.

- scenario_id:

  Unique scenario identifier.

- model:

  Climate model, observational product, or scenario data source.

- period:

  Period label, for example \`"1991-2020"\` or \`"2041-2070"\`.

- management:

  Management label such as \`"rainfed"\` or \`"irrigated"\`.

- pathway:

  Optional forcing/pathway label such as \`"SSP2-4.5"\`.

- baseline:

  Logical; whether this is the reference scenario.

- metadata:

  Additional named metadata.

## Value

An object of class \`agri_land_scenario\`.
