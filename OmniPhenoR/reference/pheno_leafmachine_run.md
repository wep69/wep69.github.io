# Prepare or run LeafMachine2 externally

Prepare or run LeafMachine2 externally

## Usage

``` r
pheno_leafmachine_run(
  path,
  config = NULL,
  python = "python",
  execute = FALSE,
  args = character()
)
```

## Arguments

- path:

  LeafMachine2 repository.

- config:

  Optional YAML configuration path.

- python:

  Python executable.

- execute:

  Execute the command. Default `FALSE` returns a dry-run plan.

- args:

  Additional command-line arguments.

## Value

Command plan or execution status.

## Examples

``` r
pheno_leafmachine_run(tempdir(),execute=FALSE)
#> $program
#> [1] "python"
#> 
#> $args
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/LeafMachine2.py"
#> 
#> $working_directory
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO"
#> 
#> $config
#> NULL
#> 
#> $execute
#> [1] FALSE
#> 
pheno_leafmachine_run(tempdir(),python="python",args="--help",execute=FALSE)
#> $program
#> [1] "python"
#> 
#> $args
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/LeafMachine2.py"
#> [2] "--help"                                                      
#> 
#> $working_directory
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO"
#> 
#> $config
#> NULL
#> 
#> $execute
#> [1] FALSE
#> 
pheno_leafmachine_run(tempdir(), config = file.path(tempdir(), "config.yaml"), execute = FALSE)
#> $program
#> [1] "python"
#> 
#> $args
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/LeafMachine2.py"
#> 
#> $working_directory
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO"
#> 
#> $config
#> [1] "C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/config.yaml"
#> 
#> $execute
#> [1] FALSE
#> 
```
