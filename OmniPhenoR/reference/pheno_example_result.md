# Load a compact frozen vignette result

Load a compact frozen vignette result

## Usage

``` r
pheno_example_result(id)
```

## Arguments

- id:

  Artifact identifier or filename stem.

## Value

R object/data frame depending on stored extension.

## Examples

``` r
pheno_example_result("mock_yolo_leaf_detection")
#>    image_id object_id  class confidence xmin ymin xmax ymax    engine
#> 1 plant_001         1   leaf       0.96   18   14   72   93 mock_yolo
#> 2 plant_001         2   leaf       0.89   78   19  126   98 mock_yolo
#> 3 plant_001         3 flower       0.84   52    8   70   27 mock_yolo
#>                model_id
#> 1 frozen_teaching_model
#> 2 frozen_teaching_model
#> 3 frozen_teaching_model
head(pheno_example_result("cross_engine_demo"))
#>            engine dice  iou area_bias runtime_sec                   note
#> 1      native_exg 0.94 0.89    -0.012       0.018 frozen teaching result
#> 2      torch_unet 0.97 0.94    -0.004       0.052 frozen teaching result
#> 3 plantcv_adapter 0.95 0.91     0.008       0.074  frozen adapter result
head(pheno_example_result("instance_demo"))
#>    image_id object_id class confidence area_px severity_percent
#> 1 plant_001         1  leaf       0.95    1820              4.8
#> 2 plant_001         2  leaf       0.92    1765              7.2
```
