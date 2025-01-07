# 如何轻松使用Pandera验证数据

> 原文：[https://towardsdatascience.com/how-to-easily-validate-your-data-with-pandera-a9cd22c515a5?source=collection_archive---------7-----------------------#2024-08-14](https://towardsdatascience.com/how-to-easily-validate-your-data-with-pandera-a9cd22c515a5?source=collection_archive---------7-----------------------#2024-08-14)

## 学习如何构建一个简单的数据模型，通过类型提示来验证数据

[](https://medium.com/@conalhenderson?source=post_page---byline--a9cd22c515a5--------------------------------)[![Conal Henderson](../Images/e10624264efc3861a2384493fa5ee55a.png)](https://medium.com/@conalhenderson?source=post_page---byline--a9cd22c515a5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a9cd22c515a5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a9cd22c515a5--------------------------------) [Conal Henderson](https://medium.com/@conalhenderson?source=post_page---byline--a9cd22c515a5--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a9cd22c515a5--------------------------------) ·阅读时间：6分钟·2024年8月14日

--

![](../Images/9cb51cb89dabc5e3cfd2ce6056273fff.png)

图片来自[Christina Morillo](https://www.pexels.com/@divinetechygirl/)在[Pixels](https://www.pexels.com/photo/man-standing-infront-of-white-board-1181345/)

许多网上的`pandas`教程教授如何操作和清理数据，但很少有教程展示如何验证数据是否正确。这就是使用`pandera`进行数据验证的意义所在。

# 为什么需要数据验证？

就像任何人一样，当我第一次查看数据时，我会进行基本的调查，比如查看数据类型、检查空值，并可视化数据分布，来大致判断如何处理数据。

然而，我们需要数据验证来确保数据遵循业务逻辑。例如，对于包含产品信息的数据，我们需要验证产品价格没有负值；或者当用户提供电子邮件地址时，我们需要验证该电子邮件地址符合已知的模式。

忽视数据验证会对分析和建模产生下游影响，因为数据质量差会导致**偏差、噪声和不准确性增加**。

最近一个数据验证不当的例子是[Zillow的房价算法](https://insideainews.com/2021/12/13/the-500mm-debacle-at-zillow-offers-what-went-wrong-with-the-ai-models/)，其高估了Zillow所购买的2/3的房产，导致2021年第三季度和第四季度Zillow房产估值下降了5亿美元。

这表明，你不仅需要关注数据是否符合验证标准，还需要关注数据是否反映了现实情况，而在Zillow的案例中，数据并未做到这一点。

# 什么是Pandera？

`[Pandera](https://pandera.readthedocs.io/en/stable/index.html)`是一个Python包，提供了一个文档齐全且灵活的API，能够与`pandas`和`polars`这两个主要的Python数据库进行集成。

我们可以使用`pandera`通过**业务逻辑和领域知识**来验证数据框架的数据类型和属性。

# 大纲

本文将覆盖以下内容：

+   如何开始使用`pandera`

+   如何定义`pandera`数据模型

+   如何验证数据并处理错误

# 设置

## 安装依赖

```py
pip install pandas
pip install pandera
```

## 数据

本文使用的数据是通过Claude.ai生成的假足球市场数据。

# 定义验证模型

该包允许你定义验证模式或数据验证模型，后者与另一个很棒的数据验证包`[Pydantic](https://docs.pydantic.dev/latest/)`非常相似。

对于本次练习，我们将专注于验证模型，因为它允许**与我们的Python代码集成类型提示**，并且我发现它比验证模式稍微更易于阅读。然而，如果你想利用验证模式，模型也有方法将其转换为模式。

你可以在这里找到关于两种验证方法的信息：

+   [DataFrameSchema](https://pandera.readthedocs.io/en/stable/dataframe_schemas.html)

+   [DataFrameModel](https://pandera.readthedocs.io/en/stable/dataframe_models.html)

## 加载数据

```py
data = {
    'dob': pd.to_datetime(['1990-05-15', '1988-11-22', '1995-03-10', '1993-07-30', '1992-01-18', '1994-09-05', '1991-12-03', '1989-06-20', '1996-02-14', '1987-08-08']),
    'age': [34, 35, 29, 31, 32, 30, 32, 35, 28, 37],
    'country': ['England', 'Spain', 'Germany', 'France', 'Italy', 'Brazil', 'Argentina', 'Netherlands', 'Portugal', 'England'],
    'current_club': ['Manchester United', 'Chelsea', 'Bayern Munich', 'Paris Saint-Germain', 'Juventus', 'Liverpool', 'Barcelona', 'Ajax', 'Benfica', 'Real Madrid'],
    'height': pd.array([185, 178, None, 176, 188, 182, 170, None, 179, 300], dtype='Int16'),
    'name': ['John Smith', 'Carlos Rodriguez', 'Hans Mueller', 'Pierre Dubois', 'Marco Rossi', 'Felipe Santos', 'Diego Fernandez', 'Jan de Jong', 'Rui Silva', 'Gavin Harris'],
    'position': ['Forward', 'Midfielder', 'Defender', 'Goalkeeper', 'Defender', 'Forward', 'Midfielder', 'Defender', 'Forward', 'Midfielder'],
    'value_euro_m': [75.5, 90.2, 55.8, 40.0, 62.3, 88.7, 70.1, 35.5, 45.9, 95.0],
    'joined_date': pd.to_datetime(['2018-07-01', '2015-08-15', None, '2017-06-30', '2016-09-01', None, '2021-07-15', '2014-08-01', '2022-01-05', '2019-06-01']),
    'number': [9, 10, 4, 1, 3, 11, 8, 5, 7, 17],
    'signed_from': ['Everton', 'Atletico Madrid', 'Borussia Dortmund', None, 'AC Milan', 'Santos', None, 'PSV Eindhoven', 'Sporting CP', 'Newcastle United'],
    'signing_fee_euro_m': [65.0, 80.5, 45.0, None, 55.0, 75.2, 60.8, None, 40.5, 85.0],
    'foot': ['right', 'left', 'right', 'both', 'right', 'left', 'left', 'right', 'both', 'right'],
}

df = pd.DataFrame(data)
```

![](../Images/e3e1bc6302c113c4e0752e131a944a58.png)

图片来自作者

## 检查数据类型

```py
data.types
```

![](../Images/6e878c39c8dda42332ac60e5846fe134.png)

图片来自作者

我们可以使用数据类型来帮助定义我们的数据模型。

## 创建数据模型

```py
class PlayerSchema(pa.DataFrameModel):

    dob: Series[pd.Timestamp] = pa.Field(nullable=False, ge=pd.Timestamp('1975-01-01'))
    age: Series[pa.Int64] = pa.Field(ge=0, le=50, nullable=False)
    country: Series[pa.String] = pa.Field(nullable=False)
    current_club: Series[pa.String] = pa.Field(nullable=False)
    height: Series[pa.Int16] = pa.Field(ge=120, le=210, nullable=True)
    name: Series[pa.String] = pa.Field(nullable=False)
    position: Series[pa.String] = pa.Field(nullable=False)
    value_euro_m: Series[pa.Float64] = pa.Field(ge=0, le=200)
    joined_date: Series[pd.Timestamp] = pa.Field(nullable=True, ge=pd.Timestamp('2000-01-01'))
    number: Series[pa.Int64] = pa.Field(ge=0, le=99)
    signed_from: Series[pa.String] = pa.Field(nullable=True)
    signing_fee_euro_m: Series[pa.Float64] = pa.Field(ge=0, le=300, nullable=True)
    foot: Series[pa.String] = pa.Field(nullable=False, isin=['right', 'left', 'both', 'unknown'])
```

上面，我们通过子类化`pa.DataFrameModel`定义了一个模式，这与在`[Pydantic](https://docs.pydantic.dev/latest/)`中子类化`BaseModel`的方式相同。然后，我们用相应的**数据集中的列**填充了该模式，提供了每列的**预期数据类型**，并使用`pa.Field`方法**定义了边界**。

`Pandera`与`pandas`的集成非常好，意味着你可以使用`pandas`数据类型（例如`pd.Timestamp`）以及pandera数据类型（例如`pa.Int64`）来定义每一列。

## 重用字段

为了避免重复字段，我们可以通过使用内建的Python库`functools`中的`partial`来重用字段。

```py
from functools import partial

NullableField = partial(pa.Field, nullable=True)
NotNullableField = partial(pa.Field, nullable=False)
```

`partial`类创建了一个**应用原始函数指定子集参数的新函数**。

上面，我们为模型变量创建了两个可重用字段，这些变量要么包含空值，要么不包含空值。

我们更新后的数据模型如下所示

```py
class PlayerSchema(pa.DataFrameModel):

    dob: Series[pd.Timestamp] = pa.Field(nullable=False, ge=pd.Timestamp('1975-01-01'))
    age: Series[pa.Int64] = pa.Field(ge=0, le=50, nullable=False)
    country: Series[pa.String] = NotNullableField()
    current_club: Series[pa.String] = NotNullableField()
    height: Series[pa.Int16] = pa.Field(ge=120, le=250, nullable=True)
    name: Series[pa.String] = NotNullableField()
    position: Series[pa.String] = NotNullableField()
    value_euro_m: Series[pa.Float64] = pa.Field(ge=0, le=200)
    joined_date: Series[pd.Timestamp] = pa.Field(nullable=True, ge=pd.Timestamp('2000-01-01'))
    number: Series[pa.Int64] = pa.Field(ge=0, le=99)
    signed_from: Series[pa.String] = NullableField() 
    signing_fee_euro_m: Series[pa.Float64] = pa.Field(ge=0, le=300, nullable=True)
    foot: Series[pa.String] = pa.Field(nullable=False, isin=['right', 'left', 'both', 'unknown'])
```

由于`partial`创建了一个新函数，我们必须使用括号来调用我们的新字段。

# 验证数据

现在数据模型已经定义好，我们可以用它来验证数据。

```py
@pa.check_types
def load_data() -> DataFrame[PlayerSchema]:
    return pd.read_parquet('../data/player_info_cleaned.parquet')
```

为了验证数据，我们使用**类型提示和装饰器的结合**。`@pa.check_types`装饰器表示数据应该按照返回类型提示`DataFrame[PlayerSchema]`中定义的模式进行验证。

```py
@pa.check_types
def validate_data(df: DataFrame) -> DataFrame[PlayerSchema]:
    try:
        return df
    except pa.errors.SchemaError as e:
        print(e)

validate_data(df)
# error in check_types decorator of function 'load_data': Column 'height' failed element-wise validator number 1: less_than_or_equal_to(210) failure cases: 300
```

通过使用try-except块，我们可以**捕获加载和验证数据时抛出的任何错误**。结果显示，‘height’列**未通过小于或等于测试**，其中一个标记为300cm的身高是不正确的。

## 清理数据

清理数据有很多策略，其中一些我在之前的文章中已详细介绍。

[](https://medium.com/@conalhenderson/master-pandas-to-build-modular-and-reusable-data-pipelines-1d12b003a423?source=post_page-----a9cd22c515a5--------------------------------) [## 精通Pandas构建模块化和可重用的数据管道

### 通过利用pandas实现关键的数据处理策略，构建模块化、可重用且高效的数据管道。

medium.com](https://medium.com/@conalhenderson/master-pandas-to-build-modular-and-reusable-data-pipelines-1d12b003a423?source=post_page-----a9cd22c515a5--------------------------------)

为了简化处理，我将使用人口的中位数身高来填补所有大于210cm的值。

```py
def clean_height(df: DataFrame) -> DataFrame[PlayerSchema]:
    data = df.copy()
    data.loc[data[PlayerSchema.height] > 210, PlayerSchema.height] = round(data[PlayerSchema.height].median())
    return data

df = clean_height(df) 
```

`pandera`的另一个有用特性是，架构可以用来**指定你想要分析的列名**，例如，我们可以使用`PlayerSchema.height`来**提高代码的可读性并鼓励一致性**，而不是显式地标记‘height’。

## 重新验证

由于我们的数据已经被加载进来，我们可以调整之前的函数，使其接受数据框作为输入，运行数据框并通过try-except块进行评估，并使用装饰器和类型提示。

```py
validate_data_2(df)

# if there are no errors, the dataframe is the output. 
```

这次没有错误，意味着数据框被作为函数的输出返回。

# 结论

这篇文章概述了为什么验证数据非常重要，以确保数据**与业务逻辑一致并反映现实世界**，以Zillow为例，说明了缺乏数据验证可能带来的严重后果。

使用了`Pandera`来展示如何轻松地将数据验证与`pandas`集成，以快速**验证一个架构**，该架构与`Pydantic`非常相似，使用了**装饰器**和**类型提示**。还展示了如何使用`pa.Field`来**设置数据的边界**，并且当与`partial`一起使用时，可以创建可重用字段，从而提高代码的可读性。

希望你觉得这篇文章有用，感谢阅读。如果有任何问题，可以通过[LinkedIn](https://www.linkedin.com/in/conal-henderson-4128631b6/)与我联系！
