# Main Concepts

Using OpenDesk is easy. First, come up with an idea and make sure it can be organized in a clear and concise way. Then, build one or more "blocks" that bring this idea to life, following OpenDesk's core guidelines. Finally, create a portfolio and test it out through backtesting to see how it performs.

Building **alpha blocks** is a methodology which involves a sequential application of multiple, modular strategies. These blocks are rule-based and can be easily modified, added, or removed from the sequence. Each block returns a score, and the overall approach is designed to facilitate the integration of research and the efficient testing and optimization of strategies and Machine Learning overlaye, while also allowing for the risk-efficient combination of different sources of alpha. The building blocks approach is a comprehensive yet extensible method for data analysis and financial strategy development. 

## Features

- [x]  **Portfolio strategy first**: Opendesk focuses on portfolio strategy development
- [x]  **Fast**: Opendesk system has been designed to achieve superior performance in the development, implementation, and evaluation of strategies, built with [NumPy](https://numpy.org/) and [Pandas](https://pandas.pydata.org/)
- [x]  **Fast to code**: Created through fluent interface, which is an API design style that allows method calls to be chained together in a single statement. This make Opendesk's code more readable and concise, as it eliminates the need to create intermediate variables to store the results of temporary method calls.
- [x]  **Intuitive**: Develop a plan, organize your ideas, conduct research, implement strategies based on the findings, and test various parameters to determine their impact in a single session.
- [x]  **Easy**: Opendesk user interface has been optimized for simplicity and ease of use, reducing the need for extensive training or consultation of documentation.
- [x]  **Batteries included**: 
  
    * Quantitative data pre-processing
    * ML and scientific computing
    * Large and diversified model library
    * Flexible portfolio construction
    * Fast Backtesting capabilities
    * [AWS CloudFormation](https://aws.amazon.com/cloudformation/) service integrated
    * [FastAPI](https://fastapi.tiangolo.com/) web framework built-in

Our platform integrates a diverse set of state-of-the-art tools and libraries, including those for machine learning, data analysis, and scientific computing. These resources are carefully curated to ensure that they represent the most advanced and effective technologies currently available.

## Alpha blocks

The alpha block sequence is a modular approach to creating investment strategies that involves breaking down the code pipeline into individual units, or "blocks," that can be easily replaced or removed. Each block is designed to function as a rule-based strategy, contributing to the overall views and confidence assigned to a particular asset. This approach, which was first proposed by Kowalski[^1] (1979), involves arranging blocks of code in a specific sequence in order to generate an output.The modular design of building sequences allows for easy expansion and integration of alpha sources. This approach has already been well-established in computer science and software engineering (Bass, Clements, & Kazman, 2003[^2]). It is escpecially powerful for integrating research, as it allows for the combination of alpha sources in a risk-sensitive manner and leads towards efficient backtesting and optimization.

!!! info "Did you know?"

    There are several scientific references that support the use of modular approaches and building blocks in investment strategy development. For example, in their paper "Modular Investment Strategies," published in the Journal of Financial Economics[^3], K. G. Rouwenhorst and G. S. Wu discuss the benefits of using a modular approach to constructing investment strategies. 
    They argue that this approach allows for better risk management, as it allows for the independent testing and evaluation of each component of the strategy. Another scientific reference that supports the use of building blocks in investment strategy development is the paper "Building Investment Strategies with Building Blocks," published in the Journal of Portfolio Management[^4]. In this paper, the authors discuss the benefits of using a building block approach, including the ability to easily incorporate new research and the flexibility to adjust the strategy as market conditions change.

### Scores

Scores are numerical values that are used to evaluate or rank various options or candidates. In order to generalize the results, a score is generated for each block of data, which serves as a signal to adjust the level of exposure to a particular asset or group of assets:

* **Scores allow for flexibility:** By calculating scores, you can easily adjust the relative importance of different factors and criteria, or even add or remove factors as needed. This flexibility can be useful when an your goals or circumstances change.
* **Scores can be more easily customized:** Scores can be customized to reflect your specific goals, risk tolerance, and other factors. This can make it easier for you to create portfolios that are tailored to your individual needs and preferences.

* **Scores can help to incorporate subjective factors:** Some investment decisions involve subjective judgment, such as whether a company's management team is strong or whether a particular market is likely to experience significant growth in the near future. Scores can be used to incorporate these subjective factors in a systematic way.

* **Scores can be used to compare investments on a common scale:** By assigning scores to different investments, you can more easily compare them to one another on a common scale. This can make it easier to identify the most attractive candidates for inclusion in a portfolio.

* **Scores extend optimization processes:** The optimization process can use these scores to select the investment portfolio that maximizes returns while minimizing risk, subject to any constraints or objectives that have been defined.
    
### Model Design

The building blocks approach involves sequentially executing multiple steps on a dataset, allowing for the modification of steps, step parameters, and the order in which they are executed. Each block represents a modular, rule-based strategy that can be removed or replaced in the sequence. These blocks return a score for each feature. This approach is designed to facilitate the integration of alpha sources, while also being easily extensible.

``` mermaid
graph LR
  id1[(Database)] --> B[Strategy];
  B --> C[Block A] & D[Block B] & E[Block N];
  C --> I[/Exposures/];
  D --> I;
  E --> I;
```

### Speed

#### Multiprocessing

Using multiprocessing can be an efficient way to integrate the blocks. Multiprocessing refers to the ability of a computer to execute multiple processes concurrently, which can be useful for completing tasks more quickly. By utilizing multiprocessing to integrate each individual units, it is possible to speed up the process of constructing code pipelines and evaluating the performance of investment strategies or models.

``` mermaid
graph LR
  id1[(Database)] --> B[Strategy];
  B --> C[Block A] & D[Block B] & E[Block N];
  subgraph Multiprocessing
  C --- F[/Score A/];
  D --- G[/Score B/];
  E --- H[/Score N/];
  end
  F --> I[/Exposures/];
  G --> I;
  H -->I;
```
  
=== "Advantages"

    1. **Improved performance**: Multiprocessing can improve the performance of your program by allowing it to utilize multiple CPU cores, which can speed up the execution of CPU-bound and multithreaded programs.
    12. **Better utilization of resources**: Multiprocessing allows you to make use of all available CPU cores, which can be especially useful when running programs on a machine with multiple cores or on a multi-core server.
    3. **Easier parallelization**: Python's [Joblib](https://joblib.readthedocs.io/en/latest/parallel.html) module provides a high-level interface for creating and managing processes, making it easier to write parallelized programs. Joblib provides a 
    simple helper class to write parallel for loops using multiprocessing. The core idea is to write the code to be executed as a generator expression, and convert it to parallel computing.

=== "Limitations"

    However, it is important to note that the efficiency of using multiprocessing will depend on the specific characteristics of the blocks and the hardware being used. It may be necessary to carefully analyze the performance of the code and the available resources in order to determine the most appropriate approach for integrating the blocks in the sequence.

In the above example, **5 blocks** (processes) were executed concurrently using backend `LokyBackend` with 8 concurrent workers[^5],
each operating on a dataset of **1000 samples** times **20 sectors**.

The elapsed time for this operation was 1.2 seconds.

<div class="termy">
  ```console
  $ strategy.fit(df).estimate(sum)
  <span style="color: yellow;">[Parallel(n_jobs=-1)]:   </span><span style="color: lime;">Done 1 tasks</span>
  <span style="color: yellow;">[Parallel(n_jobs=-1)]:   </span><span style="color: lime;">Done 2 out of 5</span>    
  <span style="color: yellow;">[Parallel(n_jobs=-1)]:   </span><span style="color: lime;">Done 3 out of 5</span>
  <span style="color: yellow;">[Parallel(n_jobs=-1)]:   </span><span style="color: lime;">Done 4 out of 5</span> 
  <span style="color: yellow;">[Parallel(n_jobs=-1)]:   </span><span style="color: lime;">Done 5 out of 5 | elapsed: 1.2s finished</span>
  ```
</div>

#### Polars

In some instances, we use [polars](https://www.pola.rs/), a lightning-fast DataFrame library for Rust and Python. Polars is written in Rust, uncompromising in its choices to provide a feature-complete DataFrame API to the Rust ecosystem. We use it as a DataFrame library for your data models. Polars is built upon the safe Arrow2 implementation of the Apache Arrow specification, enabling efficient resource use and processing performance. By doing so it also integrates seamlessly with other tools in the Arrow ecosystem.

For example, the `apply` function is used to aggregate scores from different blocks. Here, we tested 10000 rows x 10 columns:

**In pandas:**
```pyton
df.apply(func, axis=1).rename(func.__name__)
```
Time to apply with pandas: `3.211 seconds`

**In polars:**
```python
import polars as pl

pd.Series(
      list(pl.DataFrame(df).with_column(
          pl.fold(0, lambda acc, s: acc + s, pl.all()).alias("sum")
      )[:,-1])
  )
```
Time to apply with polars: `1.012 seconds`

Both function return a `pandas.Series` object, however, Polars is ==3.17x faster== than pandas at `apply`. In a near future, if time complexity becomes an issue, we might turn the entire code base to polars.


## Step-by-Step Example

Let's design the following strategy:

* Long-Short Equity
* Top-Down (from sector to stock level)
* Risk optimized (8% target volatility)
* Market neutral
* **Sentiment-based**, which integrates both sector momentum **reversion** and **trend following** techniques.

### Step 1: Blocks

> To calculate scores, we follow a set of predefined rules that compute the deviation between long-term and short-term sector rankings. This generates a composite factor that averages the relative price return momentum within each sector. These techniques can be found in [the sentiment folder](model_glossary/sentiment) under the `Reversion` and `TrendFollowing` alpha blocks. For more information about these techniques and other blocks, please visit [the Model Glossary](model_glossary).
  
The Strategy class is initialized by defining the steps as outlined above and setting the `topdown` parameter to `True`. 

```python
from opendesk import Strategy
from opendesk.blocks import Reversion, TrendFollowing

strategy = Strategy(
  steps = [
    ("reversion", Reversion),
    ("trend_following", TrendFollowing)
  ],
  topdown=True, 
  mapping_table=mapping_table
)
```

!!! example "Mapping Table"
    Because we are implementing a top-down strategy (parameter `topdown=True`), the `mapping_table` is also passed. The mapping table is a dictionnary, which associates *stocks* with *sectors*, as follow:

    <div class="termy">
      ```console
      $ pd.Series(mapping_table)
      <span style="color: grey;">stock 1       sector 1
      stock 2       sector 2
      stock 3       sector 3
      stock 4       sector 4
      stock 5       sector 5
                      ...  
      stock 96      sector 6
      stock 97      sector 7
      stock 98      sector 8
      stock 99      sector 9
      stock 100     sector 10
      Length: 100, dtype: object
      </span>
      ```
    </div>

### Step 2: Exposures

The returns time-series (at the sector level) are fit using the `fit()` method, and the portfolio exposures (tilts) are estimated through `estimate()` by summing the individual scores:

<div class="termy">
  ```console
  $ strategy.fit(model_data).estimate(sum)
  <span style="color: yellow;">[Parallel(n_jobs=-1)]:</span>   <span style="color: lime;">1 tasks</span>
  <span style="color: yellow;">[Parallel(n_jobs=-1)]:</span>   <span style="color: lime;">Done 2 out of 2 | elapsed: 0.5s</span>
  <span style="color: yellow;">[Parallel(n_jobs=-1)]:</span>   <span style="color: lime;">Done 2 out of 2 | elapsed: 0.5s finished</span>
  ```
</div>

A summary breakdown and final tilts can be shown using the `breakdown` and `exposure` attributes, respectively.:

=== "Breakdown"
    <div class="termy">
      ```console
      $ strategy.breakdown
      <span style="color: grey;">           Trendfollowing  Reversion
      sector 1                1          1
      sector 2                0          1
      sector 3                0          0
      sector 4               -2          0
      sector 5               -1         -1
      sector 6                2         -2
      sector 7                2         -1
      sector 8               -2          0
      sector 9               -1          0
      sector 10               1          2
      Name: sum, dtype: int64 
      </span>
      ```
    </div>

=== " Exposures"
    <div class="termy">
      ```console
      $ strategy.exposures
      <span style="color: grey;">sector 1     2
      sector 2     1
      sector 3     0
      sector 4    -2
      sector 5    -2
      sector 6     0
      sector 7     1
      sector 8    -2
      sector 9    -1
      sector 10    3
      Name: sum, dtype: int64
      </span>
      ```
    </div>

### Step 3: Portfolio Construction

Let's find individual stocks weights. To accomplish this, you can use the `optimize()` method, which allows us to align the portfolio with your objectives and constraints. 

The modular structure of our code pipeline enables you to choose various optimization techniques, return estimation methods, and risk models within the `portfolio()` method, leveraging open-source capabilities. 

Additionally, we have the ability to incorporate additional constraints using the `add()` method, such as lower and upper bounds for each individual stocks. You can adjust the weightings of you investment strategy in order to achieve a targeted level of volatility and market neutrality, as follow:

```python
# Optimize portfolio
portfolio = (
    strategy
    .optimize(stock_prices) # (1)
    .portfolio(
        model="mvo",
        expected_returns_params=dict(
          method: "capm_return"
        ), # (2)
        cov_matrix_params=dict(
          method="sample_cov"
        ), # (3)
        weight_bounds=(-1, 1) # (4)
    )
    .add(
      constraints=[
        lambda w: w <=  0.1, 
        lambda w: w >= -0.1
      ]
    )
)

# Solver target volatility and market neutrality
portfolio.efficient_risk(target_volatility=0.08, market_neutral=True)
```

1.  Because it is a top-down strategy, we want to optimize your portfolio (finding weights which align with both objectives and constraints) at the stock level.
2.  Mean-variance optimization requires knowledge of the expected returns.
3.  Mean-variance optimization requires a risk model for quantifying asset risk. Commonly, the covariance matrix, which describes asset volatilities and their co-dependence, is used.
4.  Minimum and maximum weight of each asset. When `weight_bounds=(-1, 1)`, it allows for portfolios with shorting.

To finalize the portfolio weightings, we can utilize `clean_weights()`, a utility function provided by the [PyPortfolioOpt library](https://pyportfolioopt.readthedocs.io/en/latest/index.html) and integrated within `Strategy`. This function allows us to filter out any weightings with absolute values below a specified cutoff, setting them to zero, and rounding the remaining entities.

<div class="termy">
  ```console
  $ weights = pd.Series(portfolio.clean_weights(), name="weights")
  <span style="color: grey;">asset 1     -0.09
  stock 2      0.09
  stock 3     -0.08
  stock 4      0.00
  stock 5      0.00

  stock 96     0.06
  stock 97    -0.04
  stock 98     0.00
  stock 99     0.00
  stock 100    0.00
  Name: weights, Length: 100, dtype: float64
  </span>
  ```
</div>

[^1]: Kowalski, M. (1979). Algorithm = logic + control. Communications of the ACM, 22(7), 424-436.
[^2]: Bass, L., Clements, P., & Kazman, R. (2003). Software architecture in practice (2nd ed.). Boston, MA: Addison-Wesley.
[^3]: Rouwenhorst, K. G., & Wu, G. S. (2001). Modular investment strategies. Journal of Financial Economics, 61(3), 369-403.
[^4]: Faber, M., & O'Shaughnessy, J. (2013). Building investment strategies with building blocks. Journal of Portfolio Management, 39(4), 104-115.
[^5]: By default joblib.Parallel uses the 'loky' backend module to start separate Python worker processes to execute tasks concurrently on separate CPUs. More information available in the [Joblib Documentation](https://joblib.readthedocs.io/en/latest/parallel.html).