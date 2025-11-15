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
#> GVZCLS_day      3.798218e+00 5.710693e-03  -0.041480205
#> OVXCLS_day     -1.447837e-02 5.794661e-01  -0.033550961
#> VXFXICLS_day   -3.989778e-02 1.228035e-02   3.859875767
#> GVZCLS_week    -2.387188e-02 1.439438e-03  -0.009924035
#> OVXCLS_week    -2.991781e-03 1.142588e-02  -0.008043484
#> VXFXICLS_week  -9.444259e-03 3.040440e-03  -0.024688563
#> GVZCLS_month   -8.740220e-03 7.351569e-04  -0.003737717
#> OVXCLS_month   -5.576118e-04 4.846443e-03  -0.003197449
#> VXFXICLS_month -3.636920e-03 1.272456e-03  -0.008277746
#> const          -4.849950e+01 1.097013e+01 -67.777809784
head(residuals(fit))
#>           [,1]     [,2]       [,3]
#> [1,] -5.643868 1.393289 -14.606643
#> [2,] -7.235559 1.559351 -13.724984
#> [3,] -5.066533 1.908444  -7.638915
#> [4,] -4.457047 2.104330 -20.687706
#> [5,] -1.831300 2.187068 -16.111489
#> [6,] -2.448442 3.200336 -14.691583
head(fitted(fit))
#>        GVZCLS   OVXCLS VXFXICLS
#> [1,] 26.33387 30.92671 44.06664
#> [2,] 27.26556 30.87065 42.39498
#> [3,] 24.79653 30.92156 39.36891
#> [4,] 23.52705 31.19567 51.15771
#> [5,] 21.07130 31.45293 46.30149
#> [6,] 21.72844 31.64966 45.20158
```
