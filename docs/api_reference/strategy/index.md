# Strategy

## Strategy
```python
opendesk.Strategy(
    steps: List[Tuple[str, Type, Dict, Dict]], 
    topdown: Optional[bool] = False, 
    mapping_table: Optional[Dict[str, str]] = None, 
    mapping_weights: Optional[Dict[int, Tuple]] = None
)
```

The main implementation of the library is the `Strategy` class, which initializes the execution of the sequence and allows to compute tilts/exposures, create and backtest portfolios.

!!! info "Fluent interface"
    In object-oriented programming, returning self from a method allows for the implementation of a fluent interface, where methods can be cascaded i a single statement.

    In object-oriented programming, returning `self` from a method can be useful for several reasons. One common use case is to create a fluent interface, which is an API design style that allows method calls to be chained together in a single statement. This can make the code more readable and concise, as it eliminates the need to create intermediate variables to store the results of intermediate method calls. For example, with a fluent interface, this code could be written as `results = SomeClass().method1().method2().method3()`.

## Parameters

``` markdown title="steps"
List[Tuple[str, Type, Dict, Dict]]
```
<div class="result" markdown>
Alpha blocks in order of execution. More information, please visit the [Model Glossary](../../model_glossary/index.md).
The parameter `steps` is required to enable the smooth integration of the strategy into the module with minimal disruption. It is a list of tuples containing:

* Custom name of the selected block (e.i. `myblock`)
* Class object of the selected block (e.i. `MyBlock`)
* Necessary parameters used at the block initialization (`__init__`) section (e.i. `dict(param_1 = "param_1, param_2 = "param_2")`)
* Additional data, `**kwargs`, required to train the model from the method `processing()`

!!! example "Example"
    ```python
    from opendesk.blocks import Reversion
    steps=[("Reversion", Reversion)]
    ```

</div>

``` markdown title="topdown"
Optional[bool] = False
```
<div class="result" markdown>
Activates top-down strategy. The strategy tilts is processed at a higher level (e.i. sector level) than the actual allocation exectution (e.i. stock level). If set to `True`, a mapping table should be passed. Defaults to `False`.
</div>

``` markdown title="mapping_table"
Optional[Dict[str, str]] = None
```
<div class="result" markdown>
Maps higher with lower level assets. Variable `topdown` needs to be turned to `True`. Defaults to `None`.
</div>

``` markdown title="mapping_weights"
Optional[Dict[int, Tuple]] = None
```
<div class="result" markdown>
Maps scores with range of weights. Defaults to `None`.
</div>

## Ancestors (in MRO)

* opendesk.portfolio.Portfolio
* pypfopt.efficient_frontier.efficient_frontier.EfficientFrontier
* pypfopt.base_optimizer.BaseConvexOptimizer
* pypfopt.base_optimizer.BaseOptimizer


## Attributes

### breakdown

``` markdown title="breakdown"
pandas.core.frame.DataFrame
```
<div class="result" markdown>
Output (scores) of all provided blocks. Following the method `fit()`, the attribute `breakdown` can be accessed, which contains the output from various models in a single `pandas.DataFrame` object

!!! example "Breakdown"
    <div class="termy">
    ```console
    $ strategy.breakdown
    <span style="color: grey;">           Model 1  Model 2  Model 3  Model 4
    sector 1        -1        0        2        1
    sector 2         2       -2        0        1
    sector 3         1        1       -2        2
    sector 4        -2        0       -1        0
    sector 5         0       -1       -1        2
    sector 6        -1        0        1       -2
    sector 7        -2        1       -2       -1
    sector 8         1        2        0       -1
    sector 9         0        0       -1        0
    sector 10        2        0        1        0
    </span>
    ```
    </div>

</div>

### exposures

``` markdown title="exposures"
pandas.core.frame.DataFrame
```
<div class="result" markdown>
Strategy exposures/tilts aggregated from model scores.
</div>


### model_data

``` markdown title="model_data"
pandas.core.frame.DataFrame
```
<div class="result" markdown>
Adjusted closing prices of the asset, each row is a date and each column is a ticker/id.
</div>

### weights

``` markdown title="weights"
Dict[str, float]
```
<div class="result" markdown>
Portfolio weights calculated through the discrete allocation `method`.
</div>

