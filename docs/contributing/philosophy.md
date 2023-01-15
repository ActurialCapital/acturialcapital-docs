# Philosophy

The engineering and research process in the context of investment strategies involves several key stages.

1. **Research phase**: In this phase, the focus is on gathering and analyzing data to inform the development of a quantitative model or algorithm. This involves collecting data from various sources, such as financial statements, market trends, and economic indicators, and using statistical and mathematical techniques to analyze and interpret the data.
2. **Strategy implementation**: Once the research phase is complete, the next step is to implement the quantitative model or algorithm. 
3. **Model Optimization**: After the initial implementation of the quantitative model, it is important to continuously review and optimize it to ensure that it is accurately predicting outcomes and providing valuable insights. This may involve 
adjusting the model's parameters or adding additional data sources to improve its performance.
4. **Backtesting**: Before implementing a quantitative model in a live trading environment, it is often useful to backtest it to see how it would have performed in the past. This can help to identify any potential weaknesses or areas for improvement in the model.
5. **Target and invested portfolio**: In the context of quantitative analysis, the target portfolio refers to the desired mix of investments that the model is intended to identify and select, while the invested portfolio refers to the actual mix of investments that has been chosen based on the model's predictions.
6. **Risk monitoring**: It is important to continuously monitor the portfolio for any potential risks and to take appropriate action to mitigate those risks. This involves regularly reviewing the portfolio and making adjustments as needed, as well as staying informed about market trends and economic conditions.

``` mermaid
sequenceDiagram
  Database->>Blocks: Feed Strategy
  Blocks->>Portfolio: Compute Exposures
  Portfolio->>Backtest: Weights
  loop Objectives & Constraints
        Backtest->>Portfolio: Performance
    end
```

Overall, the engineering and research process in the context of quantitative analysis involves a systematic approach to collecting and analyzing data, developing and implementing a quantitative model or algorithm, and continuously reviewing and optimizing it to ensure that it is providing accurate and valuable insights.