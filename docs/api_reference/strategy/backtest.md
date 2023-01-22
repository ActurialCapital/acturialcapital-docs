# Backtest

## Strategy.backtest

```python
@classmethod
opendesk.Strategy.backtest(
    config: BacktestConfig, 
    **kwargs
) ‑> vectorbt.portfolio.base.Portfolio
```

The `backtest` instance of the `Strategy` class is a **classmethod**. It is used to run a simulation using a custom order function `from_order_func`. It requires `BacktestConfig` **dataclass**, a configuration pipeline which simplifies model configurations.

!!! info "`@classmethod`"
    A class method is a method that is bound to the class and not the instance of the class. It can be called on the class itself, as well as on any instance of the class. In Python, a class method is defined using the `@classmethod` decorator.

The `backtest` classmethod initially initializes and fits the strategy using the `fit()` method, and estimates its exposures using the `estimate()` method. Afterwards, it constructs a portfolio based on specified orchestration and methodologies at each point in time. The final result is a vectorbt object, which provides access to the full range of functionality offered by the vectorbt ecosystem.

### Parameters

``` markdown title="config"
opendesk.backtest.config.BacktestConfig
```
<div class="result" markdown>
Backtesting configuration pipeline `dataclass`, which includes the necessary parameters to activate the internal strategy builder, is required for this process.
</div>

``` markdown title="kwargs"
Dict
```
<div class="result" markdown>
Additional parameters for `vbt.portfolio.base.Portfolio.from_order_func` function.
</div>

### Returns

`vectorbt.Portfolio.from_order_func`: vectorbt `Portfolio` ecosystem.

!!! example "Example Backtest"

        The `backtest` classmethod is initalized through the `BacktestConfig` dataclass, which facilitates feature integration. Mandatory variables `universe`, `model_data` and `steps` are set, as follow:

        <div class="termy">
        ```console
        $ from opendesk import BacktestConfig
        $ from opendesk import Strategy
        $ config = BacktestConfig(
        $     universe=stock_prices, 
        $     model_data=model_data, 
        $     steps=steps
        $ )
        $ backtest = Strategy.backtest(config)
        $ backtest.stats()
        <span style="color: grey;">Start                                 2019-01-02 00:00:00
        End                                   2022-12-30 00:00:00
        Period                                 1008 days 00:00:00
        Start Value                                         100.0
        End Value                                      152.092993
        Total Return [%]                                52.092993
        Benchmark Return [%]                            62.173178
        Max Gross Exposure [%]                          31.819902
        Total Fees Paid                                       0.0
        Max Drawdown [%]                                13.822639
        Max Drawdown Duration                   320 days 00:00:00
        Total Trades                                          391
        Total Closed Trades                                   372
        Total Open Trades                                      19
        Open Trade PnL                                  14.614093
        Win Rate [%]                                    54.301075
        Best Trade [%]                                 122.866679
        Worst Trade [%]                               -164.309231
        Avg Winning Trade [%]                           22.115064
        Avg Losing Trade [%]                            -19.44764
        Avg Winning Trade Duration    158 days 21:51:40.990099010
        Avg Losing Trade Duration     143 days 02:49:24.705882354
        Profit Factor                                    1.417933
        Expectancy                                        0.10075
        Sharpe Ratio                                     0.942305
        Calmar Ratio                                     0.799575
        Omega Ratio                                      1.179846
        Sortino Ratio                                    1.431216
        Name: group, dtype: object
        </span>
        ```
        </div>

## backtest.BacktestConfig

