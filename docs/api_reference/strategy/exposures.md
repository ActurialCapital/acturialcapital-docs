# Exposures

## Strategy.add_blocks

```python
Strategy.add_blocks(
  *args: Tuple[str, Type, Dict, Dict]
) -> opendesk.strategy.Strategy:
```

After initialization, additional strategies (blocks) can be added to the instance `steps`. We included in the below snippet two additionnal examples: A reversion factor and a Markov regime switching model.

### Parameters

``` markdown title="*args"
Tuple[str, Type, Dict, Dict]
```
<div class="result" markdown>
Additional blocks to be added.
</div>

### Returns

`opendesk.strategy.Strategy` instance.

!!! example "Example Add Blocks"

    ```py 
    from opendesk import Strategy
    from opendesk.blocks import Reversion, MarkovRegression)

    # pre-trained model
    strategy = Strategy(steps)
    # print(strategy)
    # Blocks -> signal + gaussian_mixture

    strategy.add_blocks(
        (
            "reversion",
            Reversion, # (1)
            {'short_term_rule': None, 'long_term_rule': None}
        ),
        (
            "markov_regression",
            MarkovRegression, # (2)
            {"n_phase": 3},
            {"cli": cli}
        )
    )
    ```

    1.  Calculate sentiment using Reversion Ranking Method.
        More information provided in the [Model Glossary](model_glossary/sentiment/reversion_models).
    2.  Calculate sentiment using Trend Following Ranking Method.
        More information provided in the [Model Glossary](model_glossary/sentiment/trend_following_models).

    <div class="termy">
    ```console
    $ print(strategy)
    <span style="color: grey;">Blocks -> signal + gaussian_mixture + reversion + markov_regression</span>
    ```
    </div>

## Strategy.check_goup_constraints

```python
Strategy.check_group_constraints(
  weights: pandas.core.series.Series
) ‑> pandas.core.frame.DataFrame
```

The performance of the model can be assessed by examining the satisfaction of predetermined constraints. By comparing the output of the model to the set of exposures produced by the actual allocation, it is possible to determine if it is operating as intended. To do this, we use the `check_group_constraints()` method, which groups assets and sum their weights.

### Parameters

``` markdown title="weights"
pandas.core.series.Series
```
<div class="result" markdown>
Portfolio weights calculated either with the `optimize` or the `discrete_allocation` methods.
</div>

### Returns

`pandas.core.frame.DataFrame` object with calculated aggregated weights and target weights.

!!! example "Example Group Constraints"

    ```python
    from opendesk import Strategy
    from opendesk.blocks import Reversion, TrendFollowing

    strategy = (
      Strategy(
        steps=steps,
        topdown=True,
        mapping_table=mapping_table,
      )
      .fit(df)
      .estimate(sum)
      .optimize(data=stock_prices, backend="pypfopt")
      .portfolio(
        model="mvo",
        expected_returns_params={
            "method": "capm_return",
        },
        cov_matrix_params={
            "method": "sample_cov",
        },   
        weight_bounds=(-1, 1)
      )
      .add(
        constraints=[
          lambda w: w <= 0.1, 
          lambda w: w >= -0.1
        ]
      )
    )

    strategy.efficient_risk(target_volatility=0.08, market_neutral=True)
    weights = pd.Series(strategy.clean_weights(), name="weights")
    ```

    <div class="termy">
    ```console
    $ strategy.check_group_constraints(weights)
    <span style="color: grey;">            weights  target weights
    sector 1      0.15    (0.05, 0.15)
    sector 10     0.10    (0.05, 0.15)
    sector 2      0.00      (0.0, 0.0)
    sector 3      0.00      (0.0, 0.0)
    sector 4     -0.05  (-0.15, -0.05)
    sector 5     -0.15  (-0.15, -0.05)
    sector 6      0.00      (0.0, 0.0)
    sector 7      0.00      (0.0, 0.0)
    sector 8     -0.05  (-0.15, -0.05)
    sector 9      0.00      (0.0, 0.0)
    </span>
    ```
    </div>

## Strategy.estimate

```python
Strategy.estimate(
  func: Type, 
  inplace: Optional[bool] = False
) ‑> Union[pandas.core.series.Series, opendesk.strategy.Strategy]
```

Aggregate exposures by aggregating each units using a predetermined function `func`.

### Parameters

``` markdown title="func"
Type
```
<div class="result" markdown>
Strategy exposures/tilts aggregated from model scores. The `func` parameter can be any object that is compatible with the `.apply` function in the pandas library.
</div>

``` markdown title="inplace"
Optional[bool] = False
```
<div class="result" markdown>
Returns a copy of `exposures`. Defaults to `False`.
</div>

### Returns

`opendesk.strategy.Strategy` instance.

!!! example "Example Estimate"

    Some examples of different `func` parameter are provided below:

    === "Mean"
        <div class="termy">
        ```console
        $ strategy.estimate(mean, inplace=True)
        <span style="color: grey;">sector 1     0.50
        sector 2     0.25
        sector 3     0.50
        sector 4    -0.75
        sector 5     0.00
        sector 6    -0.50
        sector 7    -1.00
        sector 8     0.50
        sector 9    -0.25
        sector 10    0.75
        Name: mean, dtype: float64
        </span>
        ```
        </div>

    === "Median"
        <div class="termy">
        ```console
        $ from statistics import median
        $ strategy.estimate(median, inplace=True)
        <span style="color: grey;">sector 1     0.5
        sector 2     0.5
        sector 3     1.0
        sector 4    -0.5
        sector 5    -0.5
        sector 6    -0.5
        sector 7    -1.5
        sector 8     0.5
        sector 9     0.0
        sector 10    0.5
        Name: median, dtype: float64
        </span>
        ```
        </div>
    
    === "Max"
        <div class="termy">
        ```console
        $ from statistics import median
        $ strategy.estimate(median, inplace=True)
        <span style="color: grey;">sector 1     2
        sector 2     2
        sector 3     2
        sector 4     0
        sector 5     2
        sector 6     1
        sector 7     1
        sector 8     2
        sector 9     0
        sector 10    2
        Name: max, dtype: int64
        </span>
        ```
        </div>
        
    === "Min"
        <div class="termy">
        ```console
        $ from statistics import median
        $ strategy.estimate(median, inplace=True)
        <span style="color: grey;">sector 1    -1
        sector 2    -2
        sector 3    -2
        sector 4    -2
        sector 5    -1
        sector 6    -2
        sector 7    -2
        sector 8    -1
        sector 9    -1
        sector 10    0
        Name: min, dtype: int64
        </span>
        ```
        </div>

## Strategy.fit

```python
Strategy.fit(
  model_data: pandas.core.frame.DataFrame, 
  backend: Optional[str] = 'joblib', 
  verbose: Optional[bool] = True
) ‑> opendesk.strategy.Strategy
```

Executes each provided blocks with common dataset.

### Parameters

``` markdown title="model_data"
pandas.core.frame.DataFrame
```
<div class="result" markdown>
Market returns time-series used to train the model.
</div>

``` markdown title="backend"
Optional[str] = "joblib"
```
<div class="result" markdown>
Run parallel multiprocessing or iterative process. Defaults to `"joblib"`.
</div>

``` markdown title="verbose"
Optional[bool] = True
```
<div class="result" markdown>
Joblib progress messages are printed. Defaults to `True`.
</div>

### Returns

`opendesk.strategy.Strategy` instance.

!!! example "Example Fit"

    After initializing the class, the market returns can be fit using the `fit()` method, as in the following example:

    ```py
    strategy = Strategy(steps).fit(df)
    ```

    This returns the instance object `self` on which the method was called. 

