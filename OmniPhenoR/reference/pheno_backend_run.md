# Run an external backend with explicit execution control

Run an external backend with explicit execution control

## Usage

``` r
pheno_backend_run(
  backend = c("leafmachine2", "command"),
  program = NULL,
  args = character(),
  execute = FALSE,
  ...
)
```

## Arguments

- backend:

  `leafmachine2` or `command`.

- program:

  External executable for `command`.

- args:

  Command arguments.

- execute:

  Execute rather than return a plan.

- ...:

  Backend-specific arguments.

## Value

Command plan or backend result.

## Examples

``` r
pheno_backend_run("command",program="python",args="--version",execute=FALSE)
#> $program
#> [1] "python"
#> 
#> $args
#> [1] "--version"
#> 
#> $execute
#> [1] FALSE
#> 
pheno_backend_run("leafmachine2",path=tempdir(),execute=FALSE)
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
pheno_backend_run("command", program = "Rscript", args = "--version", execute = FALSE)
#> $program
#> [1] "Rscript"
#> 
#> $args
#> [1] "--version"
#> 
#> $execute
#> [1] FALSE
#> 
```