```python
@dataclass
backtest.config.BacktestConfig(
    steps: List, 
    universe: pandas.core.frame.DataFrame, 
    model_data: Optional[pandas.core.frame.DataFrame] = None, 
    topdown: Optional[bool] = False, 
    mapping_table: Optional[Dict] = None, 
    mapping_weights: Optional[Dict] = None, 
    freq: Optional[int] = 252, 
    verbose: Optional[bool] = False, 
    fit_backend: Optional[str] = 'joblib', 
    estimate: Optional[Type] = <built-in function sum>, 
    portfolio: Optional[str] = 'optimize', 
    optimize_model: Optional[str] = 'mvo', 
    optimize_cov_matrix_params: Optional[Dict[str, Any]] = <factory>, 
    optimize_expected_returns_params: Optional[Dict[str, Any]] = <factory>, 
    optimize_black_litterman: Optional[bool] = False,
    optimize_black_litterman_params: Optional[Dict[str, Any]] = None, 
    optimize_weight_bounds: Optional[Tuple[int, int]] = (-1, 1), 
    optimize_solver: Optional[str] = None, 
    optimize_verbose: Optional[bool] = False, 
    optimize_solver_options: Optional[Dict] = None, 
    add_alpha_block_constraints: Optional[bool] = True, 
    add_n_asset_constraints: Optional[int] = None, 
    add_l2_regularization: Optional[bool] = True, 
    add_gamma: Optional[int] = 2, 
    add_custom_objectives: Optional[List[Tuple[Type, Dict[str, Any]]]] = None, 
    add_custom_constraints: Optional[List[Type]] = None, 
    optimize_method: Optional[str] = 'efficient_risk', 
    optimize_params: Optional[Dict[str, Any]] = <factory>, 
    discrete_allocation_model: Optional[str] = 'equal_weighted',
    discrete_allocation_model_params: Dict[str, Any] = None, 
    discrete_allocation_range_bound: Optional[str] = 'mid', 
    backtest_every_nth: Optional[int] = 30, 
    backtest_history_len: Optional[int] = -1, 
    backtest_direction: Optional[str] = 'long_short', 
    backtest_backup: Optional[str] = 'discrete_allocation', 
    backtest_backup_params: Optional[Dict[str, Any]] = <factory>
)
```

!!! info "`@dataclass`"
    A `dataclass` is a decorator that is used to define a class that will be used to store data. It automatically generates special methods, such as `__init__`, `__repr__`, and `__eq__`, based on the class's attributes. A dataclass is defined using the `@dataclass` decorator.


### Parameters

``` markdown title="steps"
List[Tuple[str, Type, Dict, Dict]]
```
<div class="result" markdown>
Alpha blocks in order of execution. More information, please visit [the Model Glossary](../../model_glossary/index.md).
The parameter `steps` is required to enable the smooth integration of the strategy into the module with minimal disruption. It is a list of tuples containing:

* Custom name of the selected block (e.i. `myblock`)
* Class object of the selected block (e.i. `MyBlock`)
* Necessary parameters used at the block initialization (`__init__`) section (e.i. `dict(param_1="param_1, param_2="param_2")`)
* Additional data, `**kwargs`, required to train the model from the method `processing()`
</div>

``` markdown title="universe"
pandas.core.frame.DataFrame
```
<div class="result" markdown>
Market price time-series universe, each row is a date and each column is a ticker/id.
</div>

``` markdown title="model_data"
Optional[pandas.core.frame.DataFrame] = None
```
<div class="result" markdown>
Market returns time-series used to train the model.
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

``` markdown title="fit_backend"
Optional[str] = "joblib"
```
<div class="result" markdown>
Run parallel multiprocessing or iterative process.
</div>

``` markdown title="verbose"
Optional[bool] = None
```
<div class="result" markdown>
Progress messages are printed. Applied to `fit()` and `optimize()` functions.
</div>

``` markdown title="estimate"
Optional[Type] = sum
```
<div class="result" markdown>
Strategy exposures/tilts aggregated from model scores. The `func` parameter can be any object that is compatible with the `.apply` function in the pandas library.
</div>

``` markdown title="portfolio"
Optional[str] = "optimize"
```
<div class="result" markdown>
Portfolio construction procedure to allocate weights. It could be `optimize` or `discrete allocation`. Defauts to `optimize`
</div>

``` markdown title="optimize_model"
Optional[str] = "mvo"
```
<div class="result" markdown>
Type of optimizer to be used.
Type of optimization:

* `mvo`: Mean-variance optimization
* `hrp`: Hierarchical Risk Parity

</div>

``` markdown title="optimize_expected_returns_params"
Optional[Dict[str, Any]]
```
<div class="result" markdown>
Parameters to compute an estimate of future returns:

* `method` (str): the return model to use. Should be one of:
    * `mean_historical_return`
    * `ema_historical_return`
    * `capm_return`
* `**kwargs`: Method specificities
Defaults to:

```python
dict(
    method="ema_historical_return", 
    compounding=True, 
    span=500, 
    frequency=252, 
    log_returns=False
)
```
</div>

