# Criterion Engine and Fuzzy Suitability

## Purpose

Version 0.2.0 standardizes each agronomic criterion to a suitability
scale from zero to one. It does not combine criteria into a final
suitability index.

## Reference fuzzy functions

``` r

x <- seq(0, 10, by=0.5)
membership_triangular(x, 0, 5, 10)
membership_trapezoid(x, 0, 3, 7, 10)
membership_increasing(x, 2, 8)
membership_decreasing(x, 2, 8)
membership_gaussian(x, center=5, sigma=1.5)
```

## Requirement-driven scoring

A crop requirement describes agronomic semantics. The criterion engine
converts that description to the appropriate membership function.

``` r

req <- crop_requirement(
  criterion="synthetic_temperature",
  domain="climate",
  unit="degC",
  response="range",
  limits=c(absolute_min=10, optimum_min=20,
           optimum_max=28, absolute_max=36),
  source="Synthetic teaching requirement",
  source_id="internal-demo"
)

curve <- criterion_curve(req)
head(curve)
```

## Spatial scoring

``` r

library(terra)
r <- rast(ncols=10,nrows=10,xmin=0,xmax=10,ymin=0,ymax=10,crs="EPSG:31985")
values(r) <- seq(8,38,length.out=ncell(r))
names(r) <- "synthetic_temperature"

land <- land_data(
  climate=r,
  units=c("climate.synthetic_temperature"="degC")
)

z <- land_criterion(land, req)
plot(z)
```

## Optional Python

``` r

python_backend_status("fuzzy")
z_py <- land_criterion(land, req, engine="python")
```

Python is not installed or modified automatically. Native R remains the
default.

## Scientific interpretation

A criterion score close to one means that the environmental value has
high membership in the suitability concept defined by the requirement.
It does **not** by itself mean that the location is suitable overall. A
different criterion may be strongly limiting, a hard restriction may
exclude the location, or uncertainty may alter the decision. Those
operations are intentionally deferred to later releases.
