# Discrete Allocation

## Strategy.discrete_allocation

```python
discrete_allocation(
  self, 
  method: Optional[str] = 'uniform', 
  range_bound: Optional[str] = 'mid'
) ‑> opendesk.strategy.Strategy
```

Discrete allocation allows the implementation of single or multiple pre-determined rule-based allocation strategies. It builds optimal, high level, diversified portfolios, at scale. 

### Parameters

``` markdown title="method"
Optional[str]
```
<div class="result" markdown>
Method used to allocate weights.
</div>

``` markdown title="range_bound"
Optional[str]
```
<div class="result" markdown>
Bound from `mapping_weights`. Total budget (in %) to apply.
</div>

### Returns

`opendesk.strategy.Strategy` instance

### Example Discrete Allocation

```python
from opendesk import Strategy
from opendesk.blocks import Reversion, TrendFollowing

strategy = Strategy(steps=steps, topdown=True, mapping_table=mapping_table)
strategy.fit(df).estimate(sum)
strategy.discrete_allocation(
  method="uniform", 
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