# agriLandSuit 0.1.0: Spatial and Agronomic Foundations

``` r

library(terra)
```

    ## Warning: package 'terra' was built under R version 4.6.1

``` r

library(agriLandSuit)
set.seed(101)
```

## Purpose

Version 0.1.0 defines the objects that later suitability algorithms
consume. It deliberately avoids publishing unverified agronomic
threshold tables.

## A synthetic land object

``` r

t <- rast(ncols=10,nrows=10,xmin=0,xmax=10,ymin=0,ymax=10,crs="EPSG:31985")
values(t) <- runif(ncell(t))
names(t) <- "demo_climate"

s <- t
values(s) <- runif(ncell(s))
names(s) <- "demo_soil"

land <- land_data(
  climate=t,
  soil=s,
  units=c("climate.demo_climate"="dimensionless", "soil.demo_soil"="dimensionless"),
  metadata=list(example="synthetic")
)
land
```

    ## <agri_land_data>
    ##  domains : climate, soil 
    ##  layers  : 2 
    ##  geometry: 10 x 10 cells; resolution 1 x 1 
    ##  CRS     : +proj=utm +zone=25 +south +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +units=m +no_defs

``` r

land_validate(land)
```

    ## <agri_validation> OK
    ## No issues detected.

## A traceable crop profile

The following values are synthetic and exist only to demonstrate the
data contract.

``` r

r1 <- crop_requirement(
  "demo_climate", "climate", "dimensionless", "range",
  limits=c(0, .25, .75, 1),
  source="Synthetic demonstration only", source_id="internal-demo"
)
r2 <- crop_requirement(
  "demo_soil", "soil", "dimensionless", "range",
  limits=c(0, .2, .8, 1),
  source="Synthetic demonstration only", source_id="internal-demo"
)

p <- crop_profile("demo_crop", "Demo species", requirements=list(r1,r2))
p
```

    ## <agri_crop_profile> demo_crop 
    ##  crop        : Demo species  
    ##  management  : rainfed 
    ##  requirements: 2 
    ##  profile ver. : 0.1

``` r

crop_validate(p, require_sources=TRUE)
```

    ## <agri_validation> OK
    ## No issues detected.

## Why suitability is not calculated yet

A land-suitability model becomes difficult to audit when data cleaning,
agronomic thresholds, scoring, restrictions and aggregation are
introduced simultaneously. The 0.1.0 release froze the input contracts
first. Version 0.2.0 now transforms those requirements into
criterion-level membership/suitability functions while retaining the
same contracts.
