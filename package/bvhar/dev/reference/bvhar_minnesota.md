# Fitting Bayesian VHAR of Minnesota Prior

This function fits BVHAR with Minnesota prior.

## Usage

``` r
bvhar_minnesota(
  y,
  har = c(5, 22),
  num_chains = 1,
  num_iter = 1000,
  num_burn = floor(num_iter/2),
  thinning = 1,
  bayes_spec = set_bvhar(),
  scale_variance = 0.05,
  include_mean = TRUE,
  verbose = FALSE,
  num_thread = 1
)

# S3 method for class 'bvharmn'
print(x, digits = max(3L, getOption("digits") - 3L), ...)

# S3 method for class 'bvharhm'
print(x, digits = max(3L, getOption("digits") - 3L), ...)

# S3 method for class 'bvharmn'
logLik(object, ...)

# S3 method for class 'bvharmn'
AIC(object, ...)

# S3 method for class 'bvharmn'
BIC(object, ...)

is.bvharmn(x)

# S3 method for class 'bvharmn'
knit_print(x, ...)

# S3 method for class 'bvharhm'
knit_print(x, ...)
```

## Arguments

- y:

  Time series data of which columns indicate the variables

- har:

  Numeric vector for weekly and monthly order. By default, `c(5, 22)`.

- num_chains:

  Number of MCMC chains

- num_iter:

  MCMC iteration number

- num_burn:

  Number of burn-in (warm-up). Half of the iteration is the default
  choice.

- thinning:

  Thinning every thinning-th iteration

- bayes_spec:

  A BVHAR model specification by [`set_bvhar()`](set_bvar.md) (default)
  or [`set_weight_bvhar()`](set_bvar.md).

- scale_variance:

  Proposal distribution scaling constant to adjust an acceptance rate

- include_mean:

  Add constant term (Default: `TRUE`) or not (`FALSE`)

- verbose:

  Print the progress bar in the console. By default, `FALSE`.

- num_thread:

  Number of threads

- x:

  Any object

- digits:

  digit option to print

- ...:

  not used

- object:

  A `bvharmn` object

## Value

`bvhar_minnesota()` returns an object `bvharmn`
[class](https://rdrr.io/r/base/class.html). It is a list with the
following components:

- coefficients:

  Posterior Mean

- fitted.values:

  Fitted values

- residuals:

  Residuals

- mn_mean:

  Posterior mean matrix of Matrix Normal distribution

- mn_prec:

  Posterior precision matrix of Matrix Normal distribution

- iw_scale:

  Posterior scale matrix of posterior inverse-wishart distribution

- iw_shape:

  Posterior shape of inverse-Wishart distribution (\\\nu_0\\ - obs + 2).
  \\\nu_0\\: nrow(Dummy observation) - k

- df:

  Numer of Coefficients: 3m + 1 or 3m

- m:

  Dimension of the time series

- obs:

  Sample size used when training = `totobs` - 22

- prior_mean:

  Prior mean matrix of Matrix Normal distribution: \\M_0\\

- prior_precision:

  Prior precision matrix of Matrix Normal distribution:
  \\\Omega_0^{-1}\\

- prior_scale:

  Prior scale matrix of inverse-Wishart distribution: \\\Psi_0\\

- prior_shape:

  Prior shape of inverse-Wishart distribution: \\\nu_0\\

- y0:

  \\Y_0\\

- design:

  \\X_0\\

- p:

  3, this element exists to run the other functions

- week:

  Order for weekly term

- month:

  Order for monthly term

- totobs:

  Total number of the observation

- type:

  include constant term (`const`) or not (`none`)

- HARtrans:

  VHAR linear transformation matrix: \\C\_{HAR}\\

- y:

  Raw input (`matrix`)

- call:

  Matched call

- process:

  Process string in the `bayes_spec`: `BVHAR_MN_VAR` (BVHAR-S) or
  `BVHAR_MN_VHAR` (BVHAR-L)

- spec:

  Model specification (`bvharspec`)

It is also `normaliw` and `bvharmod` class.

## Details

Apply Minnesota prior to Vector HAR: \\\Phi\\ (VHAR matrices) and
\\\Sigma_e\\ (residual covariance).

\$\$\Phi \mid \Sigma_e \sim MN(M_0, \Omega_0, \Sigma_e)\$\$ \$\$\Sigma_e
\sim IW(\Psi_0, \nu_0)\$\$ (MN: [matrix
normal](https://en.wikipedia.org/wiki/Matrix_normal_distribution), IW:
[inverse-wishart](https://en.wikipedia.org/wiki/Inverse-Wishart_distribution))

There are two types of Minnesota priors for BVHAR:

- VAR-type Minnesota prior specified by [`set_bvhar()`](set_bvar.md),
  so-called BVHAR-S model.

- VHAR-type Minnesota prior specified by
  [`set_weight_bvhar()`](set_bvar.md), so-called BVHAR-L model.

## References

Kim, Y. G., and Baek, C. (2024). *Bayesian vector heterogeneous
autoregressive modeling*. Journal of Statistical Computation and
Simulation, 94(6), 1139-1157.

## See also

- [`set_bvhar()`](set_bvar.md) to specify the hyperparameters of BVHAR-S

- [`set_weight_bvhar()`](set_bvar.md) to specify the hyperparameters of
  BVHAR-L

- [`summary.normaliw()`](summary.normaliw.md) to summarize BVHAR model

## Examples

``` r
# Perform the function using etf_vix dataset
fit <- bvhar_minnesota(y = etf_vix[,1:3])
class(fit)
#> [1] "bvharmn"  "bvharmod" "normaliw"

# Extract coef, fitted values, and residuals
coef(fit)
#>                       GVZCLS       OVXCLS      VXFXICLS
#> GVZCLS_day      2.275811e+00  0.017491325  -0.027884754
#> OVXCLS_day     -7.063461e-03  0.115478748  -0.015893492
#> VXFXICLS_day   -2.716711e-02  0.038984564   2.276343343
#> GVZCLS_week    -1.676656e-02  0.004422105  -0.006683931
#> OVXCLS_week    -1.461915e-03  0.025948953  -0.003822077
#> VXFXICLS_week  -6.413393e-03  0.009549393  -0.016811492
#> GVZCLS_month   -6.096703e-03  0.002261860  -0.002499193
#> OVXCLS_month   -2.649829e-04  0.010990485  -0.001510146
#> VXFXICLS_month -2.472243e-03  0.004009527  -0.005623812
#> const          -2.166031e+01 22.425268793 -29.795736233
head(residuals(fit))
#>           [,1]     [,2]      [,3]
#> [1,] -2.355381 2.913112 -6.692553
#> [2,] -3.575114 3.045523 -6.494979
#> [3,] -2.399386 3.473774 -1.653097
#> [4,] -2.289625 3.766902 -9.866705
#> [5,] -0.654146 4.103733 -7.287023
#> [6,] -1.010046 5.277598 -6.319363
head(fitted(fit))
#>        GVZCLS   OVXCLS VXFXICLS
#> [1,] 23.04538 29.40689 36.15255
#> [2,] 23.60511 29.38448 35.16498
#> [3,] 22.12939 29.35623 33.38310
#> [4,] 21.35963 29.53310 40.33670
#> [5,] 19.89415 29.53627 37.47702
#> [6,] 20.29005 29.57240 36.82936
```
