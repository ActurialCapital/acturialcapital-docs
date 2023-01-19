# Discrete Allocation

## Strategy.discrete_allocation

```python
Strategy.discrete_allocation(
  data: Optional[pandas.core.frame.DataFrame] = None
) ‑> opendesk.strategy.Strategy
```

Discrete allocation allows the implementation of single or multiple pre-determined rule-based allocation strategies. It builds optimal, high level, diversified portfolios, at scale. 

### Parameters

``` markdown title="data"
Optional[pandas.core.frame.DataFrame] = None
```
<div class="result" markdown>
Market price time-series, each row is a date and each column is a ticker/id. If `None`, it takes `model_data`, the dataset used in the `fit()` method. Defaults to `None`.
</div>

### Returns

`opendesk.strategy.Strategy` instance

!!! example "Example Discrete Allocation"

    ```python
    from opendesk import Strategy

    strategy = Strategy(steps=steps, topdown=True, mapping_table=mapping_table)
    strategy.fit(df).estimate(sum)
    strategy.discrete_allocation(stock_prices).portfolio(model="equal_weighted")
    ```

    <div class="termy">
      ```console
      $ weights = pd.Series(strategy.clean_weights(), name="weights")
      <span style="color: grey;">asset 1      0.00
      asset 2      0.01
      asset 3      0.00
      asset 4      0.00
      asset 5      0.01

      asset 96     0.00
      asset 97    -0.01
      asset 98     0.00
      asset 99    -0.01
      asset 100    0.00
      Name: weights, Length: 100, dtype: float64
      </span>
      ```

## Allocator

```python
discrete_allocation.Allocator(
  data: pandas.core.frame.DataFrame,
  group_constraints: Optional[Dict[str, Tuple[float, float]]] = None,
  exposures: Optional[pd.Series] = None,
  topdown: Optional[bool] = False,
  mapping_table: Optional[Dict[str, str]] = None,
  freq: Optional[int] = 252
)
```

### Parameters

``` markdown title="data"
Optional[pandas.core.frame.DataFrame] = None
```
<div class="result" markdown>
Market price time-series, each row is a date and each column is a ticker/id. If `None`, it takes `model_data`, the dataset used in the `fit()` method. Defaults to `None`.
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
Strategy exposures attribute estimated from the `estimate()` function. 
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

#### `lower_bound`

``` markdown title="lower_bound"
Dict[str, Tuple]
```
<div class="result" markdown>
Lower bound constraints by group, from `group_constraints`.
</div>

#### `mid_bound`

``` markdown title="mid_bound"
Dict[str, Tuple]
```
<div class="result" markdown>
Mid level constraints by group, from `group_constraints`.
</div>

#### `upper_bound`

``` markdown title="upper_bound"
Dict[str, Tuple]
```
<div class="result" markdown>
Upper level constraints by group, from `group_constraints`.
</div>

### Public Methods

#### Allocator.portfolio

##### Parameters

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

##### Returns

opendesk.discrete_allocation.Allocator instance.
