# Check or explicitly install CmdStan

This function never installs CmdStan unless \`install = TRUE\` is
explicitly supplied. Installation is a local environment action, not a
package-loading side effect.

## Usage

``` r
bayes_setup_cmdstan(install = FALSE, cores = 2, ...)
```

## Arguments

- install:

  Logical. If \`TRUE\`, call \`cmdstanr::install_cmdstan()\`.

- cores:

  Positive number of compilation cores.

- ...:

  Additional explicit arguments passed to
  \`cmdstanr::install_cmdstan()\`.

## Value

CmdStan version information invisibly, or the install result.

## Examples

``` r
# Example 1: inspect status without installation
if (requireNamespace("cmdstanr", quietly = TRUE)) {
  bayes_setup_cmdstan()
}

# Example 2: request installation explicitly
if (FALSE) bayes_setup_cmdstan(install = TRUE, cores = 4)

# Example 3: pass an explicit version or installation option
if (FALSE) bayes_setup_cmdstan(install = TRUE, cores = 8, overwrite = FALSE)
```
