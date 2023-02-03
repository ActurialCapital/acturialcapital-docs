# Porfolio


Portfolio construction is the process of creating a balanced collection of investments that aligns with an investor's financial goals, risk tolerance, and investment horizon. The goal of portfolio construction is to maximize returns while minimizing risk.

Portfolio construction involves translating scores into weights, can be a complex and nuanced process. We have developed two methods that allows for greater flexibility and experimentation: 

* [Optimization](./optimization.md)
* [Discrete Allocation](./discrete_allocation.md)

These approach enables the exploration of a wide range of potential top-down and bottom-up portfolio compositions.

## portfolio

```python
Strategy.portfolio(
  data: Optional[pandas.core.frame.DataFrame] = None, 
) ‑> opendesk.strategy.Strategy
```

### Parameters

``` markdown title="data"
Optional[pandas.core.frame.DataFrame] = None
```
<div class="result" markdown>
Market price time-series, each row is a date and each column is a ticker/id. If `None`, it takes `model_data`, the dataset used in the `fit()` method. Defaults to `None`.
</div>


### Returns

`opendesk.strategy.Strategy` instance.

## PortfolioConstruction

```python
portfolio.PortfolioConstruction(
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
  
## Descendants

* opendesk.strategy.Strategy
  
## Instance variables

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

### upper_bound

``` markdown title="upper_bound"
Dict[str, Tuple]
```
<div class="result" markdown>
Upper weight level constraints by group, from `group_constraints`.
</div>

## Public Methods

* `discrete_allocation()`: Set portfolio weights following a discrete allocation weighting scheme
* `optimize()`: Portfolio optimization, which aims to select the optimal mix of assets in a portfolio in order to satisfy the defined objectives and constraints
