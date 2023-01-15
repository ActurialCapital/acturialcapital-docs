# Create Blocks

Blocks are classes, which follow [Opendesk's Development Guidelines](../contributing/development_guidelines.md), to ensure consistency and ease of use. Blocks must follow the "PRS" schema, which is "Processing" data, "Ranking" results, "Scoring" output. All classes must have the below three functions:

* `processing()`
* `ranking()`
* `scoring()`

## Step-by-Step Example

!!! note "Dependencies"
    For this step-by-step example, we are using the following dependencies:
    ```python
    import pandas as pd
    from sklearn.preprocessing import StandardScaler
    ```

In this example, we want to create a valuation level strategy. This rule-based strategy implies exposing our portfolio to directional bets, depending on both the level of the price-earning ratio (PE) time-series and the cross-sectional comparison between assets.

1. In the **processing function**, we will standardize features by removing the mean and scaling to unit variance. The standard score, $Z$, of a sample $x$ is calculated as $Z = \frac{(x - \mu)}{\sigma}$, where $\mu$ is the mean of the training samples and $\sigma$ is the standard deviation of the training samples.
2. In the **ranking function**, we will rank in ascending order $PEs$.
3. In the **scoring function**, we will map top quantiles from both long and short sides. 

### Step 1: Processing

To process data, we use [the Scikit-Learn `StdandardScaler` transform](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html). In practice, normalizing our time-series is as follow:

```python
def processing(price_earnings: pd.DataFrame):
    processing_output = (
        StandardScaler()
        .set_output(transform="pandas")
        .fit_transform(price_earnings)
    )
    return processing_output
```

Which returns a `pandas.DataFrame` object.

### Step 2: Ranking

Then, the transformed results is ranked using `pandas.rank()` function:

```python
def ranking():
    ranking_output = (
        processing_output
        .iloc[-1]
        .rename("rank")
        .rank(ascending=False)
        .sort_values()
    )
    return ranking_output
```

Which also returns a `pandas.DataFrame` object.

### Step 3: Scoring

Finally, we produce the goal of this base score model is to produce quantile scores, as follow:

```python
def scoring():
    quantiles = pd.qcut(ranking_output, q=5, labels=False)
    scoring_output = quantiles.map(mapping_score)
    return strategy_output
```

### Step 4: Parameters

Last, for reproductibility and ease, we aim to gather parameters in the `__init__` section:

```python
from typing import Dict

from opendesk.blueprints import BaseScoreModel

class MyBlock(BaseScoreModel):

   def __init__(self, mapping_score: Dict)
        self.mapping_score = mapping_score
```

### Step 5: Wrapping Things Up

```python
import pandas as pd
from sklearn.preprocessing import StandardScaler

from opendesk.blueprints import BaseScoreModel

class MyBlock(BaseScoreModel):

   def __init__(self, mapping_score: Dict)
        self.mapping_score = mapping_score
        
    def processing(self, price_earnings: pd.DataFrame) -> "MyBlock":
        self.processing_output = (
            StandardScaler()
            .set_output(transform="pandas")
            .fit_transform(price_earnings)
        )
        return self

    def ranking(self) -> "MyBlock":
        self.ranking_output = (
            processing_output
            .iloc[-1]
            .rename("rank")
            .rank(ascending=False)
            .sort_values()
        )
        return self

    def scoring(self) -> "MyBlock":
        quantiles = pd.qcut(ranking_output, q=5, labels=False)
        self.scoring_output = quantiles.map(mapping_score)
        self.strategy_output = scoring_output.to_dict()
        return self
```
