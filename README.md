# Classifier-Lasso

<!-- badges: start -->
<!-- badges: end -->

This package implements in R the Classifier-Lasso method by

Su, L., Shi, Z., & Phillips, P. C. (2016): ["Identifying latent structures in panel data"](https://onlinelibrary.wiley.com/doi/abs/10.3982/ECTA12560),  *Econometrica*, *84*(6), 2215-2264.



*This package is under active development...*



Code of the classifier-Lasso method was originally developed in `MATLAB` using `CVX` as the modeling language and `MOSEK` as the convex solver. Here is [replicable empirical examples](https://zhentaoshi.github.io/C-Lasso) in the paper. 

To speed up the computation, an R version using `Rmosek` to directly invoke `MOSEK` is elaborated in ["Two Examples of Convex-Programming-Based High-Dimensional Econometric Estimators"](https://arxiv.org/abs/1806.10423) with [demonstration code](https://github.com/zhan-gao/convex_prog_in_econometrics). In our experiments, this `R+Rmosek` implementation often solves the optimization problem with at most 1/3 of the time by the `MATLAB+CVX+MOSEK` implementation.



## Installation

This package is dependent on `Rmosek`. An installation gist can be found at [here](https://gist.github.com/mikelove/67ea44d5be5a053e599257fe357483dc ). The installation of the latest version `MOSEK 9.0` includes `Rmosek` and it can be invoked in `R` following [this instruction](<https://docs.mosek.com/9.0/rmosek/install-interface.html>) (Tested. Successful). *Please make sure `Rmosek` is successfully installed and activated before install this package*. In later versions, we will relax this restriction to allow users use an open source solver via [CVXR](https://github.com/anqif/CVXR) and make `Rmosek` as an option.

The current beta version can be installed from [Github](https://CRAN.R-project.org) by:

``` r
library(devtools)
devtools::install_github("zhan-gao/classo", INSTALL_opts=c("--no-multiarch"))
```

## Examples

The sample data is generated by DGP 1 described in Su, Shi and Phillips (2016) with N = 200 and T = 25.

```r
data("sample_data")
y <- as.matrix(sample_data[, 1])
x <- as.matrix(sample_data[, -1])
n <- 200
tt <- 25
lambda <- as.numeric( 0.5 * var(y) / (tt^(1/3)) )
pls_out <- PLS.mosek(n, tt, y, x, K = 3, lambda = lambda)

# estimated slope for each group. True coefficients: [1,1; 0.4,1.6; 1.6,0.4]
pls_out$a.out 
```

```
          [,1]      [,2]
[1,] 1.0387521 0.9986867
[2,] 0.4017041 1.6014119
[3,] 1.6197497 0.3614408
```

```R
# Estimated group structure
# True group structure:
# 	group 2: 1 - 60
# 	group 1: 61 - 120
# 	group 3: 121 - 200
pls_out$group.est
```

```
  [1] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
 [33] 2 2 2 2 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 1 1 1
 [65] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 3 1 1 1 1 1 1 1 1 1
 [97] 1 1 1 1 2 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 3 3 3 3 3 3 3 3
[129] 3 3 3 3 3 1 3 1 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3
[161] 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3
[193] 3 3 3 3 3 3 3 3
```

