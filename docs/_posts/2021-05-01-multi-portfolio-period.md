---
layout: post
title:  "Portfolio Optimization Part 4: Multi Portfolio and Multi Period"
date:   2021-05-01 23:51:00 -0400
categories: portfolio optimization
---

Multiple conic problems can be put together and solved together. Ignoring numerical differences, the result of solving two conic problems through the same optimization is the same as solving them individually. Linear relations can be then added to those stacked conic problems to achieve multi period and/or multi portfolio optimization.

## Multi Period Optimization

The initial position for stock $$i$$ at time period $$t$$ is

$$\begin{align}
w_{i,t} = \left(1+\alpha_{i,t-1}\right)\left(w_{i,t-1}+\Delta w_{i,t-1}\right)
\end{align}$$

where $$\alpha_{i,t}$$ is the predicted return for stock $$i$$ at time period $$t-1$$. The dollar amount traded $$\Delta w_{i,t-1}$$ is assumed to be done at price of $$t-1$$. Another way to look at this assumption is that part of $$\alpha_{i,t-1}$$ is considered in transaction cost.

Now the transaction cost will have an alpha term

$$\begin{align}
\delta \alpha_{i,t-1} \Delta w_{i,t-1}
\end{align}$$

where $$\delta$$ is the coefficient that how much $$alpha$$ is paid in the transaction. For example, $$\delta=0.5$$ if TWAP is used and alpha is assumed to be linear.

## Multi Period T Cost Model

Trading in previous periods will have impact on later periods, so the cost will be pushed higher. Assuming impact is linear

$$\begin{align}
\lambda_{0,0} \Delta w_0 \Delta w_0 + \left(\lambda_{0,1} \Delta w_0 + \lambda_{1,1} \Delta w_1\right)\Delta w_1 + \left(\lambda_{0,2} \Delta w_0 + \lambda_{1,2}  \Delta w_1 + \lambda_{2,2}\Delta w_2\right)\Delta w_2 + \cdots + \sum_{i=0}^n \lambda_{i,n}\Delta w_i \Delta w_n
\end{align}$$

where $$\lambda_{i,j}$$ is the cost from trading period $$i$$ on trading period $$j$$. To further simplify, assuming

$$\begin{align}
\lambda_{i,j}=k_{i,j}\lambda_{i,i}
\end{align}$$

where $$k_{i,j}$$ is the decay. We can assume $$k_{i,j}$$ is exponential

$$\begin{align}
k_{i,j}=a\exp\left(-k\left|i-j\right|\right)
\end{align}$$

Writing it in matrix form

$$\begin{align}
\Delta \mathbf{w}^T \mathbf{\Lambda} \Delta \mathbf{w}
\end{align}$$

And we can use the symmetric matrix $$\Lambda^*=\frac{1}{2}\mathbf{\Lambda}^T +\frac{1}{2} \mathbf{\Lambda}$$.

The coefficients can be fitted by deriving from a single period model and make some assumption about distributions of $$\lambda_{i,i}$$ and values of $$k_{i,j}=k+{\left\|i-j\right\|}=a\exp\left(-k\left\|i-j\right\|\right)$$ or $$k_{i,j}$$ is only determined by $$\left\|i-j\right\|$$.

Several notes

1. Period length should be the same - for example, dividing a 390-minute trading day into 78 5-minute bins.
2. The decay is related to the period length.
3. The diagonal terms can be adjusted to account for different time of the day (for example, trading cost may be higher during middle of the day when volume is lower).

For the conic optimization to work, $$\Lambda^*$$ is required to be positive semi-definite. Violating this will preset an arbitrage opportunity, when the cost of buying in the previous periods is lower than that of selling all the position that is bought - free money basically. One good way to prevent this from happening is to make sure the matrix is **diagonal dominant**.

## Multi Portfolio

Similar to multi period optimization, linear relations between different portfolios can be added to form multi period optimization. The simplest one is a series of portfolios summing up to the total portfolio, although arbitrary linear relations can be added.

Be careful with following

1. Universe and position constraints. For example, if total portfolio has a smaller universe than all sub portfolios, the names that are not in the total portfolio needs to sum up to exact 0. Different position constraints on the portfolios may contradict each other.
2. Transaction cost. Given transaction cost is not linear, summing up individual portfolio's transaction cost doesn't equal to the transaction cost if the trading is aggregated. This can be solved by only optimizing the aggregated portfolio's transaction cost.
3. Make sure all portfolios have proper constraints and objective terms to have unique solution.