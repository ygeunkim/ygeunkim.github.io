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
#> GVZCLS_day      1.5366349054 0.0016615036 -1.506726e-02
#> OVXCLS_day     -0.0026524127 0.9359349918 -5.708013e-03
#> VXFXICLS_day   -0.0146310590 0.0024995591  1.537258e+00
#> GVZCLS_week    -0.0092233616 0.0004157423 -3.630363e-03
#> OVXCLS_week    -0.0005597116 0.0013866635 -1.384420e-03
#> VXFXICLS_week  -0.0034344491 0.0007286726 -9.230511e-03
#> GVZCLS_month   -0.0033022777 0.0002368569 -1.336567e-03
#> OVXCLS_month   -0.0001072453 0.0006003371 -5.396754e-04
#> VXFXICLS_month -0.0013234128 0.0003103822 -3.081001e-03
#> const          -8.9686314530 1.6366720182 -1.242785e+01
head(residuals(fit))
#>            [,1]      [,2]      [,3]
#> [1,] -0.8194821 0.1336131 -3.049195
#> [2,] -1.8555011 0.3284684 -3.173116
#> [3,] -1.1557180 0.6285560  1.092094
#> [4,] -1.3084869 0.7160126 -4.866712
#> [5,] -0.1447166 0.6198937 -3.215935
#> [6,] -0.3712924 1.5118750 -2.461061
head(fitted(fit))
#>        GVZCLS   OVXCLS VXFXICLS
#> [1,] 21.50948 32.18639 32.50920
#> [2,] 21.88550 32.10153 31.84312
#> [3,] 20.88572 32.20144 30.63791
#> [4,] 20.37849 32.58399 35.33671
#> [5,] 19.38472 33.02011 33.40593
#> [6,] 19.65129 33.33813 32.97106
```
