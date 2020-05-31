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

# CVXPY

**What is `cvxpy`?** [`cvxpy`](http://www.cvxpy.org/index.html) is a Python package for solving convex optimization problems. It allows you to express the problem in a human-readable way, calls a solver, and unpacks the results.

## How to use `cvxpy`

**Import**: First, you need to import the package:`import cvxpy as cvx`

**Steps**: Optimization problems involve finding the values of a *variable* that minimize an *objective function* under a set of *constraints* on the range of possible values the variable can take. So we need to use `cvxpy` to declare the *variable*, *objective function* and *constraints*, and then solve the problem.

**Optimization variable**: Use `cvx.Variable()` to declare an optimization variable. For portfolio optimization, this will be $\mathbf{x}$, the vector of weights on the assets. Use the argument to declare the size of the variable; e.g. `x = cvx.Variable(2)` declares that  $\mathbf{x}$ is a vector of length 2. In general, variables can be scalars, vectors, or matrices.

**Objective function**: Use `cvx.Minimize()` to declare the objective function. For example, if the objective function is $(\mathbf{x} - \mathbf{y})^2$ , you would declare it to be: `objective = cvx.Minimize((x - y)**2)`.

**Constraints**: You must specify the problem constraints with a list of expressions. For example, if the constraints are $\mathbf{x} + \mathbf{y} = 1$ and $\mathbf{x} - \mathbf{y} \geq 1$ you would create the list: `constraints = [x + y == 1, x - y >= 1]`. Equality and inequality constraints are elementwise, whether they involve scalars, vectors, or matrices. For example, together the constraints `0 <= x` and `x <= 1` mean that every entry of $\mathbf{x}$ is between 0 and 1. You cannot construct inequalities with < and >. Strict inequalities don’t make sense in a real world setting. Also, you cannot chain constraints together, e.g., `0 <= x <= 1` or `x == y == 2`.

**Quadratic form**: Use `cvx.quad_form()` to create a quadratic form. For example, if you want to minimize portfolio variance, and you have a covariance matrix $\mathbf{P}$, the quantity `cvx.quad_form(x, P)` represents the quadratic form $\mathbf{x}^\mathrm{T}\mathbf{P}\mathbf{x}$ , the portfolio variance.

**Norm**: Use `cvx.norm()` to create a norm term. For example, to minimize the distance between $\mathbf{x}$  and another vector, $\mathbf{b}$, i.e. $\left \| \mathbf{x} - \mathbf{b} \right \|_2$, create a term in the objective function `cvx.norm(x-b, 2)`. The second argument specifies the type of norm; for an L2-norm, use the argument 2.

**Constants**: Constants are the quantities in objective or constraint expressions that are not Variables. You can use your numeric library of choice to construct matrix and vector constants. For instance, if `x` is a `cvxpy` `Variable` in the expression `A*x + b`, `A` and `b` could be Numpy ndarrays, Numpy matrices, or SciPy sparse matrices. `A` and `b` could even be different types.

**Optimization problem**: The core step in using `cvxpy` to solve an optimization problem is to specify the problem. Remember that an optimization problem involves minimizing an *objective function*, under some *constraints*, so to specify the problem, you need both of these. Use `cvx.Problem()` to declare the optimization problem. For example, `problem = cvx.Problem(objective, constraints)`, where `objective` and `constraints` are quantities you've defined earlier. Problems are immutable. This means that you cannot modify a problem’s objective or constraints after you have created it. If you find yourself wanting to add a constraint to an existing problem, you should instead create a new problem.

**Solve**: Use `problem.solve()` to run the optimization solver.

**Status**: Use `problem.status` to access the status of the problem and check whether it has been determined to be unfeasible or unbounded.

**Results**: Use `problem.value` to access the optimal value of the objective function. Use e.g. `x.value` to access the optimal value of the optimization variable.





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


$$
Minimize \left [  \mathbf{x^T} \mathbf{P} \mathbf{x} + \lambda\left \| \mathbf{x} - \mathbf{index} \right \|_2 \right  ]
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



# Example 3 Multi-Factor Model

## Objective and Constraints
Objective function is to maximizes $ \alpha^T * x \\ $, where $ x $ is the portfolio weights and $ \alpha $ is the alpha vector.

Constraint:
- $ r \leq risk_{\text{cap}}^2 \\ $

  predicted risk be less than some maximum limit

  

- $ B^T * x \preceq factor_{\text{max}} \\ $

- $ B^T * x \succeq factor_{\text{min}} \\ $

  on the maximum and minimum portfolio factor exposures

  

- $ x^T\mathbb{1} = 0 \\ $

  "market neutral constraint": the sum of the weights must be zero

  

- $ \|x\|_1 \leq 1 \\ $

   leverage constraint: the sum of the absolute value of the weights must be less than or equal to 1.0. 

  

- $ x \succeq weights_{\text{min}} \\ $

- $ x \preceq weights_{\text{max}} $

   minimum and maximum limits on individual holdings

  







