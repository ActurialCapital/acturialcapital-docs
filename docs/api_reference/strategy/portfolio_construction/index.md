# Porfolio Construction

Portfolio construction, which involves translating scores into weights, can be a complex and nuanced process. We have developed two methods that allows for greater flexibility and experimentation: 

* [Optimization](./portfolio_construction/optimization.md)
* [Discrete Allocation](./portfolio_construction/discrete_allocation.md)

These approach enables the exploration of a wide range of potential portfolio compositions.

### Strategy.portfolio

```python
Strategy.portfolio(
  data: Optional[pandas.core.frame.DataFrame] = None, 
) ‑> opendesk.strategy.Strategy
```

#### Parameters

``` markdown title="data"
Optional[pandas.core.frame.DataFrame] = None
```
<div class="result" markdown>
Market price time-series, each row is a date and each column is a ticker/id. If `None`, it takes `model_data`, the dataset used in the `fit()` method. Defaults to `None`.
</div>


#### Returns

`opendesk.strategy.Strategy` instance.