!!! example "Bounds"
    === "Group Constraints"

        <div class="termy">
          ```console
          $ strategy.group_constraints
          <span style="color: grey;">{'sector 1': (0.0, 0.0),
          'sector 2': (0.05, 0.15),
          'sector 3': (0.0, 0.0),
          'sector 4': (-0.15, -0.05),
          'sector 5': (0.0, 0.0),
          'sector 6': (0.0, 0.0),
          'sector 7': (0.0, 0.0),
          'sector 8': (0.05, 0.15),
          'sector 9': (0.0, 0.0),
          'sector 10': (0.0, 0.0)}
          </span>
          ```
        </div>

    === "Lower Bound"

        <div class="termy">
          ```console
          $ strategy.lower_bound
          <span style="color: grey;">{'sector 1': 0.0,
          'sector 2': 0.05,
          'sector 3': 0.0,
          'sector 4': -0.15,
          'sector 5': 0.0,
          'sector 6': 0.0,
          'sector 7': 0.0,
          'sector 8': 0.05,
          'sector 9': 0.0,
          'sector 10': 0.0}
          </span>
          ```
        </div>

    === "Mid Bound"

        <div class="termy">
          ```console
          $ strategy.mid_bound
          <span style="color: grey;">{'sector 1': 0.0,
          'sector 2': 0.1,
          'sector 3': 0.0,
          'sector 4': -0.1,
          'sector 5': 0.0,
          'sector 6': 0.0,
          'sector 7': 0.0,
          'sector 8': 0.1,
          'sector 9': 0.0,
          'sector 10': 0.0}
          </span>
          ```
        </div>

    === "Upper Bound"

        <div class="termy">
          ```console
          $ strategy.upper_bound
          <span style="color: grey;">{'feature 1': 0.0,
          'feature 2': 0.15,
          'feature 3': 0.0,
          'feature 4': -0.05,
          'feature 5': 0.0,
          'feature 6': 0.0,
          'feature 7': 0.0,
          'feature 8': 0.15,
          'feature 9': 0.0,
          'feature 10': 0.0}
          </span>
          ```
        </div>

## Instance variables

### group_constraints

``` markdown title="group_constraints"
Optional[Dict[str, Tuple(float, float)]]
```
<div class="result" markdown>
Strategy constraints by group. Product of `exposures` and `mapping_weights`.
</div>

## Public Methods

### Exposures

!!! note "Exposures API"
    API available in [Exposures](./exposures.md).

* `add_blocks()`: After initialization, additional blocks can be added to `steps`
* `check_group_constraints()`: Check group constraints after creating a portfolio
* `estimate()`: Aggregate exposures by summing each units using a predetermined function
* `fit()`: Executes each provided blocks with provided dataset

### Portfolio Construction

!!! note "Portfolio Construction API"
    API available in [Portfolio Construction](./portfolio_construction/index.md).


* `portfolio()`: Find portfolio weights, at any levels

### Discrete Allocation

#### Built-In Methods

* `discrete_allocation()`: Set portfolio weights following a discrete allocation weighting scheme

#### Inherited Methods

* `equal_weighted`: Asset equally weighted
* `market_cap_weighted`: Asset weighted in proportion to their free-float market cap
* `score_weighted`: Asset weighted in proportion to their target-factor scores
* `score_tilt_weighted`: Asset weighted in proportion to the product of their market cap and factor score
* `inverse_volatility_weighted`: Asset weighted in proportion to the inverse of their historical volatility
* `minimum_correlation_weighted`: Optimized weighting scheme to obtain a portfolio with minimum volatility under the assumption that all asset have identical volatilities
  
### Optimization

#### Built-In Methods

* `add()`: Add a new objectives and constraints to the optimization problem 
* `optimize()`: Portfolio optimization, which aims to select the optimal mix of assets in a portfolio in order to satisfy the defined objectives and constraints

#### Inherited Methods

* `min_volatility()`: optimizes for minimum volatility
* `max_sharpe()`: optimizes for maximal Sharpe ratio (a.k.a the tangency portfolio)
* `max_quadratic_utility()`: maximises the quadratic utility, given some risk aversion
* `efficient_risk()`: maximises return for a given target risk
* `efficient_return()`: minimises risk for a given target return
* `clean_weights()`: rounds the weights and clips near-zeros
