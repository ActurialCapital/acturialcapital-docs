# Discrete Allocation

The `portfolio()` calls the [`Portfolio`](../../portfolio/index.md) class, which includes the implementation of the following built-in public methods for discrete allocation procedures:

* `discrete_allocation()` Implementation of single or multiple pre-determined rule-based allocation strategies

Which triggers **PyPortfolioOpt** inherited method:

* `clean_weights()`: rounds the weights and clips near-zeros
  
## Strategy.discrete_allocation

```python
Strategy.discrete_allocation(
    model: str,
    model_params: Dict[str, Any] = None,
    range_bound: Optional[str] = 'mid'
) ‑> opendesk.strategy.Strategy
```

Discrete allocation allows the implementation of single or multiple pre-determined rule-based allocation strategies. It builds optimal, high level, diversified portfolios, at scale. 

### Parameters

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

### Returns

`opendesk.strategy.Strategy` instance

## Strategy.clean_weights

```python
Strategy.clean_weights(
    cutoff: Optional[float] = 0.0001, 
    rounding: Optional[int] = 5
) ‑> OrderedDict
```

Helper method to clean the raw weights, setting any weights whose absolute values are below the cutoff to zero, and rounding the rest.

### Parameters

``` markdown title="cutoff"
Optional[float] = 0.0001
```
<div class="result" markdown>
The lower bound, defaults to 1e-4
</div>

``` markdown title="rounding"
Optional[int] = 5
```
<div class="result" markdown>
Number of decimal places to round the weights, defaults to 5. Set to None if rounding is not desired.
</div>

### Returns

`OrderedDict`, asset weights.

## Example Discrete Allocation
!!! example "Example Discrete Allocation"

    ```python
    from opendesk import Strategy

    strategy = Strategy(steps=steps, topdown=True, mapping_table=mapping_table)
    strategy.fit(df).estimate(sum)
    strategy.portfolio(stock_prices).discrete_allocation(model="equal_weighted")
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

