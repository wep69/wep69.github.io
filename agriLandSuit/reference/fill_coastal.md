# Fill missing coastal cells by iterated focal means

Climate-model and reanalysis grids often have no values over the sea,
which leaves coastal cells of a finer study grid without change factors
after resampling. Each iteration replaces missing cells by the mean of
their available neighbours.

## Usage

``` r
fill_coastal(r, iterations = 4L, w = 3L)
```

## Arguments

- r:

  A \`SpatRaster\`.

- iterations:

  Number of focal passes.

- w:

  Focal window size (odd integer).

## Value

The filled \`SpatRaster\`.

## Examples

``` r
r <- terra::rast(ncols = 5, nrows = 5, xmin = 0, xmax = 5, ymin = 0, ymax = 5)
terra::values(r) <- 1:25; r[c(1, 2, 6)] <- NA
terra::values(fill_coastal(r, iterations = 2))[c(1, 2, 6)]
#> [1]  7  6 10
```
