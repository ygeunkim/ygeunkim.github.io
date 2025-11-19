# Fitting Bayesian VAR(p) of Minnesota Prior

This function fits BVAR(p) with Minnesota prior.

## Usage

``` r
bvar_minnesota(
  y,
  p = 1,
  num_chains = 1,
  num_iter = 1000,
  num_burn = floor(num_iter/2),
  thinning = 1,
  bayes_spec = set_bvar(),
  scale_variance = 0.05,
  include_mean = TRUE,
  verbose = FALSE,
  num_thread = 1
)

# S3 method for class 'bvarmn'
print(x, digits = max(3L, getOption("digits") - 3L), ...)

# S3 method for class 'bvarhm'
print(x, digits = max(3L, getOption("digits") - 3L), ...)

# S3 method for class 'bvarmn'
logLik(object, ...)

# S3 method for class 'bvarmn'
AIC(object, ...)

# S3 method for class 'bvarmn'
BIC(object, ...)

is.bvarmn(x)

# S3 method for class 'bvarmn'
knit_print(x, ...)

# S3 method for class 'bvarhm'
knit_print(x, ...)
```

## Arguments

- y:

  Time series data of which columns indicate the variables

- p:

  VAR lag (Default: 1)

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

  A BVAR model specification by [`set_bvar()`](set_bvar.md).

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

  A `bvarmn` object

## Value

`bvar_minnesota()` returns an object `bvarmn`
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

  Posterior scale matrix of posterior inverse-Wishart distribution

- iw_shape:

  Posterior shape of inverse-Wishart distribution (\\alpha_0\\ - obs +
  2). \\\alpha_0\\: nrow(Dummy observation) - k

- df:

  Numer of Coefficients: mp + 1 or mp

- m:

  Dimension of the time series

- obs:

  Sample size used when training = `totobs` - `p`

- prior_mean:

  Prior mean matrix of Matrix Normal distribution: \\A_0\\

- prior_precision:

  Prior precision matrix of Matrix Normal distribution:
  \\\Omega_0^{-1}\\

- prior_scale:

  Prior scale matrix of inverse-Wishart distribution: \\S_0\\

- prior_shape:

  Prior shape of inverse-Wishart distribution: \\\alpha_0\\

- y0:

  \\Y_0\\

- design:

  \\X_0\\

- p:

  Lag of VAR

- totobs:

  Total number of the observation

- type:

  include constant term (`const`) or not (`none`)

- y:

  Raw input (`matrix`)

- call:

  Matched call

- process:

  Process string in the `bayes_spec`: `BVAR_Minnesota`

- spec:

  Model specification (`bvharspec`)

It is also `normaliw` and `bvharmod` class.

## Details

Minnesota prior gives prior to parameters \\A\\ (VAR matrices) and
\\\Sigma_e\\ (residual covariance).

\$\$A \mid \Sigma_e \sim MN(A_0, \Omega_0, \Sigma_e)\$\$ \$\$\Sigma_e
\sim IW(S_0, \alpha_0)\$\$ (MN: [matrix
normal](https://en.wikipedia.org/wiki/Matrix_normal_distribution), IW:
[inverse-wishart](https://en.wikipedia.org/wiki/Inverse-Wishart_distribution))

## References

Bańbura, M., Giannone, D., & Reichlin, L. (2010). *Large Bayesian vector
auto regressions*. Journal of Applied Econometrics, 25(1).

Giannone, D., Lenza, M., & Primiceri, G. E. (2015). *Prior Selection for
Vector Autoregressions*. Review of Economics and Statistics, 97(2).

Litterman, R. B. (1986). *Forecasting with Bayesian Vector
Autoregressions: Five Years of Experience*. Journal of Business &
Economic Statistics, 4(1), 25.

KADIYALA, K.R. and KARLSSON, S. (1997), *NUMERICAL METHODS FOR
ESTIMATION AND INFERENCE IN BAYESIAN VAR-MODELS*. J. Appl. Econ., 12:
99-132.

Karlsson, S. (2013). *Chapter 15 Forecasting with Bayesian Vector
Autoregression*. Handbook of Economic Forecasting, 2, 791-897.

Sims, C. A., & Zha, T. (1998). *Bayesian Methods for Dynamic
Multivariate Models*. International Economic Review, 39(4), 949-968.

## See also

- [`set_bvar()`](set_bvar.md) to specify the hyperparameters of
  Minnesota prior.

- [`summary.normaliw()`](summary.normaliw.md) to summarize BVAR model

## Examples

``` r
# Perform the function using etf_vix dataset
fit <- bvar_minnesota(y = etf_vix[,1:3], p = 2)
class(fit)
#> [1] "bvarmn"   "bvharmod" "normaliw"

# Extract coef, fitted values, and residuals
coef(fit)
#>                   GVZCLS       OVXCLS      VXFXICLS
#> GVZCLS_1     1.913509789  0.004994449 -4.016937e-02
#> OVXCLS_1    -0.005498471  0.112554958 -8.629578e-02
#> VXFXICLS_1  -0.005414071  0.010765289  7.919289e+00
#> GVZCLS_2    -0.003194915  0.001260855 -9.822957e-03
#> OVXCLS_2    -0.001293572  0.025425141 -2.124776e-02
#> VXFXICLS_2  -0.001311051  0.002645952 -2.394721e-02
#> const      -16.270698462 24.216906337 -1.674443e+02
head(residuals(fit))
#>           [,1]     [,2]      [,3]
#> [1,] -2.042202 5.860228 -26.65172
#> [2,] -4.372029 6.946097 -28.61144
#> [3,] -3.350418 5.862288 -22.70333
#> [4,] -2.386267 5.112153 -31.78984
#> [5,] -2.576977 4.406004 -28.84179
#> [6,] -3.427569 3.144420 -26.19604
head(fitted(fit))
#>        GVZCLS   OVXCLS VXFXICLS
#> [1,] 24.38220 29.65977 55.71172
#> [2,] 25.97203 29.64390 57.07144
#> [3,] 24.55042 29.75771 52.24333
#> [4,] 23.78627 29.68285 60.89484
#> [5,] 24.17698 29.56400 57.51179
#> [6,] 24.56757 29.44558 54.15604
```
