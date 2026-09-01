# Define one explicit land-use constraint

Constraints are intentionally separate from agronomic criterion scores.
\`exclude\` marks cells as ineligible, \`cap\` limits the maximum
attainable suitability in active cells, and \`penalty\` multiplies
suitability by a factor in \[0, 1\].

## Usage

``` r
land_constraint(
  name,
  source,
  type = c("exclude", "cap", "penalty"),
  operator = c("gt", "ge", "lt", "le", "eq", "ne", "between", "outside", "in", "not_in"),
  threshold = NULL,
  values = NULL,
  cap = NULL,
  penalty = NULL,
  unit = NULL,
  description = NULL,
  metadata = list(),
  validate = TRUE
)
```

## Arguments

- name:

  Stable unique rule name.

- source:

  Source layer key (\`domain.layer\`) or an unambiguous bare layer name
  available in \`agri_land_data\`.

- type:

  One of \`exclude\`, \`cap\`, or \`penalty\`.

- operator:

  Comparison operator: \`gt\`, \`ge\`, \`lt\`, \`le\`, \`eq\`, \`ne\`,
  \`between\`, \`outside\`, \`in\`, or \`not_in\`.

- threshold:

  Numeric threshold. Scalar for simple comparisons and length two for
  \`between\`/\`outside\`.

- values:

  Numeric values used by \`in\`/\`not_in\`.

- cap:

  Maximum suitability permitted in active cells for a \`cap\` rule.

- penalty:

  Multiplicative factor applied in active cells for a \`penalty\` rule.

- unit:

  Optional expected unit for the source layer.

- description:

  Optional human-readable description.

- metadata:

  Optional named list.

- validate:

  Validate the object immediately.

## Value

An \`agri_land_constraint\` object.