``` markdown title="optimize_cov_matrix_params"
Optional[Dict[str, Any]]
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
Defautls to:

```python
dict(
    method="ledoit_wolf", 
    frequency=252, 
    log_returns=False
)
```
</div>

``` markdown title="optimize_weight_bounds"
Optional[Tuple[int, int]] = (-1, 1)
```
<div class="result" markdown>
Minimum and maximum weight of each asset or single min/max pair if all identical, defaults to (-1, 1). If `weight_bounds=(-1, 1)`, allows short positions. Defaults to `(-1, 1)`.
</div>

``` markdown title="optimize_solver"
Optional[str] = None
```
<div class="result" markdown>
name of solver. list available solvers with: `cvxpy.installed_solvers()`.
</div>

``` markdown title="optimize_solver_options"
Optional[Dict] = None
```
<div class="result" markdown>
Parameters for the given solver.
</div>

``` markdown title="add_alpha_block_constraints"
Optional[bool] = True
```
<div class="result" markdown>
Alpha blocks core constraints. It adds constraints on the sum of weights of different groups of assets. Most commonly, these will be sector constraints. These constraints a particularly relevant when working with alpha blocks (top-down or bottom-up), as we aim to limit our exposure to paricular group of assets. Defaults to `True`.
</div>

``` markdown title="add_n_asset_constraints"
Optional[List[Type]] = None
```
<div class="result" markdown>
Number of assets in the portfolio constraints. Cardinality constraints are not convex, making them difficult to implement. However, we can treat it as a mixed-integer program and solve (provided you have access to a solver). for small problems with less than 1000 variables and constraints, you can use the community version of [CPLEX](https://en.wikipedia.org/wiki/CPLEX) available in python `pip install cplex`.

!!! warning "`n_asset_constraints`"
    This functionnality is still work in progress, as it requires external capabilities (`cplex`).
</div>

``` markdown title="add_l2_regularization"
Optional[bool] = True
```
<div class="result" markdown>
L2 regularisation, i.e $\gamma ||w||^2$, to increase the number of nonzero weights.

Mean-variance optimization often results in many weights being negligible, i.e the efficient portfolio does not end up including most of the assets. This is expected behaviour, but it may be undesirable if you need a certain number of assets in your portfolio. 

In order to coerce the mean-variance optimizer to produce more non-negligible weights, we add what can be thought of as a “small weights penalty” to all of the objective functions, parameterised by $\gamma$ (gamma). This term reduces the number of negligible weights, because it has a minimum value when all weights are equally distributed, and maximum value in the limiting case where the entire portfolio is allocated to one asset. We refer to it as L2 regularisation because it has exactly the same form as the L2 regularisation term in machine learning, though a slightly different purpose (in ML it is used to keep weights small while here it is used to make them larger).

!!! note "Gamma"
    In practice, $\gamma$ must be tuned to achieve the level of regularisation that you want. However, if the universe of assets is small (less than 20 assets), then gamma=1 is a good starting point. For larger universes, or if you want more non-negligible weights in the final portfolio, increase gamma.
</div>

``` markdown title="add_gamma"
Optional[int] = 2
```
<div class="result" markdown>
L2 regularisation parameter. Defaults to 2. Increase if you want more non-negligible weights
</div>

``` markdown title="add_custom_objectives"
Optional[List[Tuple[Type, Dict[str, Any]]]] = None
```
<div class="result" markdown>
List of lambda functions to add new term into the based objective function. This term must be convex, and built from cvxpy atomic functions.
</div>

``` markdown title="add_custom_constraints"
Optional[List[Type]] = None
```
<div class="result" markdown>
 List of lambda function (e.i. all assets <= 3% of the total portfolio = [lambda w: w <= .03]. This constraint must satisfy DCP rules, i.e be either a linear equality constraint or convex inequality constraint.
</div>


``` markdown title="optimize_method"
Optional[str] = "efficient_risk"
```
<div class="result" markdown>
Optimization method that can be called (corresponding to different objective functions).

!!! success "Object Instantiation"
    A new EfficientFrontier object should be instantiated if you want to make any change to objectives/constraints/bounds/parameters. 
    The backtesting framework re-instantiate the optimization process at every rebalancing periods.

</div>

``` markdown title="optimize_params"
Optional[Dict[str, Any]] = dict(target_volatility=0.08, market_neutral=True)
```
<div class="result" markdown>
Optimization method parameters. Defaults to:

```python
dict(
    target_volatility=0.08, 
    market_neutral=True
)
```
</div>

``` markdown title="discrete_allocation_method"
Optional[str] = "uniform"
```
<div class="result" markdown>
Method used to allocate weights.
</div>

``` markdown title="discrete_allocation_model_params"
Dict[str, Any] = None
```
<div class="result" markdown>
Model specific parameters.
</div>

``` markdown title="discrete_allocation_range_bound"
Optional[str] = "mid"
```
<div class="result" markdown>
Bound from `mapping_weights`. Total budget (in %) to apply.
</div>

``` markdown title="backtest_every_nth"
Optional[int] = 30
```
<div class="result" markdown>
Backtest rebalancing period in days. Defaults to 30. 
</div>

``` markdown title="backtest_history_len"
Optional[int] = -1
```
<div class="result" markdown>
Backtest model training period. If -1, the model trains the entire history. Defaults to -1.
</div>

``` markdown title="backtest_direction"
Optional[str] = "long_short"
```
<div class="result" markdown>
Backtest Direction. It can be `long_only`, `short_only' or `long_short`. Defaults to `long_short`.
</div>

