# Optimization

## strategy.optimize

```python
Strategy.optimize(
  self, 
  data: Optional[pandas.core.frame.DataFrame] = None, 
  returns_data: Optional[bool] = False, 
  backend: Optional[str] = 'pypfopt'
) ‑> opendesk.strategy.Strategy
```

Portfolio optimization capabilities, which is the process of selecting the optimal mix of assets in a portfolio, with respect to the alpha scores, in order to maximize returns while minimizing risk. The `Strategy.optimizer()` method has been implemented to streamline the process of optimization and facilitate the integration of backtesting.

The `Strategy.optimize` (backend) calls the `Optimizer` class, which includes the implementation of the following public methods:

* `add()` Add a new objectives and constraints to the optimization problem
* `portfolio()` Base optimizer model

### Parameters

``` markdown title="data"
Optional[pandas.core.frame.DataFrame]
```
<div class="result" markdown>
Market price time-series, each row is a date and each column is a ticker/id.
</div>

``` markdown title="returns_data"
Optional[bool] = True
```
<div class="result" markdown>
If true, the first argument is returns instead of prices. Defaults to `True`
</div>

``` markdown title="backend"
Optional[str] = "pypfopt"
```
<div class="result" markdown>
Backend optimizer library. Defaults to `pyportopt`.

* `pyportopt`: PyPortfolioOpt is a library that implements portfolio optimization methods, including classical efficient frontier techniques and Black-Litterman allocation, as well as more recent developments in the field like shrinkage and Hierarchical Risk Parity, along with some novel experimental features, like exponentially-weighted covariance matrices. 
* `riskfolio`: Riskfolio-Lib is a library for making portfolio optimization and quantitative strategic asset allocation in Python. Its objective is to help students, academics and practitioners to build investment portfolios based on mathematically complex models with low effort. It is built on top of CVXPY and closely integrated with pandas data structures.
</div>

### Returns

`opendesk.strategy.Strategy` instance.

## Optimizer

```python
class optimizer.Optimizer(
    self,
    data: pandas.core.frame.DataFrame,
    group_constraints: Optional[Dict[str, Tuple[float, float]]],
    topdown: Optional[bool] = False,
    mapping_table: Optional[Dict[str, str]] = None,
    returns_data: Optional[bool] = False,
)
```

Initalize portfolio optimization processes.

!!! warning "New Object Instantiation"
    A new object should be instantiated if you want to make any change to objectives/constraints/bounds/parameters.

### Parameters

``` markdown title="data"
pandas.core.frame.DataFrame
```
<div class="result" markdown>
Adjusted closing prices of the asset, each row is a date and each column is a ticker/id. If returns data, variable `returns_data` should be turned to `True`.
</div>

``` markdown title="group_constraints"
Optional[Dict[str, Tuple(float, float)]]
```
<div class="result" markdown>
Strategy constraints by group. Product of `exposures` and `mapping_weights`.
</div>

``` markdown title="mapping_table"
Optional[Dict[str, str]]
```
<div class="result" markdown>
Maps higher with lower level assets. Variable `topdown` needs to be turned to `True`. Defaults to `None`.
</div>

``` markdown title="returns_data"
Optional[bool]
```
<div class="result" markdown>
If true, the first argument is returns instead of prices. Defaults to False.
</div>

### Public Methods

#### Optimizer.add

Add a new objectives and constraints to the optimization problem.

##### Parameters

``` markdown title="objectives"
Optional[List[Type]] = None
```
<div class="result" markdown>
List of lambda functions to add new term into the based objective function. This term must be convex, and built from cvxpy atomic functions.
</div>

