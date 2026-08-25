# Import LeafMachine2 tabular outputs

Import LeafMachine2 tabular outputs

## Usage

``` r
pheno_leafmachine_import(path, pattern = "[.]csv$")
```

## Arguments

- path:

  Output directory or CSV file.

- pattern:

  CSV filename pattern.

## Value

Named list of tables, or canonical detection when recognizable box
columns exist.

## Examples

``` r
d<-tempdir()
f<-file.path(d,"lm2_demo.csv")
utils::write.csv(data.frame(class="leaf",confidence=.9,
  xmin=1,ymin=1,xmax=5,ymax=5),f,row.names=FALSE)
pheno_leafmachine_import(f); unlink(f)
#> <pheno_detection>
#>   objects: 1 
#>   engine: leafmachine2 
#>   classes: leaf 
pheno_leafmachine_import(file.path(tempdir(), "directory_without_outputs"))
#> list()
```
