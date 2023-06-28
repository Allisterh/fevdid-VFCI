
<!-- README.md is generated from README.Rmd. Please edit that file -->

# fevdid

<!-- badges: start -->

[![R-CMD-check](https://github.com/vfci/fevdid/actions/workflows/R-CMD-check.yaml/badge.svg)](https://github.com/vfci/fevdid/actions/workflows/R-CMD-check.yaml)
[![codecov](https://codecov.io/gh/VFCI/fevdid/branch/main/graph/badge.svg?token=2UCPW70685)](https://codecov.io/gh/VFCI/fevdid)
<!-- badges: end -->

R package to identify structural VAR shocks using maximization of
explained forecast error variances. Implemented to target either the
time domain or frequency domain.

## Installation

You can install the development version of fevdid from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("VFCI/fevdid")
```

## Usage

Example of identifying the main shock along the time domain. Here the
target variable is inflation over four to ten quarters out. The figure
compares the identified main shock to the Choleskey shock for inflation,
which already explains most of the variation in this simple 3-variable
VAR. Clearly, however, the contribution from the Main shock is higher
over the targeted time period.

``` r
library(fevdid)

## US data on inflation (pi), output (x), and federal funds rate (i)
x <- svars::USA
v <- vars::VAR(x, p = 2)
sv <- svars::id.chol(v, order_k = c("x", "pi", "i"))

## Find shock that maximizes forecast error variance for inflation (pi)
## from 4 to 10 quarters out
mv <- id_fevdtd(v, target = "pi", horizon = 4:10)

## Comparing fevds
fevdsv <- vars::fevd(sv, n.ahead = 20)$pi
fevdmv <- vars::fevd(mv, n.ahead = 20)$pi
colnames(fevdmv) <- c("Main", "orth1", "orth2")


## Plotting
require(ggplot2)
cbind(h = 1:20, fevdsv, fevdmv) |>
  tidyr::pivot_longer(cols = !h) |>
  dplyr::filter(name %in% c("Main", "pi")) |>
  ggplot(aes(x = h, y = value, color = name)) +
  geom_line()
```

<img src="man/figures/README-example-1.png" width="100%" />

## References

[Chapter 6: “Forecast error variance
maximization”](https://jrenne.github.io/IdentifStructShocks/forecast-error-variance-maximization.html)
of the book *The Identification of Dynamic Stuctural Shocks*, by
Jean-Paul Renne and Kenza Benhima.

- [jrenne Github](https://github.com/jrenne)

Explains and codes up an example of identifying a shock that maximizes
forecast error variance contribution.

“[What moves real
GNP?](http://fmwww.bc.edu/repec/esNAWM04/up.2923.1054309431.pdf)”.
Harlad Uhlig. (2003).
