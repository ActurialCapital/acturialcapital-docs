# Backtest Blocks

## Open-Source Resources

This platform aggregates a range of state-of-the-art tools and libraries and facilitates their automation through a unified interface. This allows users to easily access and utilize a wide range of advanced resources for their research or development efforts, streamlining the process of utilizing cutting-edge technologies.

There is a wide range of options available for Python when it comes to selecting a backtesting framework. A comprehensive list of these libraries can be found on the GitHub page titled [awesome-quant](https://github.com/wilsonfreitas/awesome-quant#trading--backtesting). We initially experimented with one called Backtesting and then subsequently tested a few others.

|                                  | [Backtesting](https://kernc.github.io/backtesting.py) | [bt](https://github.com/pmorissette/bt)       | [vectorbt](https://github.com/polakowo/vectorbt) | [backtrader](https://github.com/mementum/backtrader) |
| :------------------------------- | ----------------------------------------------------: | --------------------------------------------: | --------------------------------------------: | --------------------------------------------: |
| How easy is strategy creation?   | :ok_hand: `EASY`                                    | :fontawesome-solid-skull-crossbones: `HARD` | :ok_hand: `EASY` | :ok_hand: `EASY` |
| How quick is it?                 | :material-bike-fast: `FAST`                         | `UNKNOWN`                                       | :material-bike-fast: `FAST` | :material-bike-fast: `FAST` |
| How easy is extracting metrics?  | :material-speedometer-medium: `MEDIUM`              | `UNKNOWN`                                       | :material-speedometer-medium: `MEDIUM` | :fontawesome-solid-skull-crossbones: `HARD` |

!!! success "vectorbt"
    For the purpose of backtesting, vectorbt can be considered as a suitable initial choice for integrating our model pipeline due to its fast performance and ease of use. It also actively managed and a more advance version has been created: [vectorbt **pro**](https://vectorbt.pro/).

## Vectorbt

### Abstract

Vectorbt is a powerful tool that combines the functionality of a fast backtester with advanced data analysis capabilities. It enables users to analyze and evaluate a wide range of trading options, instruments, and time periods with ease, enabling them to identify patterns and optimize their strategy. It allows users to explore and understand complex phenomena in trading data, providing them with valuable insights that can inform their decision-making and potentially give them an informational advantage in the market.

Vectorbt utilizes a vectorized representation of trading strategies in order to optimize performance. This representation involves storing multiple strategy instances in a single multi-dimensional array, as opposed to the traditional object-oriented programming (OOP) approach of representing strategies as classes and data structures. The vectorized representation used by vectorbt allows for more efficient processing and comparison of strategies, and can particularly improve the speed of analysis when dealing with quadratic programming problems.

> vectorbt is a Python package for quantitative analysis that takes a novel approach to backtesting: it operates entirely on pandas and NumPy objects, and is accelerated by Numba to analyze any data at speed and scale. This allows for testing of many thousands of strategies in seconds.

The three main `Portfolio` methods are:

* `from_orders()`
* `from_signals()`
* `from_order_func()`

These methods go through each row and column in the input data provided by the user, and at each step they turn the current element into an order request. They then process this request by updating the current simulation state and adding the filled order record to an array. This array and other information can be used later on to analyze the simulation during the reconstruction phase.

### Numba

Numba is a just-in-time (JIT) compiler for Python that is designed to improve the performance of Python code. It does this by compiling Python code to native machine instructions, which can be executed much faster than the interpreted code that is normally used in Python. Numba works by decorating functions or methods with a special `@jit` decorator, which tells the Numba compiler to compile the function for faster execution. Numba can be used to speed up code that makes heavy use of Python's scientific and numerical libraries, such as NumPy and SciPy, as well as code that performs CPU-bound operations. In addition to its JIT compiler, Numba also provides support for parallel programming through its `@vectorize` and `@guvectorize` decorators, which can be used to parallelize the execution of certain types of functions.

!!! failure "Numba and Third Party Packages"
    Numba is a tool that can significantly improve the speed of code execution. However, it does not support integration with third-party development. Therefore, our current portfolio optimization implementation, which uses PyPortfolioOpt and RiskFolio-Lib, does not utilize Numba, but still maintains efficient performance.

## Step-by-Step Example

The `backtest` classmethod within the `Strategy` class utilizes the vectorbt library to test and evaluate the performance of a given strategy. Vectorbt is a Python package that provides fast and scalable quantitative analysis using pandas and NumPy objects, and is optimized for speed using Numba. 

In the field of software engineering, it is recommended that a function be designed in a manner that promotes testability by adhering to the principles of cohesion. Specifically, this entails limiting the function's scope to a single, well-defined task, as well as minimizing the number of arguments it accepts. 

In cases where a function is required to operate on multiple arguments, it is suggested to employ techniques such as encapsulation by utilizing higher-level objects to group the arguments together. This allows for more robust and maintainable codebase.

This is why we created `Pipeline`, a dataclass object, which encapsultes required arguments to create exposure and build portfolio at each rebalancing period.

### Step 1: BacktestConfig

The `backtest` method is initalized through the `BacktestConfig` dataclass, which facilitates feature integration. For example, variables `universe`, `model_data`, `steps`, `topdown` and `mapping_table` are set to match your requirement. All other variables are pre-set.

```python
from opendesk.backtest import BacktestConfig

config = BacktestConfig(
    universe=stock_prices, 
    model_data=model_data, 
    steps=[(
        "my_block", 
        MyBlock, 
        {"mapping_score": mapping_score}, 
        {"price_earnings": price_earnings}
    )], 
    topdown=True, 
    mapping_table=mapping_table,
    portfolio_construction='optimize',
    portfolio_expected_returns_params=dict(
        method="capm_return"
    ),
    portfolio_cov_matrix_params=dict(
        method="sample_cov"
    ),
    solver_method="efficient_risk",
    solver_params=dict(
        target_volatility=.08, 
        market_neutral=True
    ),
    add_constraints = [
        lambda w: w <=  .03, 
        lambda w: w >= -.03
    ],
    backtest_backup="discrete_allocation"
)
```

The backtest implementation initializes, fits and estimates exposures using the `fit()` and the `estimate()` methods. Then, it optimizes the portfolio at the stock level using the `.optimize()` and `.portfolio()` methods and finds weights that align with the desired level of risk. 

The strategy is rebalanced on a monthly basis, and a `discrete_allocation` is used as a fallback in the event that the optimizer is unable to deliver feasible weights.

### Step 2: Backtest

The output is a [vectorbt.Portfolio object](https://vectorbt.dev/api/portfolio/), which allows users to leverage the entire vectorbt ecosystem.

<div class="termy">
  ```console
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
