# Plot individual and group longitudinal trajectories

Plot individual and group longitudinal trajectories

## Usage

``` r
pheno_plot_series(
  data,
  time,
  value,
  subject = NULL,
  group = NULL,
  show_individual = TRUE,
  summary = TRUE
)
```

## Arguments

- data:

  Long-form data frame.

- time:

  Time column.

- value:

  Value column.

- subject:

  Optional subject identifier.

- group:

  Optional treatment/genotype group.

- show_individual:

  Show subject trajectories.

- summary:

  Add group mean trajectories.

## Value

A ggplot object.

## Examples

``` r
d <- subset(pheno_data("growth_series"),trait=="leaf_area")
pheno_plot_series(d,"day","value","plant_id","treatment")

pheno_plot_series(d,"day","value","plant_id","treatment",show_individual=FALSE)

pheno_plot_series(subset(d,treatment=="control"),"day","value","plant_id",summary=TRUE)
```
