# Discrete Allocation

The `portfolio()` calls the [`PortfolioConstruction`](../../portfolio/index.md) class, which includes the implementation of the `discrete_allocation()` built-in public methods for discrete allocation procedures, which allows multiple pre-determined rule-based allocation strategies

## discrete_allocation

```python
Portfolio.discrete_allocation(
    model: str,
    model_params: Dict[str, Any] = None,
    range_bound: Optional[str] = 'mid'
) ‑> OrderedDict
```

Discrete allocation is a method for implementing predefined, rule-based allocation strategies for building optimal, diversified portfolios at scale. The use of the `topdown` parameter, when set to `True`, can introduce additional complexity in the portfolio construction process, as it involves diversifying allocation across a wide range of assets, using techniques such as uniform or market cap-based allocation. The `PortfolioConstruction.discrete_allocation()` function calls the `DiscreteAllocation` class (only when `topdown=True`), which offers a variety of rule-based weighting schemes. These weighting schemes, which are commonly used to construct factor portfolios, are designed to achieve a range of portfolio objectives.

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
* `inv_volatility_weighted`: Asset weighted in proportion to the inverse of their historical volatility
* `min_correlation_weighted`: Optimized weighting scheme to obtain a portfolio with minimum volatility under the assumption that all asset have identical volatilities

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

`OrderedDict`, discrete weights.

## Example Discrete Allocation
!!! example "Example Discrete Allocation"

    ```python
    from opendesk import Strategy

    strategy = Strategy(steps=steps, topdown=True, mapping_table=mapping_table)
    strategy.fit(df).estimate(sum)
    weights = strategy.portfolio(stock_prices).discrete_allocation(model="equal_weighted")
    ```

