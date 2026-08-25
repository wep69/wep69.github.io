# Create a longitudinal phenotyping series

Creates the canonical 0.4.0 object used to preserve biological identity,
acquisition time, trait identity, units, source and quality flags.

## Usage

``` r
pheno_series(
  data,
  id,
  time,
  trait,
  value,
  unit = NULL,
  group = NULL,
  source = NULL,
  qc = NULL
)
```

## Arguments

- data:

  Data frame in long form.

- id:

  Column containing the biological/experimental subject identifier.

- time:

  Column containing numeric, Date, or POSIXct time.

- trait:

  Column containing trait names.

- value:

  Column containing numeric trait values.

- unit:

  Optional unit column or scalar unit label.

- group:

  Optional additional identity columns, such as plot, block, treatment,
  genotype, or environment.

- source:

  Optional source column or scalar label.

- qc:

  Optional quality-flag column.

## Value

A `pheno_series` object.

## Examples

``` r
d <- pheno_data("growth_series")
s1 <- pheno_series(d, "plant_id", "day", "trait", "value")
s2 <- pheno_series(d, "plant_id", "day", "trait", "value", group = c("treatment", "block"))
s3 <- pheno_series(d, "plant_id", "date", "trait", "value", unit = "relative units")
```
