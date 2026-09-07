# Create scientific time-series and panel carriers

Creates explicit scientific data carriers used by the package.

## Usage

``` r
awi_series(time,
  value,
  variable = "value",
  unit = NA_character_,
  id = NA_character_,
  metadata = list())
awi_panel(data, time = "time", value = "value", key = character())
```

## Arguments

- time:

  Time coordinates or the time-column name.

- value:

  Numeric values or the value-column name.

- variable:

  Scientific variable name.

- unit:

  Measurement unit.

- id:

  Optional series identifier.

- metadata:

  Named metadata list.

- data:

  A data frame.

- key:

  Panel key columns.

## Value

An S3 `awi_series` or `awi_panel` object.
