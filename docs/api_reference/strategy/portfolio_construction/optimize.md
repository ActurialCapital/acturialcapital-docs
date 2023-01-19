# Optimization

## strategy.optimize

```python
Strategy.optimize(
  self, 
  data: Optional[pandas.core.frame.DataFrame] = None, 
  backend: Optional[str] = 'pypfopt'
) ‑> opendesk.strategy.Strategy
```

Portfolio optimization capabilities, which is the process of selecting the optimal mix of assets in a portfolio, with respect to the alpha scores, in order to maximize returns while minimizing risk. The `optimize()` method has been implemented to streamline the process of optimization and facilitate the integration of backtesting.

The `optimize()` (backend) calls the `Optimizer` class, which includes the implementation of the following public methods:

* `add()` Add a new objectives and constraints to the optimization problem
* `portfolio()` Base optimizer model

### Parameters

``` markdown title="data"
Optional[pandas.core.frame.DataFrame] = None
```
<div class="result" markdown>
Market price time-series, each row is a date and each column is a ticker/id. If `None`, it takes `model_data`, the dataset used in the `fit()` method. Defaults to `None`.
</div>

``` markdown title="backend"
Optional[str] = "pypfopt"
```
<div class="result" markdown>
Backend optimizer library. Defaults to `pyportopt`.

* `pyportopt`: PyPortfolioOpt is a library that implements portfolio optimization methods, including classical efficient frontier techniques and Black-Litterman allocation, as well as more recent developments in the field like shrinkage and Hierarchical Risk Parity, along with some novel experimental features, like exponentially-weighted covariance matrices. 
* `riskfolio`: Riskfolio-Lib is a library for making portfolio optimization and quantitative strategic asset allocation in Python. Its objective is to help students, academics and practitioners to build investment portfolios based on mathematically complex models with low effort. It is built on top of CVXPY and closely integrated with pandas data structures.

!!! warning "Riskfolio-Lib"
    The Riskfolio-Lib integration is still work-in-progress.

</div>

### Returns

`opendesk.strategy.Strategy` instance.

