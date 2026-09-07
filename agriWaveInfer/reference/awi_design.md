# Create a formal scientific design

Creates an S7 `AwiDesign` object with validated scientific semantics.

## Usage

``` r
awi_design(response, predictors = character(), controls = character(), time = "time",
  group = character(), space = character(), crop = character(), units = list(),
  sampling_interval = NA_real_, missing_policy = c("error", "omit"),
  detrending_policy = c("none", "linear", "user"))
```

## Arguments

- response:

  One non-empty response-variable name.

- predictors:

  Character vector naming candidate predictor variables.

- controls:

  Character vector naming conditioning/control variables.

- time:

  One non-empty name identifying the time variable.

- group:

  Optional grouping-variable names.

- space:

  Optional spatial-variable names.

- crop:

  Optional crop or crop-identifier metadata.

- units:

  Named list describing scientific units.

- sampling_interval:

  `NA` or one positive sampling interval in the time-axis units.

- missing_policy:

  Policy for missing values in downstream workflows.

- detrending_policy:

  Declared detrending policy; `"user"` means detrending is supplied
  outside the package.

## Value

An S7 `AwiDesign` object.
