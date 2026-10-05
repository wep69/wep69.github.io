# Run analysis scripts in separate R processes

Long spatial pipelines are more robust when each step runs in a fresh R
session: memory is released between steps and a crash stops the pipeline
at a known step. Scripts run in order with \`Rscript\`; the pipeline
stops at the first non-zero exit status.

## Usage

``` r
run_isolated(
  scripts,
  env = character(),
  rscript = file.path(R.home("bin"), "Rscript"),
  log_dir = NULL
)
```

## Arguments

- scripts:

  Paths of R scripts.

- env:

  Named character vector of environment variables set for the child
  processes.

- rscript:

  Path of the \`Rscript\` executable.

- log_dir:

  Optional folder receiving one \`.log\` file per script.

## Value

Invisibly, a data frame with script, exit status and minutes.

## Examples

``` r
# \donttest{
s <- file.path(tempdir(), "step1.R"); writeLines("cat('step 1 done')", s)
run_isolated(s)
#> step1.R: exit 0
# }
```
