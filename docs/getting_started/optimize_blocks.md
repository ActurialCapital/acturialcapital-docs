# Optimize Blocks

Portfolio optimization is the process of selecting the optimal mix of assets in a portfolio in order to maximize returns while minimizing risk. One method of portfolio optimization involves the use of alpha scores. By selecting assets with high alpha scores, an investor can potentially achieve higher probability of positive returns while taking on the same level of risk. The optimal portfolio is typically determined through the use of statistical analysis and optimization techniques, such as mean-variance or Black-Litterman optimization.

Portfolio optimization using alpha scores has been studied extensively in the financial literature. For example, in a study published in the Journal of Financial Economics[^1], Chen, Novy-Marx, and Zhang (2013) found that portfolios constructed using alpha scores significantly outperformed traditional market-cap weighted portfolios. Another study by Geczy, Musto, and Reed (2005) published in the Review of Financial Studies[^2] found that portfolios constructed using alpha scores had higher Sharpe ratios (a measure of risk-adjusted returns) compared to traditional portfolios.

!!! note "Black-Litterman Approach"
     This method is also particularly useful in the Black-Litterman 
    (BL) model, which is a Bayesian approach to asset allocation. The BL model combines an initial estimate of returns with specific views on certain assets to generate a revised estimate of expected returns. This revised estimate, known as the posterior estimate, is then used to optimize the allocation of assets in accordance with a predetermined set of objectives (e.g., maximizing Sharpe ratio) and constraints.

!!! warning "Risk Considerations"
    It's important to note that while alpha scores can be useful for portfolio optimization, they are not a guarantee of success. Like any investment strategy, portfolio optimization using alpha scores carries its own set of risks and uncertainties. It is always important for investors to carefully consider their investment goals and risk tolerance before making any investment decisions.

## Key Takeaways

An interval query is used to assign scores to elements in a portfolio. These scores are then used to determine the relative weightings of the highest and lowest elements in the portfolio. The resulting portfolio reflects these weightings and adheres to any specified objectives and constraints.

``` mermaid
flowchart TD
    B[Strategy] --> E[Exposures];
    E -- Default Config --> M[Map]
    E -- Custom Config --> M
    M -- Bound Constraints --> P[Portfolio]
    P -- Objectives --> O[Optimizer]
    P -- Constraints --> O
    O -- Weights --> P
    P -. Analysis .-> Backtests
```
!!! question "Why Are We Not Creating Weights in the First Place?"

    There are a few reasons why you might calculate scores rather than directly assigning "weights" to potential assets in their investment portfolios. 
    
    One reason is that scores can be used to rank potential investments relative to one another, while weights are typically used to indicate the proportion of your total portfolio that should be allocated to a particular asset. This means that scores can be used to identify which assets are the most attractive candidates for inclusion in a portfolio, while weights are used to determine how much of an your capital should be allocated to each asset. 
    
    Another reason is that scores can be based on a variety of different factors and criteria, whereas weights are usually based on a single factor (e.g., expected return or risk). By using scores, you can take a more holistic view of potential investments and consider multiple factors when making decisions. 
    
    Furthermore, the allocation of weights in the portfolio would not consider the overall goals and limitations of the portfolio. It would only be based on the weights where alpha scores have been calculated, such as at the sector level.

## Step-by-Step Example

Portfolio construction, which involves optimizing the allocation of assets within a portfolio, can be a complex and nuanced process. We have developed a method that allows for greater flexibility and experimentation in the portfolio optimization process. This approach enables the exploration of a wide range of potential portfolio compositions, and the example provided illustrates this method applied from the initial stages of portfolio construction.

```py
from opendesk import Strategy
from opendesk.blocks import Reversion

strategy = Strategy([("reversion", Reversion)]).fit(df).estimate(sum) # (1)

```

1.  Calculate sentiment using Reversion Ranking Method.
    More information provided in the [Model Glossary](/pro_version/model_glossary/sentiment/reversion_models).

### Step 1: Portfolio

We aim to find weights for a large universe of stocks:

```python
strategy.portfolio(data=stock_prices) # (1)
```

1.  pandas.DataFrame object, with specifiy the variation of stock prices over time.

### Step 2: Optimize

The wrapper class inherite from `PortfolioConstruction`, which adds the `optimize` public method to your toolbox, allowing for the efficient computation of optimized asset weights. Constraints are lambda functions (e.i. all assets must be lower or equal to 10% of the total portfolio would simply translate to `[lambda w: w <= .1]`. This constraint must satisfy DCP rules, i.e be either a linear equality constraint or convex inequality constraint. Here is an example with "min_volatility", which finds the minimum risk portfolio:

```python
weights = strategy.optimize(  
    weight_bounds=(-1, 1), # (1)
    target="min_volatility",
    constraints=[lambda w: w <= .1]
)
```

1. `weight_bounds` parameter serves as a constraint by limiting the minimum and maximum weight of each asset in portfolios. Because it ranges from `-1` to `1`, it allows Long and Shorts.

<div class="termy">
  ```console
  $ pd.Series(weights, name="weights")
  <span style="color: grey;">asset 1      0.10
  asset 2      0.03
  asset 3     -0.02
  asset 4      0.03
  asset 5     -0.05

  asset 96     0.00
  asset 97    -0.09
  asset 98     0.00
  asset 99    -0.07
  asset 100    0.00
  Name: weights, Length: 100, dtype: float64
  </span>
  ```
</div>

[^1]: Chen, L., Novy-Marx, R., & Zhang, L. (2013). Factor Premia and Interaction with the Market Portfolio. Journal of Financial Economics, 110(1), 1-35.
[^2]: Geczy, C., Musto, D., & Reed, A. (2005). A simple approach to performance attribution for hedge funds: the case of equity market neutral strategies. Review of Financial Studies, 18(2), 367-384.