``` markdown title="constraints"
Optional[List[Type]] = None
```
<div class="result" markdown>
 List of lambda function (e.i. all assets <= 3% of the total portfolio = [lambda w: w <= .03]. This constraint must satisfy DCP rules, i.e be either a linear equality constraint or convex inequality constraint.
</div>


!!! notes "Top-Down Method"
    When `topdown` is set to `True`, it adds constraints on the sum of weights of different groups of assets. Most commonly, these will be sector constraints e.g portfolio’s exposure to tech must be less than x%.

##### Returns

`opendesk.optimizer.Optimizer` instance.

#### Optimizer.portfolio

Base optimizer model, allowing for the efficient computation of optimized asset weights. The portfolio method houses different optimization methods, which generate optimal portfolios for various possible objective functions and parameters.

##### Parameters

``` markdown title="model"
Optional[str] = "mvo"
```
<div class="result" markdown>
Type of optimizer to be used.
Type of optimization:

* `mvo`: Mean-variance optimization
  
Work in progress:

* `bl`: Black-Litterman allocation
* `hrp`: Hierarchical Risk Parity
* `cla`: Critical Line Algorithm
</div>

``` markdown title="expected_returns_params"
Dict[str, Any]
```
<div class="result" markdown>
Parameters to compute an estimate of future returns:

* `method` (str): the return model to use. Should be one of:
    * `mean_historical_return`
    * `ema_historical_return`
    * `capm_return`
* `**kwargs`: Method specificities
</div>

``` markdown title="cov_matrix_params"
Dict[str, Any]
```
<div class="result" markdown>
Parameters to compute a covariance matrix:

* `method` (str): the risk model to use. Should be one of:
    * `sample_cov`
    * `semicovariance`
    * `exp_cov`
    * `ledoit_wolf`
    * `ledoit_wolf_constant_variance`
    * `ledoit_wolf_single_factor`
    * `ledoit_wolf_constant_correlation`
    * `oracle_approximating`
* `**kwargs`: Method specificities
</div>

``` markdown title="weight_bounds"
Optional[Tuple[int, int]] = (-1, 1)
```
<div class="result" markdown>
Minimum and maximum weight of each asset or single min/max pair if all identical, defaults to (-1, 1). If `weight_bounds=(-1, 1)`, allows short positions.
</div>

``` markdown title="kwargs"
Dict
```
<div class="result" markdown>
Model specificities.
</div>

##### Returns

`opendesk.optimizer.Optimizer` instance.
 
## Example Optimize

Portfolio construction, which involves optimizing the allocation of assets within a portfolio, can be a complex and nuanced process. We have developed a method that allows for greater flexibility and experimentation in the portfolio optimization process. This approach enables the exploration of a wide range of potential portfolio compositions, and the example provided illustrates this method applied from the initial stages of portfolio construction:

* A mapping table, `mapping_table`, has been defined to specify the group membership of each investable stocks
* The base model is set `mvo`, the Mean-Variance Optimization from the [pypfopt library](https://pyportfolioopt.readthedocs.io/en/latest/MeanVariance.html), with the appropriate return and risk models
* The weight bounds parameter, `weight_bounds` is set to `(-1, 1)`, which serves as the first constraint by limiting the minimum and maximum weight of each asset in portfolios that allow short positions
* Additionally, new constraints are introduced to the optimization problem in the form of convex inequalities, which ensure that long positions do not exceed 10% and short positions do not fall below -10%

```py
from opendesk import Strategy
from opendesk.alpha_blocks import Reversion
    
steps = [("reversion", Reversion)] # (1)

strategy = (
    Strategy(
      steps,
      topdown=True,
      mapping_table=mapping_table # (4)
    )
    .fit(df) # (2)
    .estimate(sum)
    .optimize(
      data=stock_prices, # (3)
      backend="pypfopt"        
    )
    .portfolio(
      model="mvo",
      expected_returns_params={
          "method": "capm_return",
      },
      cov_matrix_params={
          "method": "sample_cov",
      },   
      weight_bounds=(-1, 1) # (5)
    )
    .add(
        objectives=None,
        constraints=[
            lambda w: w <=  .1, 
            lambda w: w >= -.1
        ] # (6)
    )
)
```

1.  Calculate sentiment using Reversion Ranking Method.
    More information provided in the [Model Glossary](/pro_version/model_glossary/sentiment/reversion_models).
2.  pandas.DataFrame object, with specifiy the variation of sector returns over time.
3.  pandas.DataFrame object, with specifiy the variation of stock prices over time.
4.  Mapping table where stock ticker/id are keys and sector name are values.
5. `weight_bounds` parameter serves as a constraint by limiting the minimum and maximum weight of each asset in portfolios. Because it ranges from `-1` to `1`, it allows Long and Shorts.
6. Users can add new constraints in a form of lambda function as the user need to the optimization problem. This constraint must satisfy DCP rules, i.e be either a linear equality constraint or convex inequality constraint.

The wrapper class creates `portfolio`, a public method, allowing for the efficient computation of optimized asset weights through inheritance. Here is an example with `efficient_risk()`, which maximises return for a given target risk of 8% (`target_volatility=.08`) and market neutrality (`market_neutral=True`):

<div class="termy">
  ```console
  $ strategy.efficient_risk(target_volatility=.08, market_neutral=True)
  $ weights = pd.Series(strategy.clean_weights(), name="weights")
  <span style="color: grey;">asset 1      0.10
  asset 2      0.03
  asset 3     -0.02
  asset 4      0.03
  asset 5     -0.05

  asset 96     0.00
  asset 97    -0.09
  asset 98     0.00
  asset 99    -0.07
  asset 100    0.00
  Name: weights, Length: 100, dtype: float64
  </span>
  ```