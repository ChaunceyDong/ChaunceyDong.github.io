---

layout:     post
title:      Portfolio Optimization using cvxpy
subtitle:   Notes of AI for Trading, Project 2
date:       2020-05-26
author:     Chauncey
header-img: img/home-bg-o.jpg
mathjax: true
catalog: true
tags:
    - Python
    - DataFrame
    - AI for Trading
    - cvxpy
    - optimization
    - quadratic programming
---



# Example 1

Simple optimization problem

Find the optimal weights on a two-asset portfolio given the variance of Stock A, the variance of Stock B, and the correlation between Stocks A and B. 

## Objective

To minimize the portfolio variance, which is defined by our quadratic form $\mathbf{x^T} \mathbf{P} \mathbf{x}$

- variables vector $\mathbf{x} = \begin{bmatrix}
  x_A & x_B
  \end{bmatrix}
  $
- covariance matrix

$$
P = 
\begin{bmatrix}
\sigma^2_A & \sigma_{A,B} \\ 
\sigma_{A,B} & \sigma^2_B 
\end{bmatrix}
$$



- quadratic form of variance $\sigma^2_p = \mathbf{x^T} \mathbf{P} \mathbf{x}$

## Constraint

The sum of weights is 1.
$$
\sum_{1}^{n}x = x_A + x_B = 1
$$

## optimization

So now that we have our objective function and constraints, we can solve for the values of $\mathbf{x}$.
cvxpy has the constructor `Problem(objective, constraints)`, which returns a `Problem` object.

The `Problem` object has a function solve(), which returns the minimum of the solution.  In this case, this is the minimum variance of the portfolio.

It also updates the vector $\mathbf{x}$.

We can check out the values of $x_A$ and $x_B$ that gave the minimum portfolio variance by using `x.value`


```python
import cvxpy as cvx
import numpy as np

def optimize_twoasset_portfolio(varA, varB, rAB):
    """Create a function that takes in the variance of Stock A, the variance of
    Stock B, and the correlation between Stocks A and B as arguments and returns 
    the vector of optimal weights
    
    Parameters
    ----------
    varA : float
        The variance of Stock A.
        
    varB : float
        The variance of Stock B.    
        
    rAB : float
        The correlation between Stocks A and B.
        
    Returns
    -------
    x : np.ndarray
        A 2-element numpy ndarray containing the weights on Stocks A and B,
        [x_A, x_B], that minimize the portfolio variance.
    
    """
    # TODO: Use cvxpy to determine the weights on the assets in a 2-asset
    # portfolio that minimize portfolio variance.
     
    
    cov = np.sqrt(varA)*np.sqrt(varB)*rAB
    x = cvx.Variable(2)
    P = np.array([[varA, cov],[cov, varB]])
    objective = cvx.Minimize(cvx.quad_form(x,P))
    constraints = [sum(x)==1]
    problem = cvx.Problem(objective, constraints)
    min_value = problem.solve()
    xA,xB = x.value
    
    return xA, xB
```

# Example 2

## Objective

- **minimize the portfolio variance**
- **closely track a market cap weighted index**. In other words, minimize the distance between the weights of our portfolio and the weights of the index.

$$
Minimize \left [ \sigma^2_p + \lambda \sqrt{\sum_{1}^{m}(weight_i - indexWeight_i)^2} \right  ]
$$

>  where $m$ is the number of stocks in the portfolio, and $\lambda$ is a scaling factor that you can choose.

### portfolio variance

 quadratic form of the portfolio variance:
$$
\sigma^2_p = \mathbf{x^T} \mathbf{P} \mathbf{x}
$$
in which:

$$
\mathbf{x} = \begin{bmatrix}
x_1 &...& x_M
\end{bmatrix}
$$

$$
\mathbf{P} = 
\begin{bmatrix}
\sigma^2_{1,1} & ... & \sigma^2_{1,m} \\ 
... & ... & ...\\
\sigma^2_{m,1} & ... & \sigma^2_{m,m}  \\
\end{bmatrix}
$$

### L2 norm

Recall from the Pythagorean theorem that you can get the distance between two points in an x,y plane by adding the square of the x and y distances and taking the square root.  Extending this to any number of dimensions is called the L2 norm.  So: $\sqrt{\sum_{1}^{n}(weight_i - indexWeight_i)^2}$  Can also be written as $\left \| \mathbf{x} - \mathbf{index} \right \|_2$.  There's a cvxpy function called [norm()](https://www.cvxpy.org/api_reference/cvxpy.atoms.other_atoms.html#norm)
`norm(x, p=2, axis=None)`.  The default is already set to find an L2 norm, so you would pass in one argument, which is the difference between your portfolio weights and the index weights.

###  $\lambda$ 

We also want to choose a `scale` constant, which is $\lambda$ in the expression. This lets us choose how much priority we give to minimizing the difference from the index, relative to minimizing the variance of the portfolio.  If you choose a higher value for `scale` ($\lambda$), do you think this gives more priority to minimizing the difference, or minimizing the variance?



## Constraint

- weights to sum to one. So $\sum_{1}^{n}x = 1$.  
-  go long only, no negative weights.  So $x_i >0 $ for all $i$.



## Code


```python
import cvxpy as cvx
import numpy as np

def optimize_portfolio(returns, index_weights, scale=.00001):
    """
    Create a function that takes the return series of a set of stocks, the index weights,
    and scaling factor. The function will minimize a combination of the portfolio variance
    and the distance of its weights from the index weights.  
    The optimization will be constrained to be long only, and the weights should sum to one.
    
    Parameters
    ----------
    returns : numpy.ndarray
        2D array containing stock return series in each row.
        
    index_weights : numpy.ndarray
        1D numpy array containing weights of the index.
        
    scale : float
        The scaling factor applied to the distance between portfolio and index weights
        
    Returns
    -------
    x : np.ndarray
        A numpy ndarray containing the weights of the stocks in the optimized portfolio
    """
    # TODO: Use cvxpy to determine the weights on the assets
    # that minimizes the combination of portfolio variance and distance from index weights
    
    # number of stocks m is number of rows of returns, and also number of index weights
    m = returns.shape[0]
    
    #covariance matrix of returns
    cov = np.cov(returns)
    
    # x variables (to be found with optimization)
    x = cvx.Variable(m)
    
    #portfolio variance, in quadratic form
    portfolio_variance = cvx.quad_form(x, cov)
    
    # euclidean distance (L2 norm) between portfolio and index weights
    distance_to_index = cvx.norm(x - index_weights)
    
    #objective function
    objective = cvx.Minimize(portfolio_variance + scale * distance_to_index)
    
    #constraints
    constraints = [x >= 0, sum(x) == 1]

    #use cvxpy to solve the objective
    cvx.Problem(objective, constraints).solve()
    
    #retrieve the weights of the optimized portfolio
    x_values = x.value
    
    return x_values

```















