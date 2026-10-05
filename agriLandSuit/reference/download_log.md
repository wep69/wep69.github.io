# Read the download log

Read the download log

## Usage

``` r
download_log(dest)
```

## Arguments

- dest:

  Folder used as \`dest\` in the \`get\_\*()\` functions.

## Value

Data frame with one row per logged step (time, dataset, URL, file,
status, bytes, SHA-256, detail), or an empty data frame.

## Examples

``` r
download_log(tempdir())
#> data frame with 0 columns and 0 rows
```
