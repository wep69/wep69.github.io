# Define sensor metadata

Define sensor metadata

## Usage

``` r
pheno_sensor(
  sensor_id,
  type,
  manufacturer = NA_character_,
  model = NA_character_,
  serial = NA_character_,
  details = NA_character_
)
```

## Arguments

- sensor_id:

  Identifier.

- type:

  Sensor type.

- manufacturer, model:

  Manufacturer/model.

- serial:

  Optional serial number.

- details:

  Named list or text details.

## Value

One-row data frame.

## Examples

``` r
pheno_sensor("rgb1","RGB camera","Canon","EOS")
#>   sensor_id       type manufacturer model serial details
#> 1      rgb1 RGB camera        Canon   EOS   <NA>    <NA>
pheno_sensor("swir1","hyperspectral","Specim","SWIR",details="900-2500 nm")
#>   sensor_id          type manufacturer model serial     details
#> 1     swir1 hyperspectral       Specim  SWIR   <NA> 900-2500 nm
pheno_sensor("uav1","UAV RGB","DJI","Mavic")
#>   sensor_id    type manufacturer model serial details
#> 1      uav1 UAV RGB          DJI Mavic   <NA>    <NA>
```