!!! example "Example Optimizer"

        Portfolio construction, which involves optimizing the allocation of assets within a portfolio, can be a complex and nuanced process. We have developed a method that allows for greater flexibility and experimentation in the portfolio optimization process. This approach enables the exploration of a wide range of potential portfolio compositions, and the example provided illustrates this method applied from the initial stages of portfolio construction:

        * A mapping table, `mapping_table`, has been defined to specify the group membership of each investable stocks
        * The base model is set `mvo`, the Mean-Variance Optimization from the [pypfopt library](https://pyportfolioopt.readthedocs.io/en/latest/MeanVariance.html), with the appropriate return and risk models
        * The weight bounds parameter, `weight_bounds` is set to `(-1, 1)`, which serves as the first constraint by limiting the minimum and maximum weight of each asset in portfolios that allow short positions
        * Additionally, new constraints are introduced to the optimization problem in the form of convex inequalities, which ensure that long positions do not exceed 10% and short positions do not fall below -10%

        ```py
        from opendesk import Strategy

        strategy = (
            Strategy(
            steps=steps, 
            topdown=True,
            mapping_table=mapping_table # (3)
            )
            .fit(df) # (1)
            .estimate(sum)
            .optimize(
                data=stock_prices, # (2)
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
                weight_bounds=(-1, 1) # (4)
            )
            .add(
                custom_constraints=[
                    lambda w: w <=  .1, 
                    lambda w: w >= -.1
                ] # (5)
            )
        )
        ```

        1.  pandas.DataFrame object, with specifiy the variation of sector returns over time.
        2.  pandas.DataFrame object, with specifiy the variation of stock prices over time.
        3.  Mapping table where stock ticker/id are keys and sector name are values.
        4. `weight_bounds` parameter serves as a constraint by limiting the minimum and maximum weight of each asset in portfolios. Because it ranges from `-1` to `1`, it allows Long and Shorts.
        5. Users can add new constraints in a form of lambda function as the user need to the optimization problem. This constraint must satisfy DCP rules, i.e be either a linear equality constraint or convex inequality constraint.

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

## Optimizer

```python
class optimizer.Optimizer(
    self,
    data: pandas.core.frame.DataFrame,
    group_constraints: Optional[Dict[str, Tuple[float, float]]] = None,
    exposures: Optional[pd.Series] = None,
    topdown: Optional[bool] = False,
    mapping_table: Optional[Dict[str, str]] = None,
    freq: Optional[int] = 252
)
```

Initalize the portfolio optimization process. 

### Parameters

``` markdown title="data"
pandas.core.frame.DataFrame
```
<div class="result" markdown>
Adjusted closing prices of the asset, each row is a date and each column is a ticker/id.
</div>

``` markdown title="group_constraints"
Optional[Dict[str, Tuple[float, float]]] = None
```
<div class="result" markdown>
Strategy constraints by group. Product of `mapping_weights` inputs and `exposures` outputs from the `Strategy` class.
</div>

``` markdown title="exposures"
Optional[pd.Series] = None
```
<div class="result" markdown>
Strategy exposures attribute estimated from the `estimate()` function. Used in `asset_views` properties. Used to limiting the overall average score (exposure) to a level, as a custom constraint. E.i. suppose that for each asset you have some “score” – it could be an ESG metric, or some custom risk/return metric. It is simple to specify linear constraints, like “portfolio ESG score must be greater than x”: you simply create a vector of scores, add a constraint on the dot product of those scores with the portfolio weights, then optimize your objective:

!!! example ESG Scores
    ```python hl_lines="2 11"
    # portolfio mininum score to find
    portfolio_min_score = 0.5

    # start strategy
    esg_strategy = Strategy(steps=[("ESG", ESGModel)], topdown=True, mapping_table=mapping_table)
    esg_strategy.fit(df).estimate(sum) # (1)
    esg_strategy.optimize(stock_prices)
    esg_strategy.portfolio(**portfolio_params) # (2)
    esg_strategy.add(
        custom_constraints=[
            lambda w: strategy.asset_scores @ w >= portfolio_min_score
        ]
    )
    ```

    1.  Here, ESG scores are produced by the strategy `estimate()` function.
    2.  Portfolio parameters are not explained here, as the goal of this snippet is to showcase the `custom_constraints` parameter with the optimizer `asset_scores` proprety.

</div>

``` markdown title="topdown"
Optional[bool] = False
```
<div class="result" markdown>
Activates top-down strategy. The strategy tilts is processed at a higher level (e.i. sector level) than the actual allocation exectution (e.i. stock level). If set to True, a mapping table should be passed. Defaults to False.
</div>

``` markdown title="mapping_table"
Optional[Dict[str, str]] = None
```
<div class="result" markdown>
Maps higher with lower level assets. Variable `topdown` needs to be turned to `True`. Defaults to `None`.
</div>

``` markdown title="freq"
Optional[int] = 252
```
<div class="result" markdown>
Number of time periods in a year, Defaults to 252 (the number of trading days in a year).
</div>

### Attributes

#### asset_scores

``` markdown title="asset_scores"
Dict[str, float]
```
<div class="result" markdown>
Transform exposures to score at any level. If `topdown` is set to `True`, it transforms exposures at the lower level. Otherwise, It returns `exposures`.
</div>

#### asset_views

``` markdown title="asset_views"
Dict[str, float]
```
<div class="result" markdown>

The alpha blocks implementation works with the Black-Litterman `asset_views`, where views direction is extracted from the median of weight range and the magnitude is $1.96 \sigma$, where $\sigma$ is the annualized volatility calculated from log returns.

!!! note "1.96 is Hardcoded"
    In probability and statistics, the 97.5th percentile point of the standard normal distribution is a number commonly used for statistical calculations. The approximate value of this number is 1.96, meaning that 95% of the area under a normal curve lies within approximately 1.96 standard deviations of the mean.
</div>

#### bounds

``` markdown title="bounds"
List[Dict[str, float]]
```
<div class="result" markdown>
Split range of weights, where the first element is the lower weight and the second element is the higher weight.
</div>

#### risk_free_rate

``` markdown title="risk_free_rate"
float
```
<div class="result" markdown>
Risk free rate. Defaults to 0.02.
</div>

### Public Methods

#### Optimizer.add

```python
Optimizer.add(
    self,
    alpha_block_constraints: Optional[bool] = True,
    n_asset_constraints: Optional[int] = None,
    l2_regularization: Optional[bool] = True,
    gamma: Optional[int] = 2,
    custom_objectives: Optional[List[Tuple[Type, Dict[str, Any]]]] = None,
    custom_constraints: Optional[List[Type]] = None
) -> "Optimizer"
```

Add a new objectives and constraints to the optimization problem.

##### Parameters

``` markdown title="alpha_block_constraints"
Optional[bool] = True
```
<div class="result" markdown>
Alpha blocks core constraints. It adds constraints on the sum of weights of different groups of assets. Most commonly, these will be sector constraints. These constraints a particularly relevant when working with alpha blocks (top-down or bottom-up), as we aim to limit our exposure to paricular group of assets. Defaults to `True`.
</div>

``` markdown title="n_asset_constraints"
Optional[List[Type]] = None
```
<div class="result" markdown>
Number of assets in the portfolio constraints. Cardinality constraints are not convex, making them difficult to implement. However, we can treat it as a mixed-integer program and solve (provided you have access to a solver). for small problems with less than 1000 variables and constraints, you can use the community version of [CPLEX](https://en.wikipedia.org/wiki/CPLEX) available in python `pip install cplex`.

!!! warning "`n_asset_constraints`"
    This functionnality is still work in progress, as it requires external capabilities (`cplex`).
</div>

``` markdown title="l2_regularization"
Optional[bool] = True
```
<div class="result" markdown>
L2 regularisation, i.e $\gamma ||w||^2$, to increase the number of nonzero weights.

Mean-variance optimization often results in many weights being negligible, i.e the efficient portfolio does not end up including most of the assets. This is expected behaviour, but it may be undesirable if you need a certain number of assets in your portfolio. 

In order to coerce the mean-variance optimizer to produce more non-negligible weights, we add what can be thought of as a “small weights penalty” to all of the objective functions, parameterised by $\gamma$ (gamma). This term reduces the number of negligible weights, because it has a minimum value when all weights are equally distributed, and maximum value in the limiting case where the entire portfolio is allocated to one asset. We refer to it as L2 regularisation because it has exactly the same form as the L2 regularisation term in machine learning, though a slightly different purpose (in ML it is used to keep weights small while here it is used to make them larger).

!!! note "Gamma"
    In practice, $\gamma$ must be tuned to achieve the level of regularisation that you want. However, if the universe of assets is small (less than 20 assets), then gamma=1 is a good starting point. For larger universes, or if you want more non-negligible weights in the final portfolio, increase gamma.
</div>

``` markdown title="gamma"
Optional[int] = 2
```
<div class="result" markdown>
L2 regularisation parameter. Defaults to 2. Increase if you want more non-negligible weights
</div>

``` markdown title="custom_objectives"
Optional[List[Tuple[Type, Dict[str, Any]]]] = None
```
<div class="result" markdown>
List of lambda functions to add new term into the based objective function. This term must be convex, and built from cvxpy atomic functions.
</div>

``` markdown title="custom_constraints"
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

```python
Optimizer.portfolio(
    self,
    model: str,
    cov_matrix_params: Dict[str, Any],
    expected_returns_params: Optional[Dict[str, Any]] = None,
    black_litterman: Optional[bool] = False,
    black_litterman_params: Optional[Dict[str, Any]] = None,
    weight_bounds: Optional[Tuple[int, int]] = (-1, 1),
    **kwargs
) -> "Optimizer":
```

Base optimizer model, allowing for the efficient computation of optimized asset weights. The portfolio method houses different optimization methods, which generate optimal portfolios for various possible objective functions and parameters.

!!! warning "New Object Instantiation"
    A new object should be instantiated if you want to make any change to objectives/constraints/bounds/parameters.

##### Parameters

``` markdown title="model"
Optional[str] = "mvo"
```
<div class="result" markdown>
Type of optimizer to be used.
Type of optimization:

* `mvo`: Mean-variance optimization
* `hrp`: Hierarchical Risk Parity
  
Work in progress:

* `kelly`: Kelly criterion

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

``` markdown title="black_litterman"
 Optional[bool] = False
```
<div class="result" markdown>
Black-litterman integration.

Black-Litterman model takes a Bayesian approach to asset allocation. Specifically, it combines a prior estimate of returns (for example, the market-implied returns) with views on certain assets, to produce a posterior estimate of expected returns. It will then meaningfully propagate views, taking into account the covariance with other assets. 

The alpha blocks implementation works with the Black-Litterman absolute views, where views direction is extracted from the median of weight range and the magnitude is $1.96\sigma$ ($\sigma$ is the annualized volatility calculated from log returns).

!!! note "1.96 is Hardcoded"
    In probability and statistics, the 97.5th percentile point of the standard normal distribution is a number commonly used for statistical calculations. The approximate value of this number is 1.96, meaning that 95% of the area under a normal curve lies within approximately 1.96 standard deviations of the mean.
    
</div>

``` markdown title="black_litterman_params"
Optional[Dict[str, Any]] = None
```
<div class="result" markdown>

Black-Litterman parameters. It accepts the following variables:

* `pi` (np.ndarray, pd.Series, optional) – Nx1 prior estimate of returns, defaults to None. If pi=”market”, calculate a market-implied prior (requires market_caps to be passed). If pi=”equal”, use an equal-weighted prior.
* `omega` (np.ndarray or Pd.DataFrame, or string, optional) – KxK view uncertainty matrix (diagonal), defaults to None Can instead pass “idzorek” to use Idzorek’s method (requires you to pass view_confidences). If omega=”default” or None, we set the uncertainty proportional to the variance.
* `view_confidences` (np.ndarray, pd.Series, list, optional) – Kx1 vector of percentage view confidences (between 0 and 1), required to compute omega via Idzorek’s method.
* `tau` (float, optional) – the weight-on-views scalar (default is 0.05)
risk_aversion (positive float, optional) – risk aversion parameter, defaults to 1
* `market_caps` (np.ndarray, pd.Series, optional) – (kwarg) market caps for the assets, required if pi=”market”
* `risk_free_rate` (float, defaults to 0.02) – (kwarg) risk_free_rate is needed in some methods

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