# Reproducibility and interoperability

``` r

library(agriLandSuit)

fp <- land_fingerprint(result)
manifest <- land_manifest(result, inputs = c(climate = "climate.tif"))
land_export(result, "analysis_bundle", format = "bundle")
restored <- land_import("analysis_bundle")
reproducibility_check(restored, manifest)

targets_template("_targets.R", use_geotargets = TRUE)
```

The bundle representation writes substantial spatial payloads to
geospatial files instead of relying on raw external pointers. `targets`
and `geotargets` are optional orchestration tools and remain outside the
mandatory package runtime.