``` markdown title="backtest_backup"
 Optional[str] = "discrete_allocation"
```
<div class="result" markdown>
Backtest "back-up" used as a fallback in the event that the optimizer is unable to deliver feasible weights. It can be another portfolio construction procedure to allocate weights: `optimize` or `discrete allocation`. Defauts to `discrete_allocation`.
</div>

``` markdown title="backtest_backup_params"
Optional[Dict[str, Any]] = dict(range_bound="mid")
```
<div class="result" markdown>
Backtest "back-up" configuration. Defauts to `dict(range_bound="mid")` as `discrete_allocation` is set in `backtest_backup`.

</div>

## Example Backtest

!!! example "Example Backtest"

        For this example, we are using `yfinance`, a [python library to fetch yahoo market data](https://github.com/ranaroussi/yfinance). First, we import the module and query some tickers from 2019 to 2022:
        ```python
        import yfinance as yf

        # Date range
        start = "2019-01-01"
        end = "2022-12-31"

        # Tickers of assets
        assets = [
            "JCI", "TGT", "CMCSA", "CPB", 
            "MO", "APA", "MMC", "JPM",
            "ZION", "PSA", "BAX", "BMY", 
            "LUV", "PCAR", "TXT", "TMO",
            "DE", "MSFT", "HPQ", "SEE",
        ]

        # Downloading data
        data = yf.download(assets, start=start, end=end)
        stock_prices = data.loc[:, ("Close", slice(None))]
        stock_prices.columns = assets
        ```

        ```python
        from opendesk.backtest import BacktestConfig
        from opendesk.blocks import Reversion, SignalBased, TrendFollowing
        from opendesk.strategy import Strategy

        # Config
        config = BacktestConfig(
            universe=close_prices,
            steps=[
                ("Reversion", Reversion),
                ("TrendFollowing", TrendFollowing),
            ],
            portfolio_construction="optimize",
            portfolio_expected_returns_params=dict(
                method="capm_return"
            ),
            portfolio_cov_matrix_params=dict(
                method="sample_cov"
            ),
            solver_method="efficient_risk",
            solver_params=dict(
                target_volatility=0.2, 
                market_neutral=True
            ),
            add_constraints=[
                lambda w: w <= 0.1, 
                lambda w: w >= -0.1
            ],
            backtest_every_nth=30,
        )
        ```

        <div class="termy">
        ```console
        $ backtest = Strategy.backtest(config)
        $ backtest.total_profit(group_by=False)
        <span style="color: grey;">APA      20.123646
        BAX       6.338269
        BMY      -4.664290
        CMCSA     1.361760
        CPB       1.361636
        DE        8.268657
        HPQ       2.813247
        JCI      11.984073
        JPM       1.150170
        LUV      12.407130
        MMC      -4.770791
        MO       -4.857592
        MSFT      6.389380
        PCAR      5.609880
        PSA       4.577316
        SEE      -2.475789
        TGT      -7.691861
        TMO      -5.878451
        TXT       7.768601
        ZION     -7.347374
        Name: total_profit, dtype: float64
        </span>
        ```
        </div>

        More information about the vectorbt ecosystem can be found in the [official documentation](https://vectorbt.dev/api/portfolio/base/#vectorbt.portfolio.base).