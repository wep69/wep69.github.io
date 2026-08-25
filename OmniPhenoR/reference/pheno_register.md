# Add registry rows to an experiment

Add registry rows to an experiment

## Usage

``` r
pheno_register(
  experiment,
  what = c("variable", "germplasm", "location", "sensor", "protocol", "person",
    "license"),
  x
)
```

## Arguments

- experiment:

  `pheno_experiment`.

- what:

  Registry: variable, germplasm, location, sensor, protocol, person, or
  license.

- x:

  Data frame returned by the corresponding constructor.

## Value

Updated experiment.

## Examples

``` r
e<-pheno_experiment("d")
e<-pheno_register(e,"variable",
  pheno_variable("v","height","ruler","continuous","cm"));nrow(e$variables)
#> [1] 1
e<-pheno_register(e,"germplasm",pheno_germplasm("g","Cultivar","Zea mays"));nrow(e$germplasm)
#> [1] 1
e<-pheno_register(e,"sensor",pheno_sensor("s","RGB"));nrow(e$sensors)
#> [1] 1
```
