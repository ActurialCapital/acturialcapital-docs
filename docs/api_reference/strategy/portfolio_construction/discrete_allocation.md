# Discrete Allocation

## Strategy.discrete_allocation

```python
Strategy.discrete_allocation(
  method: Optional[str] = 'uniform', 
  range_bound: Optional[str] = 'mid'
) ‑> opendesk.strategy.Strategy
```

Discrete allocation allows the implementation of single or multiple pre-determined rule-based allocation strategies. It builds optimal, high level, diversified portfolios, at scale. 

### Parameters

``` markdown title="method"
Optional[str] = "equal_weighted"
```
<div class="result" markdown>
Method used to allocate weights. Possible methods are:

* `equal_weighted`: Asset equally weighted
* `market_cap_weighted`: Asset weighted in proportion to their free-float market cap
* `score_weighted`: Asset weighted in proportion to their target-factor scores
* `score_tilt_weighted`: Asset weighted in proportion to the product of their market cap and factor score
* `inverse_volatility_weighted`: Asset weighted in proportion to the inverse of their historical volatility
* `minimum_correlation_weighted`: Optimized weighting scheme to obtain a portfolio with minimum volatility under the assumption that all asset have identical volatilities

Defaults to `equal_weighted`.
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

### Returns

`opendesk.strategy.Strategy` instance

!!! example "Example Discrete Allocation"

    ```python
    from opendesk import Strategy
    from opendesk.blocks import Reversion, TrendFollowing

    strategy = Strategy(steps=steps, topdown=True, mapping_table=mapping_table)
    strategy.fit(df).estimate(sum)
    strategy.discrete_allocation(
      method="equal_weighted", 
      range_bound="mid"
    )
    ```

    <div class="termy">
      ```console
      $ weights = pd.Series(strategy.weights, name="weights")
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