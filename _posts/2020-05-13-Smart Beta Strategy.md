---

layout:     post
title:      Smart Beta Strategy
subtitle:   Notes of AI for Trading, Project 3
date:       2020-05-13
author:     Chauncey
header-img: img/post-bg-isos9-web.jpg
catalog: true
tags:
    - Python
    - AI for Trading
    - Pandas
    - Smart Beta
    - Factor
    - Alpha
---

# Overview

**Smart beta** has a broad meaning, but we can say in practice that when we use the universe of stocks from an index, and then apply some weighting scheme **other than market cap weighting**, it can be considered a type of **smart beta fund**.

- Building a smart beta portfolio and calculating its tracking error against a benchmark stock index in order to see how well it performs.
- Using quadratic programming to optimize the portfolio's weights.
- Rebalancing this portfolio and then calculating turnover in order to evaluate performance and determine optimal rebalancing frequency.
- The dataset is a set of end-of-day stock prices that comes from Quotemedia.



## Concepts

- Using Pandas/NumPy to calculate portfolio weights based on dollar volume, as well as weights based on dividend returns.
- Writing methods that compute returns, weighted returns, cumulative returns, and tracking error.
- Solving convex optimization problems (quadratic programming) with the [CVXPY](http://www.cvxpy.org/) Python library.
- Implementing methods that rebalance portfolio weights at any desired frequency and return the cost, or annualized turnover, of doing so.



## Tracking Error
In order to check the performance of the smart beta portfolio, we can calculate the annualized tracking error against the index. Implement `tracking_error` to return the tracking error between the ETF and benchmark.

For reference, we'll be using the following annualized tracking error function:
$$
TE = \sqrt{252} * SampleStdev(r_p - r_b)
$$
Where $ r_p $ is the portfolio/ETF returns and $ r_b $ is the benchmark returns.

_Note: When calculating the sample standard deviation, the delta degrees of freedom is 1, which is the also the default value._



## Portfolio Turnover
With the portfolio rebalanced, we need to use a metric to measure the cost of rebalancing the portfolio. Implement `get_portfolio_turnover` to calculate the annual portfolio turnover. We'll be using the formulas used in the classroom:

$$
AnnualizedTurnover =\frac{SumTotalTurnover}{NumberOfRebalanceEvents} * NumberofRebalanceEventsPerYear 
$$

$$
SumTotalTurnover =\sum_{t,n}{\left | x_{t,n} - x_{t+1,n} \right |} 
$$
Where $ x_{t,n} $ are the weights at time $ t $ for equity $ n $.

$ SumTotalTurnover $ is just a different way of writing $ \sum \left | x_{t_1,n} - x_{t_2,n} \right | $





