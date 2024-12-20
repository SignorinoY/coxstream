
<!-- README.md is generated from README.Rmd. Please edit that file -->

# CoxStream

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![CRAN
status](https://www.r-pkg.org/badges/version/coxstream)](https://CRAN.R-project.org/package=coxstream)
[![R-CMD-check](https://github.com/SignorinoY/coxstream/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/SignorinoY/coxstream/actions/workflows/R-CMD-check.yaml)
[![Codecov test
coverage](https://codecov.io/gh/SignorinoY/coxstream/graph/badge.svg)](https://app.codecov.io/gh/SignorinoY/coxstream)
<!-- badges: end -->

The goal of coxstream is to …

## Installation

You can install the development version of coxstream like so:

``` r
# install.packages("pak")
pak::pak("SignorinoY/coxstream")
```

## Example

This is a basic example which shows you how to solve a common problem:

``` r
library(coxstream)
## basic example code
formula <- survival::Surv(time, status) ~ X1 + X2 + X3 + X4 + X5
fit <- coxstream(
  formula, sim[sim$batch_id == 1, ],
  degree = 1, boundary = c(0, 3), idx_col = "patient_id"
)
for (batch in 2:10) {
  fit <- update(fit, sim[sim$batch_id == batch, ])
}
summary(fit)
#> Call:
#> coxstream(formula = formula, data = sim[sim$batch_id == 1, ], 
#>     degree = 1, boundary = c(0, 3), idx_col = "patient_id")
#> 
#>       coef exp(coef)      se     z      p    
#> X1 0.96391   2.62192 0.04955 19.45 <2e-16 ***
#> X2 0.99021   2.69179 0.05142 19.26 <2e-16 ***
#> X3 0.99061   2.69288 0.05263 18.82 <2e-16 ***
#> X4 0.89065   2.43672 0.05043 17.66 <2e-16 ***
#> X5 0.99236   2.69758 0.04814 20.61 <2e-16 ***
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
#>    exp(coef) exp(-coef) lower .95 upper .95
#> X1 2.6219    0.3814     2.3793    2.8893   
#> X2 2.6918    0.3715     2.4337    2.9772   
#> X3 2.6929    0.3713     2.4289    2.9855   
#> X4 2.4367    0.4104     2.2074    2.6898   
#> X5 2.6976    0.3707     2.4547    2.9645
```

Besides the estimated coefficients, we can also obtain the estimated
baseline hazard function. The following code plots the estimated
cumulative baseline hazard function and the true cumulative baseline
hazard function.

``` r
time <- seq(0, 3, length.out = 100)
basehaz_pred <- basehaz(fit, time)
basehaz_true <- cbind(time, 0.5 * time^2)
plot(
  time, basehaz_pred[, 2],
  type = "l", lty = 2,
  ylim = range(basehaz_pred[, 4], basehaz_pred[, 5]),
  xlab = expression(t), ylab = expression(Lambda[0](t)),
  main = "Cumulative Baseline Hazard (Estimate vs True)"
)
polygon(
  c(time, rev(time)), c(basehaz_pred[, 4], rev(basehaz_pred[, 5])),
  col = rgb(0.5, 0.5, 0.5, 0.4), border = NA
)
lines(basehaz_true[, 1], basehaz_true[, 2])
legend("topleft", legend = c("Estimate", "True"), lty = c(2, 1))
```

<img src="man/figures/README-evaluation-1.png" alt="Cumulative Baseline Hazard (Estimate vs True)" width="100%" />
