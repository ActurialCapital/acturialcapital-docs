# Development Guidelines

## Abstract Classes (ABCs)

An abstract class can be considered as a blueprint for other classes. It allows you to create a set of methods that must be created within any child classes built from the abstract class. 

* A class which contains one or more abstract methods is called an abstract class. 
* An abstract method is a method that has a declaration but does not have an implementation. 
* While we are designing large functional units we use an abstract class.
* When we want to provide a common interface for different implementations of a component, we use an abstract class. 

### Why are ABCs useful?

By defining an abstract base class, you can define a common Application Program Interface(API) for a set of subclasses. This capability is especially useful in situations where a third-party is going to provide implementations, such as with plugins, but can also help you when working in a large team or with a large code-base where keeping all classes in your mind is difficult or not possible.

### How ABCs work?

!!! info "ABC"
    Find more information about [abc — Abstract Base Classes](https://docs.python.org/3/library/abc.html).
    
By default, Python does not provide abstract classes. Python comes with a module that provides the base for defining Abstract Base classes (ABC) and that module name is `ABC`. `ABC` works by decorating methods of the base class as abstract and then registering concrete classes as implementations of the abstract base. A method becomes abstract when decorated with the keyword `@abstractmethod`. 

## Base Score Model

In order to ensure consistency and ease of use, all blocks must adhere to the `BaseScoreModel` abstract base class. 

```python
from typing import Dict
import pandas as pd

from abc import ABC, abstractmethod

class BaseScoreModel(ABC):

    @abstractmethod
    def processing(
        self, X: pd.DataFrame, **kwargs) -> "BaseScoreModel":
        self.processing_output: pd.DataFrame
        return self

    @abstractmethod
    def ranking(self) -> "BaseScoreModel":
        self.ranking_output: pd.DataFrame
        return self

    @abstractmethod
    def scoring(self) -> "BaseScoreModel":
        self.scoring_output: pd.DataFrame
        self.strategy_output: Dict
        return self
```

This set of rules allows us to seamlessly integrate blocks and reduces friction. As a result, the code becomes more readable, easier to debug, and simpler to contribute to. It also significantly decreases the amount of code required to access information.

### Step 1: Inheritance

Blocks must inherite from `BaseScoreModel`:

```python hl_lines="3"
from opendesk import BaseScoreModel

class MyBlock(BaseScoreModel)
    ...
```

### Step 2: Initialize Parameters

Parameters should be initialized at the `__init__` section of the class. It also means that model data should **NOT** be passed at this stage (see step 5 below):

```python hl_lines="5 6 7 8"
from opendesk import BaseScoreModel

class MyBlock(BaseScoreModel)
    
        def __init__(self, param_1, param_2, param_n):
            self.param_1 = param_1
            self.param_2 = param_2
            self.param_n = param_n
```

### Step 3: PRS

Blocks must follow the "PRS" schema, which is "Processing" data, "Ranking" results, "Scoring" output. All classes must have the mandatory three functions:

* `processing()`
* `ranking()`
* `scoring()`

```python hl_lines="10 13 16"
from opendesk import BaseScoreModel

class MyBlock(BaseScoreModel)

    def __init__(self, param_1, param_2, param_n):
        self.param_1 = param_1
        self.param_2 = param_2
        self.param_n = param_n
    
    def processing(self)
        ...

    def ranking(self)
        ...

    def scoring(self)
        ...
```

### Step 4: Fluent Interface

Each of the above three functions must return the instance object `self` on which the method was called:

```python hl_lines="10 11 13 14 16 17"
from opendesk import BaseScoreModel

class MyBlock(BaseScoreModel)
    
    def __init__(self, param_1, param_2, param_n):
        self.param_1 = param_1
        self.param_2 = param_2
        self.param_n = param_n
    
    def processing(self) -> "MyBlock":
        return self

    def ranking(self) -> "MyBlock":
        return self

    def scoring(self) -> "MyBlock":
        return self
```

!!! info "Fluent interface"
    In object-oriented programming, returning self from a method allows for the implementation of a fluent interface, where methods can be cascaded i a single statement.

    In object-oriented programming, returning `self` from a method can be useful for several reasons. One common use case is to create a fluent interface, which is an API design style that allows method calls to be chained together in a single statement. This can make the code more readable and concise, as it eliminates the need to create intermediate variables to store the results of intermediate method calls. For example, with a fluent interface, this code could be written as `results = SomeClass().method1().method2().method3()`. 

### Step 5: Processing Parameters

The function `processing()` must accept a market price `pandas.DataFrame` time-series object and can accept additional dataset (within `**kwargs`):

```python hl_lines="1 12"
import pandas as pd

from opendesk import BaseScoreModel

class MyBlock(BaseScoreModel)

    def __init__(self, param_1, param_2, param_n):
        self.param_1 = param_1
        self.param_2 = param_2
        self.param_n = param_n
    
    def processing(self, X: pd.DataFrame, **kwargs) -> "MyBlock":
        return self

    def ranking(self) -> "MyBlock":
        return self

    def scoring(self) -> "MyBlock":
        return self
```

### Step 6: Set Attributes

Functions `processing()`, `ranking()` and `scoring()` must set attributes `processing_output`, `ranking_output` and `scoring_output` as `pandas.DataFrame` object, respectively:

```python hl_lines="13 17 21"
import pandas as pd

from opendesk import BaseScoreModel

class MyBlock(BaseScoreModel)

    def __init__(self, param_1, param_2, param_n):
        self.param_1 = param_1
        self.param_2 = param_2
        self.param_n = param_n
    
    def processing(self, X: pd.DataFrame, **kwargs) -> "MyBlock":
        self.processing_output: pd.DataFrame
        return self

    def ranking(self) -> "MyBlock":
        self.ranking_output: pd.DataFrame
        return self

    def scoring(self) -> "MyBlock":
        self.scoring_output: pd.DataFrame
        return self
```

### Step 7: Strategy Output

The function `scoring()` must set attribute `strategy_output` as dictionnary object, representing the final scores of the model:

```python hl_lines="1 23"
from typing import Dict
import pandas as pd

from opendesk import BaseScoreModel

class MyBlock(BaseScoreModel)

    def __init__(self, param_1, param_2, param_n):
        self.param_1 = param_1
        self.param_2 = param_2
        self.param_n = param_n

    def processing(self, X: pd.DataFrame, **kwargs) -> "MyBlock":
        self.processing_output: pd.DataFrame
        return self

    def ranking(self) -> "MyBlock":
        self.ranking_output: pd.DataFrame
        return self

    def scoring(self) -> "MyBlock":
        self.scoring_output: pd.DataFrame
        self.strategy_output: Dict
        return self
```

