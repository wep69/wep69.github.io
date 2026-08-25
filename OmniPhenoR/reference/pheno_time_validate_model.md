# Build temporal validation splits

Build temporal validation splits

## Usage

``` r
pheno_time_validate_model(
  data,
  subject,
  time,
  mode = c("leave_subject_out", "leave_time_out", "forward")
)
```

## Arguments

- data:

  Data frame.

- subject:

  Subject column.

- time:

  Time column.

- mode:

  `leave_subject_out`, `leave_time_out`, or `forward`.

## Value

List of train/test index splits.

## Examples

``` r
d<-data.frame(plant=rep(1:3,each=4),day=rep(1:4,3))
pheno_time_validate_model(d,"plant","day","leave_subject_out")
#> [[1]]
#> [[1]]$train
#> [1]  5  6  7  8  9 10 11 12
#> 
#> [[1]]$test
#> [1] 1 2 3 4
#> 
#> [[1]]$label
#> [1] "1"
#> 
#> 
#> [[2]]
#> [[2]]$train
#> [1]  1  2  3  4  9 10 11 12
#> 
#> [[2]]$test
#> [1] 5 6 7 8
#> 
#> [[2]]$label
#> [1] "2"
#> 
#> 
#> [[3]]
#> [[3]]$train
#> [1] 1 2 3 4 5 6 7 8
#> 
#> [[3]]$test
#> [1]  9 10 11 12
#> 
#> [[3]]$label
#> [1] "3"
#> 
#> 
pheno_time_validate_model(d,"plant","day","leave_time_out")
#> [[1]]
#> [[1]]$train
#> [1]  2  3  4  6  7  8 10 11 12
#> 
#> [[1]]$test
#> [1] 1 5 9
#> 
#> [[1]]$label
#> [1] "1"
#> 
#> 
#> [[2]]
#> [[2]]$train
#> [1]  1  3  4  5  7  8  9 11 12
#> 
#> [[2]]$test
#> [1]  2  6 10
#> 
#> [[2]]$label
#> [1] "2"
#> 
#> 
#> [[3]]
#> [[3]]$train
#> [1]  1  2  4  5  6  8  9 10 12
#> 
#> [[3]]$test
#> [1]  3  7 11
#> 
#> [[3]]$label
#> [1] "3"
#> 
#> 
#> [[4]]
#> [[4]]$train
#> [1]  1  2  3  5  6  7  9 10 11
#> 
#> [[4]]$test
#> [1]  4  8 12
#> 
#> [[4]]$label
#> [1] "4"
#> 
#> 
pheno_time_validate_model(d,"plant","day","forward")
#> [[1]]
#> [[1]]$train
#> [1] 1 5 9
#> 
#> [[1]]$test
#> [1]  2  6 10
#> 
#> [[1]]$label
#> [1] "2"
#> 
#> 
#> [[2]]
#> [[2]]$train
#> [1]  1  2  5  6  9 10
#> 
#> [[2]]$test
#> [1]  3  7 11
#> 
#> [[2]]$label
#> [1] "3"
#> 
#> 
#> [[3]]
#> [[3]]$train
#> [1]  1  2  3  5  6  7  9 10 11
#> 
#> [[3]]$test
#> [1]  4  8 12
#> 
#> [[3]]$label
#> [1] "4"
#> 
#> 
```
