# Discrete Allocation

## discrete_allocation

```python
PortfolioConstruction.discrete_allocation(
    model: str,
    model_params: Dict[str, Any] = None,
    range_bound: Optional[str] = 'mid'
) -> opendesk.portfolio.PortfolioConstruction
```

Discrete allocation is a method for implementing predefined, rule-based allocation strategies for building optimal, diversified portfolios at scale. The use of the `topdown` parameter, when set to `True`, can introduce additional complexity in the portfolio construction process, as it involves diversifying allocation across a wide range of assets, using techniques such as uniform or market cap-based allocation. The `PortfolioConstruction.discrete_allocation()` function calls the `DiscreteAllocation` class (only when `topdown=True`), which offers a variety of rule-based weighting schemes. These weighting schemes, which are commonly used to construct factor portfolios, are designed to achieve a range of portfolio objectives.

### Parameters

``` markdown title="model"
Optional[str] = "equal_weighted"
```
<div class="result" markdown>
Model used to allocate weights. Possible methods are:

* `equal_weighted`: Asset equally weighted
* `market_cap_weighted`: Asset weighted in proportion to their free-float market cap
* `score_weighted`: Asset weighted in proportion to their target-factor scores
* `score_tilt_weighted`: Asset weighted in proportion to the product of their market cap and factor score
* `inv_volatility_weighted`: Asset weighted in proportion to the inverse of their historical volatility
* `min_correlation_weighted`: Optimized weighting scheme to obtain a portfolio with minimum volatility under the assumption that all asset have identical volatilities

Defaults to `equal_weighted`.
</div>

``` markdown title="model_params"
Dict[str, Any] = None
```
<div class="result" markdown>
Model specific parameters.
</div>

``` markdown title="range_bound"
Optional[str] = "mid"
```
<div class="result" markdown>
Weight bound (from `mapping_weights`). Total budget (in %) to apply. Possible values are:

* `lower`: Lower weight bound
* `mid`: Median weight
* `upper`: Upper weight bound

Defaults to `mid`.
</div>

### Returns

opendesk.portfolio.PortfolioConstruction instance.

## equal_weighted

```python
PortfolioConstruction.equal_weighted_model() ‑> Dict[str, float]
```

Asset equally weighted.

### Returns

`Dict[str, float]`, weights.

## inv_volatility_weighted

```python
PortfolioConstruction.inverse_volatility_weighted_model() ‑> Dict[str, float]
```

Asset weighted in proportion to the inverse of their historical volatility.

### Returns

`Dict[str, float]`, weights.

## market_cap_weighted

```python
PortfolioConstruction.market_cap_weighted_model(
    market_caps: pandas.core.series.Series
) ‑> Dict[str, float]
```

Asset weighted in proportion to their free-float market cap

### Parameters

``` markdown title="market_caps"
pandas.core.series.Series
```
<div class="result" markdown>
Asset free-float market cap.
</div>

### Returns

`Dict[str, float]`, weights.

## min_correlation_weighted

```python
PortfolioConstruction.minimum_correlation_weighted_model(
    l1_regularization: Optional[bool] = False
) ‑> Dict[str, float]
```

Optimized weighting scheme to obtain a portfolio with minimum volatility under the assumption that all asset have identical volatilities.

### Parameters

``` markdown title="l1_regularization"
Optional[bool] = False
```
<div class="result" markdown>
Sparse inverse covariance w/ cross-validated choice of the l1 penalty. Scikit-Learn `GraphicalLassoCV` is used to filter unsignificant features out.
</div>

### Returns

`Dict[str, float]`, weights.

## score_tilt_weighted

```python
PortfolioConstruction.score_tilt_weighted_model(
    scores: pandas.core.series.Series, 
    market_caps: pandas.core.series.Series
) ‑> Dict[str, float]
```

Asset weighted in proportion to the product of their market cap and factor score. Scikit_Learn `MinMaxScaler` is used to rescale the data set such that all feature values are in the range [0, 1].

### Parameters

``` markdown title="scores"
pandas.core.series.Series
```
<div class="result" markdown>
Asset scores. Scikit-Learn `MinMaxScaler` is used to rescale the data set such that all feature values are in the range [0, 1].
</div>

``` markdown title="market_caps"
pandas.core.series.Series
```
<div class="result" markdown>
Asset free-float market cap.
</div>

### Returns

`Dict[str, float]`, weights.

## score_weighted

```python
PortfolioConstruction.score_weighted_model(
    scores: pandas.core.series.Series, 
) ‑> Dict[str, float]
```
Asset weighted in proportion to their target-factor scores.

### Parameters

``` markdown title="scores"
pandas.core.series.Series
```
<div class="result" markdown>
Asset scores. Scikit-Learn `MinMaxScaler` is used to rescale the data set such that all feature values are in the range [0, 1].
</div>

### Returns

`Dict[str, float]`, weights.

## clean_weights

```python
PortfolioConstruction.clean_weights(
    cutoff: Optional[float] = 0.0001, 
    rounding: Optional[int] = 5
) ‑> OrderedDict
```

Helper method to clean the raw weights, setting any weights whose absolute values are below the cutoff to zero, and rounding the rest.

### Parameters

``` markdown title="cutoff"
Optional[float] = 0.0001
```
<div class="result" markdown>
The lower bound, defaults to 1e-4
</div>

``` markdown title="rounding"
Optional[int] = 5
```
<div class="result" markdown>
Number of decimal places to round the weights, defaults to 5. Set to None if rounding is not desired.
</div>

### Returns

`OrderedDict`, asset weights.