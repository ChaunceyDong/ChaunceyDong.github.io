---
layout:     post
title:      Trading with Momentum
subtitle:   Notes of AI for Trading, Project 1
date:       2020-05-10
author:     Chauncey
header-img: img/post-bg-isos9-web.jpg
catalog: true
tags:
    - Python
    - DataFrame
    - AI for Trading
    - Pandas
    - T-test
    - Factor
    - Alpha
---



# Trading with Momentum

As the simplest strategy and alpha factor

## Generate Signal

A trading signal is a sequence of trading actions, or results that can be used to take trading actions. A common form is to produce a "long" and "short" portfolio of stocks on each date (e.g. end of each month, or whatever frequency you desire to trade at). This signal can be interpreted as rebalancing your portfolio on each of those dates, entering long ("buy") and short ("sell") positions as indicated.

Here's a strategy that we will try:

> For each month-end observation period, rank the stocks by *previous* returns, from the highest to the lowest. Select the top performing stocks for the long portfolio, and the bottom performing stocks for the short portfolio.



```python
def get_top_n(prev_returns, top_n):
    """
    Select the top performing stocks
    
    Parameters
    ----------
    prev_returns : DataFrame
        Previous shifted returns for each ticker and date
    top_n : int
        The number of top performing stocks to get
    
    Returns
    -------
    top_stocks : DataFrame
        Top stocks for each ticker and date marked with a 1
    """    
    copy = pd.DataFrame(columns=prev_returns.columns)
    for index, row in prev_returns.iterrows():
        top = row.nlargest(top_n).index
        copy.loc[index] = 0
        copy.loc[index, top] = 1
    return copy

#     Version 2
#     top_stocks = prev_returns.apply(lambda r: r.nlargest(top_n), axis=1)
#     top_stocks = top_stocks.applymap(lambda x: 0 if pd.isnull(x) else 1)
#     return top_stocks

```



## Calculate Portfolio Return

-  to check if your trading signal has the potential to become profitable

For simplicity, we'll assume every stock gets an equal dollar amount of investment. This makes it easier to compute a portfolio's returns as the simple arithmetic average of the individual stock returns.

```python
def portfolio_returns(df_long, df_short, lookahead_returns, n_stocks):
    """
    Compute expected returns for the portfolio, assuming equal investment in each long/short stock.
    
    Parameters
    ----------
    df_long : DataFrame
        Top stocks for each ticker and date marked with a 1
    df_short : DataFrame
        Bottom stocks for each ticker and date marked with a 1
    lookahead_returns : DataFrame
        Lookahead returns for each ticker and date
    n_stocks: int
        The number number of stocks chosen for each month
    
    Returns
    -------
    portfolio_returns : DataFrame
        Expected portfolio returns for each ticker and date
    """
    return (df_long*lookahead_returns - df_short*lookahead_returns)/n_stocks


```

## Statistical Tests

Our null hypothesis ($H_0$) is that the actual mean return from the signal is zero. We'll perform a one-sample, one-sided t-test on the observed mean return, to see if we can reject $H_0$.

We'll need to first compute the t-statistic, and then find its corresponding p-value. The p-value will indicate the probability of observing a t-statistic equally or more extreme than the one we observed if the null hypothesis were true. A small p-value means that the chance of observing the t-statistic we observed under the null hypothesis is small, and thus casts doubt on the null hypothesis. It's good practice to set a desired level of significance or alpha ($\alpha$) _before_ computing the p-value, and then reject the null hypothesis if $p < \alpha$.

For this project, we'll use $\alpha = 0.05$, since it's a common value to use.

Implement the `analyze_alpha` function to perform a t-test on the sample of portfolio returns. We've imported the `scipy.stats` module for you to perform the t-test.

```python
from scipy import stats

def analyze_alpha(expected_portfolio_returns_by_date):
    """
    Perform a t-test with the null hypothesis being that the expected mean return is zero.
    
    Parameters
    ----------
    expected_portfolio_returns_by_date : Pandas Series
        Expected portfolio returns for each date
    
    Returns
    -------
    t_value
        T-statistic from t-test
    p_value
        Corresponding p-value
    """
    
    t, p = stats.ttest_1samp(expected_portfolio_returns_by_date, 0)
    return t, p/2
```



## Result 

*After performing the t-test, we observe that the t-value is 1.467 and p_value is 0.073359. Looking at the p_value (which is above the alpha threshold of 0.05), it indicates that we cannot reject the NULL hypothesis. That means the actual mean return from the signal might be zero, so our portfolio seems not profitable.*