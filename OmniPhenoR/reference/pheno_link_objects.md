# Link detected objects to experimental identity

Link detected objects to experimental identity

## Usage

``` r
pheno_link_objects(objects, identity, by = "image_id")
```

## Arguments

- objects:

  `pheno_detection`, `pheno_instances`, or data frame.

- identity:

  Identity table.

- by:

  Join field, normally `image_id`.

## Value

Joined tibble.

## Examples

``` r
d<-pheno_detection(data.frame(class="leaf",confidence=.9,xmin=1,ymin=1,xmax=5,ymax=5),
  image_id="i1")
id<-pheno_identity("i1","study",plant_id="plant1")
pheno_link_objects(d,id)
#> # A tibble: 1 × 18
#>   image_id class confidence  xmin  ymin  xmax  ymax object_id centroid_x
#>   <chr>    <chr>      <dbl> <dbl> <dbl> <dbl> <dbl>     <int>      <dbl>
#> 1 i1       leaf         0.9     1     1     5     5         1          3
#> # ℹ 9 more variables: centroid_y <dbl>, bbox_width <dbl>, bbox_height <dbl>,
#> #   bbox_area <dbl>, study_id <chr>, environment_id <chr>, plot_id <chr>,
#> #   plant_id <chr>, organ_id <chr>
```
