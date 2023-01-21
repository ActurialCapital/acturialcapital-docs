# Portfolio

```python
portfolio.Portfolio(
    data: pandas.core.frame.DataFrame,
    group_constraints: Optional[Dict[str, Tuple[float, float]]] = None,
    exposures: Optional[pandas.core.series.Series] = None,
    topdown: Optional[bool] = False,
    mapping_table: Optional[Dict[str, str]] = None,
    freq: Optional[int] = 252
)
```

Portfolio construction is the process of creating a balanced collection of investments that aligns with an investor's financial goals, risk tolerance, and investment horizon. The goal of portfolio construction is to maximize returns while minimizing risk.

## Parameters

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
Optional[pandas.core.series.Series] = None
```
<div class="result" markdown>
Strategy exposures attribute estimated from the `estimate()` function. 

In the portfolio optimization section, it also core to the `asset_views` property, to limit the overall average score (exposure) as a custom constraint. E.i. suppose that for each asset you have some “score” – it could be an ESG metric, or some custom risk/return metric. It is simple to specify linear constraints, like “portfolio ESG score must be greater than x”: you simply create a vector of scores, add a constraint on the dot product of those scores with the portfolio weights, then optimize your objective:

!!! example ESG Scores
    ```python hl_lines="4 13"
    from opendesk.blocks import ESGModel

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

## Attributes

### asset_scores

``` markdown title="asset_scores"
Dict[str, float]
```
<div class="result" markdown>
Transform exposures to score at any level. If `topdown` is set to `True`, it transforms exposures at the lower level. Otherwise, It returns `exposures`.
</div>

### asset_views

``` markdown title="asset_views"
Dict[str, float]
```
<div class="result" markdown>

The alpha blocks implementation works with the Black-Litterman `asset_views`, where views direction is extracted from the median of weight range and the magnitude is $1.96 \sigma$, where $\sigma$ is the annualized volatility calculated from log returns.

!!! note "1.96 is Hardcoded"
    In probability and statistics, the 97.5th percentile point of the standard normal distribution is a number commonly used for statistical calculations. The approximate value of this number is 1.96, meaning that 95% of the area under a normal curve lies within approximately 1.96 standard deviations of the mean.
</div>

### lower_bound

``` markdown title="lower_bound"
Dict[str, Tuple]
```
<div class="result" markdown>
Lower weight level constraints by group, from `group_constraints`.
</div>

### mid_bound

``` markdown title="mid_bound"
Dict[str, Tuple]
```
<div class="result" markdown>
Mid weight level constraints by group, from `group_constraints`.
</div>

### target_weights

``` markdown title="target_weights"
Dict[str, float]
```
<div class="result" markdown>
Target weights set through `range_bound` parameter, which equals either `lower_bound`, `mid_bound` or `upper_bound`.
</div>

### upper_bound

``` markdown title="upper_bound"
Dict[str, Tuple]
```
<div class="result" markdown>
Upper weight level constraints by group, from `group_constraints`.
</div>

### weights

``` markdown title="weights"
pandas.core.series.Series
```
<div class="result" markdown>
Weights output from the model.
</div>

### weight_bounds

``` markdown title="weight_bounds"
Optional[Tuple[int, int]]
```
<div class="result" markdown>
Minimum and maximum weight of each asset or single min/max pair if all identical, defaults to (-1, 1). If `weight_bounds=(-1, 1)`, allows short positions.
</div>

## Public Methods

### Portfolio.add

```python
Portfolio.add(
    alpha_block_constraints: Optional[bool] = True,
    n_asset_constraints: Optional[int] = None,
    l2_regularization: Optional[bool] = True,
    gamma: Optional[int] = 2,
    custom_objectives: Optional[List[Tuple[Type, Dict[str, Any]]]] = None,
    custom_constraints: Optional[List[Type]] = None
) -> opendesk.portfolio.Portfolio
```

Add a new objectives and constraints to the optimization problem.

#### Parameters

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

#### Returns

`opendesk.portfolio.Portfolio` instance.

### Portfolio.discrete_allocation

```python
Portfolio.discrete_allocation(
    model: str,
    model_params: Dict[str, Any] = None,
    range_bound: Optional[str] = 'mid'
) -> opendesk.portfolio.Portfolio
```

Discrete allocation allows the implementation of single or multiple pre-determined rule-based allocation strategies. It builds optimal, high level, diversified portfolios, at scale.

#### Parameters

``` markdown title="model"
Optional[str] = "equal_weighted"
```
<div class="result" markdown>
Model used to allocate weights. Possible methods are:

* `equal_weighted`: Asset equally weighted
* `market_cap_weighted`: Asset weighted in proportion to their free-float market cap
* `score_weighted`: Asset weighted in proportion to their target-factor scores
* `score_tilt_weighted`: Asset weighted in proportion to the product of their market cap and factor score
* `inverse_volatility_weighted`: Asset weighted in proportion to the inverse of their historical volatility
* `minimum_correlation_weighted`: Optimized weighting scheme to obtain a portfolio with minimum volatility under the assumption that all asset have identical volatilities

Defaults to `equal_weighted`.
</div>

``` markdown title="model_params"
Dict[str, Any] = None
```
<div class="result" markdown>
Model specific parameters.
</div>

``` markdown title="range_bound"
Optional[str] = "mid"
```
<div class="result" markdown>
Weight bound (from `mapping_weights`). Total budget (in %) to apply. Possible values are:

* `lower`: Lower weight bound
* `mid`: Median weight
* `upper`: Upper weight bound

Defaults to `mid`.
</div>

#### Returns

opendesk.portfolio.Portfolio instance.

### Portfolio.optimize

```python
Portfolio.optimize(
    model: str,
    cov_matrix_params: Dict[str, Any],
    expected_returns_params: Optional[Dict[str, Any]] = None,
    black_litterman: Optional[bool] = False,
    black_litterman_params: Optional[Dict[str, Any]] = None,
    weight_bounds: Optional[Tuple[int, int]] = (-1, 1),
    **kwargs
) -> opendesk.portfolio.Portfolio
```

Base optimizer model, allowing for the efficient computation of optimized asset weights. The portfolio method houses different optimization methods, which generate optimal portfolios for various possible objective functions and parameters.

!!! warning "New Object Instantiation"
    A new object should be instantiated if you want to make any change to objectives/constraints/bounds/parameters.

#### Parameters

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

#### Returns

`opendesk.portfolio.Portfolio` instance.