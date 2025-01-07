# 直观的时间序列数据框过滤

> 原文：[https://towardsdatascience.com/intuitive-temporal-dataframe-filtration-fa9d5da734b3?source=collection_archive---------8-----------------------#2024-05-27](https://towardsdatascience.com/intuitive-temporal-dataframe-filtration-fa9d5da734b3?source=collection_archive---------8-----------------------#2024-05-27)

## 摆脱你那无效的时间序列数据过滤代码

[](https://namiyousef96.medium.com/?source=post_page---byline--fa9d5da734b3--------------------------------)[![Yousef Nami](../Images/09a0baa3fe20c858ace5b7923b7c753a.png)](https://namiyousef96.medium.com/?source=post_page---byline--fa9d5da734b3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fa9d5da734b3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fa9d5da734b3--------------------------------) [Yousef Nami](https://namiyousef96.medium.com/?source=post_page---byline--fa9d5da734b3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fa9d5da734b3--------------------------------) ·阅读时间10分钟·2024年5月27日

--

![](../Images/1bdedd7a197a2764d9cb5c875fee21d4.png)

图片来源：[Carlos Muza](https://unsplash.com/@kmuza) 供职于[Unsplash](https://unsplash.com/photos/laptop-computer-on-glass-top-table-hpjSkU2UYSU)

每次我处理时间序列数据时，我都会写出复杂且不可重用的代码来进行过滤。无论是进行简单的过滤，如移除周末，还是更复杂的操作，如移除特定的时间段，我总是依赖编写一个简便且不太优雅的函数，这个函数仅适用于当前要过滤的内容，但再也不会使用。

我最终决定通过编写一个处理器来打破这个恶性循环，该处理器可以让我用非常简单且简洁的输入过滤时间序列，无论条件多么复杂。

下面是一个它在实践中如何工作的示例：

+   在工作日，我想移除早于6点和晚于8点的时间段，而在周末，我想移除早于8点和晚于10点的时间段。

```py
 df = pl.DataFrame(
   {"date": [
      # -- may 24th is a Friday, weekday
      '2024-05-24 00:00:00',  # < 6 am, should remove
      '2024-05-24 06:00:00',  # not < 6 am, should keep
      '2024-05-24 06:30:00',  # not < 6 am, should keep
      '2024-05-24 20:00:00',  # >= 8 pm, should remove

      # -- may 25th is a Saturday, weekend
      '2024-05-25 00:00:00',  # < 8 am, should remove
      '2024-05-25 06:00:00',  # < 8 am, should remove
      '2024-05-25 06:30:00',  # < 8 am, should remove
      '2024-05-25 20:00:00',  # not >= 10 pm, should keep

      ]
   }
).with_columns(pl.col("date").str.strptime(pl.Datetime, "%Y-%m-%d %H:%M:%S"))
```

+   没有处理器：表达性强，但冗长且不可重用

```py
df.filter(   
    pl.Expr.not_(
        (
            (pl.col("date").dt.weekday() < 6)
            .and_(
                (pl.col("date").dt.hour() < 6)
                .or_(pl.col("date").dt.hour() >= 20)
            )
        )
        .or_(
            (pl.col("date").dt.weekday() >= 6)
            .and_(
                (pl.col("date").dt.hour() < 8)
                .or_(pl.col("date").dt.hour() >= 22)
            )
        )
    )
)
```

+   有处理器：同样具有表达性，简洁且可重用

```py
processor = FilterDataBasedOnTime(
    "date", time_patterns=[
        "<6wd<6h",
        "<6wd>=20h",
        ">=6wd<8h",
        ">=6wd>=22h",
    ]
)
processor.transform(df)
```

在本文中，我将解释我是如何想出这个解决方案的，首先从我选择的字符串格式开始，用于定义过滤条件，然后设计处理器本身。在文章的最后部分，我将描述如何将这个处理流程与其他流程一起使用，以便只通过几行代码实现复杂的时间序列处理。

如果你只对代码感兴趣，可以跳到文章末尾获取链接，查看代码库。

# 富有表现力、简洁且灵活的时间条件？

这是这个任务中最难的部分。基于时间过滤时间序列在概念上很容易，但用代码实现却要困难得多。我的最初想法是使用一个对我自己来说最直观的字符串模式：

```py
# -- remove values between 6 am (inclusive) and 2 pm (exclusive)
pattern = '>=06:00,<14:00'
```

然而，使用这种方法，我们立即遇到了一个问题：我们失去了灵活性。这是因为`06:00`是模糊的，它可能表示`分钟:秒`或`小时:分钟`。因此，我们几乎总是必须事先定义日期格式。

这使得我们无法实现复杂的过滤技术，例如在特定的日期过滤特定的时间（例如，只在周六的`[6am, 2pm)`之间移除值）。

将我的模式扩展为类似[cron](https://crontab.guru/)的形式也没有帮助：

```py
# cronlike pattern
pattern = ‘>=X-X-X 06:00:X, <X-X-X 20:00:X’
```

上述方法可以帮助选择特定的月份或年份，但无法提供像星期几这样的灵活性。此外，使用所有的X符号使得它不太具有表现力，而且非常冗长。

我知道我需要一种允许链式连接单独时间序列组件或单位的模式。实际上，这种模式就像一个if语句：

+   如果日期 == 星期六

+   且时间 ≥ 06:00

+   且时间 < 14:00

所以我想，为什么不使用一种可以为时间组件添加任何条件的模式，并隐含假设它们都是AND条件呢？

```py
# -- remove values in [6am, 2pm) on Saturday
pattern = 'day==6,time>=06:00,time<14:00'
```

现在我们有了一个富有表现力的模式，但它仍然可能存在歧义，因为`time`隐含假设了日期格式。因此，我决定进一步深入：

```py
# -- remove values in [6am, 2pm) on Saturday
pattern = 'day==6,hour>=6,hour<14'
```

现在为了减少冗余，我借用了Polars的持续时间字符串[格式](https://docs.pola.rs/py-polars/html/reference/dataframe/api/polars.DataFrame.group_by_dynamic.html)（如果你更熟悉Pandas的话，这等同于“频率”），于是就有了这个：

```py
# -- remove values in [6am, 2pm) on Saturday
pattern = '==6wd,>=6h,<14h'
```

## 那么，关于需要OR运算符的时间条件呢？

让我们考虑一个不同的条件：过滤所有小于6 am（包含）且大于2 pm（不包含）的时间。如下的模式会失败：

```py
# -- remove values in (-inf, 6am], and (2pm, inf)
pattern = '<=6h,>14h'
```

因为我们会将其读作：≤ 6 am 且 > 2 pm

没有满足这两个条件的值存在！

但解决方案很简单：在一个模式内应用AND条件，在不同模式之间应用OR条件。所以：

```py
# -- remove values in (-inf, 6am], and (2pm, inf)
patterns = ['<=6h', '>14h']
```

这将被解读为：≤ 6 am 或 > 2 pm

## 为什么不允许在模式内使用OR语句呢？

我确实考虑过在模式内添加支持OR语句，例如使用`|`，或者让`,`表示“左”条件和“右”条件之间的区别。然而，我发现这些会增加解析模式的复杂性，却并没有使代码更加简洁。

我更喜欢简单：在一个模式内我们应用AND，在不同模式之间我们应用OR。

## 边缘案例

这里有一个值得讨论的边缘案例。类似“if语句”的模式并不总是有效。

让我们考虑过滤大于`06:00`的时间戳。如果我们仅定义：

```py
# -- pattern to remove values > 06:00
pattern = '>6h'
```

那么，我们是否应该这样解读：

+   移除所有`hour>6`的值

+   或者移除所有`time>06:00`的值？

后者更有意义，但当前的模式无法表达这一点。因此，为了明确说明我们希望包括大于当天第六个小时的时间戳，我们必须添加我所称的级联操作符：

```py
# -- pattern to remove values > 06:00
pattern = '>6h*'
```

它将被解读为：

+   hour > 6

+   OR (hour == 6 AND any(minute, second, millisecond, etc… > 0)

这将是一个准确的条件，用来捕获时间>06:00！

# 代码

在这里，我突出了设计中的重要部分，用于创建一个处理器来过滤时间序列数据。

## 解析逻辑

由于模式非常简单，解析它其实非常容易。我们需要做的就是遍历每个模式，并跟踪操作符字符。剩下的就是操作符列表和它们应用的持续时间列表。

```py
# -- code for parsing a time pattern, e.g. "==7d<7h"
pattern = pattern.replace(" ", "")
operator = ""
operators = []
duration_string = ""
duration_strings = []
for char in pattern:
    if char in {">", "<", "=", "!"}:
        operator += char
        if duration_string:
            duration_strings.append(duration_string)
            duration_string = ""
    else:
        duration_string += char
        if operator:
            operators.append(operator)
            operator = ""
duration_strings.append(duration_string)
```

现在，对于每个操作符和持续时间字符串，我们可以提取有助于我们稍后制定实际布尔规则的元数据。

```py
# -- code for extracting metadata from a parsed pattern

# -- mapping to convert each operator to the Polars method
OPERATOR_TO_POLARS_METHOD_MAPPING = {
    "==": "eq",
    "!=": "ne",
    "<=": "le",
    "<": "lt",
    ">": "gt",
    ">=": "ge",
}

operator_method = (
    OPERATOR_TO_POLARS_METHOD_MAPPING[operator]
)

# -- identify cascade operations
if duration_string.endswith("*"):
    duration_string = duration_string[:-1]
    how = "cascade"
else:
    how = "simple"

# -- extract a polars duration, e.g. 7d7h into it's components: [(7, "d"), (7, "h")]
polars_duration = PolarsDuration(duration=duration_string)
decomposed_duration = polars_duration.decomposed_duration

# -- ensure that cascade operator only applied to durations that accept it
if how == "cascade" and any(
    unit not in POLARS_DURATIONS_TO_IMMEDIATE_CHILD_MAPPING
    for _, unit in decomposed_duration
):
    raise ValueError(
        (
            "You requested a cascade condition on an invalid "
            "duration. Durations supporting cascade: "
            f"{list(POLARS_DURATIONS_TO_IMMEDIATE_CHILD_MAPPING.keys())}"
        )
    )

rule_metadata = {
    "operator": operator_method,
    "decomposed_duration": decomposed_duration,
    "how": how,
}
```

现在，对于每个模式，我们都有了字典来定义它的每个组件的规则。因此，如果我们选择一个复杂的例子：

```py
pattern = '==1m>6d6h' # remove if month = Jan, and day > 6 and hour > 6

# parsed pattern

[
   [
      {
         "operator": "eq",
         "decomposed_duration": [(1, "m")],
         "how": "simple"
      },
      { 
         "operator": "gt",
         "decomposed_duration": [(6, "d"), (6, "h")],
         "how": "simple"
      }
   ]
]
```

注意，一个模式可以被拆分成多个元数据字典，因为它可以由多个持续时间和操作组成。

## 从元数据创建规则

创建了每个模式的元数据后，现在进入有趣的部分——创建Polars规则！

请记住，在每个模式内部，我们应用的是AND条件，但在不同模式之间，我们应用的是OR条件。所以在最简单的情况下，我们需要一个包装器，它可以接受特定模式的所有元数据列表，然后对其应用AND条件。我们可以将这个表达式存储在一个列表中，和所有其他模式的表达式一起，之后再应用OR条件。

```py
# -- dictionary to contain each unit along with the polars method to extract it's value
UNIT_TO_POLARS_METHOD_MAPPING = {
        "d": "day",
        "h": "hour",
        "m": "minute",
        "s": "second",
        "ms": "millisecond",
        "us": "microsecond",
        "ns": "nanosecond",
        "wd": "weekday",
}

patterns = ["==6d<6h6s"]
patterns_metadata = get_rule_metadata_from_patterns(patterns)

# -- create an expression for the rule pattern
pattern_metadata = patterns_metadata[0]  # list of length two

# -- let's consider the condition for ==6d
condition = pattern_metadata[0]

decomposed_duration = condition["decomposed_duration"]  # [(6, 'd')]
operator = condition["operator"]  # eq
conditions = [
    getattr(  # apply the operator method, e.g. pl.col("date").dt.hour().eq(value)
        getattr(  # get the value of the unit, e.g. pl.col("date").dt.hour()
            pl.col(time_column).dt,  
            UNIT_TO_POLARS_METHOD_MAPPING[unit], 
        )(),
        operator,
    )(value) for value, unit in decomposed_duration  # for each unit separately
]

# -- finally, we aggregate the separate conditions using an AND condition
final_expression = conditions.pop()
for expression in conditions:
    final_expression = getattr(final_expression, 'and_')(expression)
```

这看起来很复杂……但是我们可以将其中的一部分转换为函数，最终的代码看起来非常简洁易读：

```py
rules = []  # list to store expressions for each time pattern
for rule_metadata in patterns_metadata:
    rule_expressions = []
    for condition in rule_metadata:
        how = condition["how"]
        decomposed_duration = condition["decomposed_duration"]
        operator = condition["operator"]
        if how == "simple":
            expression = generate_polars_condition(  # function to do the final combination of expressions
                [
                    self._generate_simple_condition(
                        unit, value, operator
                    )  # this is the complex "getattr" code
                    for value, unit in decomposed_duration
                ],
                "and_",
            )
        rule_expressions.append(expression)

    rule_expression = generate_polars_condition(
        rule_expressions, "and_"
    )
    rules.append(rule_expression)

overall_rule_expression = generate_polars_condition(
    rules, "or_"
).not_()  # we must negate because we're filtering!
```

## 为级联操作符创建规则

在上面的代码中，我只为“简单”条件设置了if条件……那么我们该如何处理级联条件呢？

请记住，在我们上面的讨论中，“>6h*”的模式意味着：

> hour > 6 OR (hour == 6 AND any(min, s, ms, etc… > 0)

所以我们需要做的，是了解每个单位对应的后续较小单位。

例如，如果我有">6d*"，我应该知道在我的**任何**条件中包括“小时”，因此：

> day > 6 OR (day == 6 AND any(hr, min, s, ms, etc… > 0)

这可以通过使用一个字典来轻松实现，该字典将每个单位映射到它的“下一个”较小单位。例如：day → hour，hour → second，等等……

```py
POLARS_DURATIONS_TO_IMMEDIATE_CHILD_MAPPING = {
    "y": {"next": "mo", "start": 1},
    "mo": {"next": "d", "start": 1},
    "d": {"next": "h", "start": 0},
    "wd": {"next": "h", "start": 0},
    "h": {"next": "m", "start": 0},
    "m": {"next": "s", "start": 0},
    "s": {"next": "ms", "start": 0},
    "ms": {"next": "us", "start": 0},
    "us": {"next": "ns", "start": 0},
}
```

启动值是必要的，因为任何条件不一定总是 > 0。因为如果我想过滤掉所有大于二月的值，那么2023-02-02应该包含在内，而2023-02-01则不应该。

有了这个字典，我们就可以轻松创建任何条件：

```py
# -- pattern example: >6h* cascade
simple_condition = self._generate_simple_condition(
    unit, value, operator
)  # generate the simple condition, e.g. hour>6
all_conditions = [simple_condition]
if operator == "gt":  # cascade only affects > operator
    equality_condition = self._generate_simple_condition(
        unit, value, "eq"
    )  # generate hour==6
    child_unit_conditions = []
    child_unit_metadata = (
        POLARS_DURATIONS_TO_IMMEDIATE_CHILD_MAPPING.get(unit, None)
    )  # get the next smallest unit, e.g. minute
    while child_unit_metadata is not None:
        start_value = child_unit_metadata["start"]
        child_unit = child_unit_metadata["next"]
        child_unit_condition = self._generate_simple_condition(
            child_unit, start_value, "gt"
        )  # generate minute > 0
        child_unit_conditions.append(child_unit_condition)
        child_unit_metadata = (
            POLARS_DURATIONS_TO_IMMEDIATE_CHILD_MAPPING.get(
                child_unit, None
            )
        )  # now go on to seconds, and so on...

    cascase_condition = generate_polars_condition(
        [
            equality_condition,  # and condition for the hour unit
            generate_polars_condition(child_unit_conditions, "or_"),  # any condition for all the child units
        ],
        "and_",
    )

    all_conditions.append(cascase_condition)

# -- final condition is hour>6 AND the cascade condition
overall_condition = generate_polars_condition(all_conditions, "or_")
```

# 更大的图景

像这样的处理器不仅仅对临时分析有用。它可以成为数据处理管道的核心组件。对我来说，一个非常有用的用例是将其与重采样结合使用。一个简单的过滤步骤将使我能够轻松地计算具有规律性中断或规律性停机的时间序列的指标。

此外，通过一些简单的修改，我可以扩展这个处理器，使其能够轻松标记我的时间序列。这使我能够为那些我知道行为不同的部分添加回归变量，例如，如果我正在建模一个在特定时刻跳变的时间序列，我可以只在那些部分添加一个阶跃回归变量。

# 结语

在本文中，我概述了一个处理器，使得在Polars数据集上进行简便、灵活和简洁的时间序列过滤成为可能。所讨论的逻辑可以通过一些小的修改扩展到你最喜欢的数据框处理库，比如Pandas。

这个处理器不仅对临时的时间序列分析有用，还可以成为数据处理的核心，如果与其他操作（如重采样）链式使用，或者用于为建模创建额外特征时。

我将通过一些我想到的扩展来结束这篇文章，以使代码更加完善：

+   我正在考虑创建一个快捷方式来定义“周末”，例如，“==we”。这样，我就不需要显式地定义“>=6wd”，这可能不太清晰。

+   通过适当的设计，我认为可以实现添加自定义时间标识符。例如，“==eve”表示傍晚，时间范围可以由用户定义。

+   我一定会添加对仅仅标记数据的支持，而不是过滤数据。

+   我还打算添加支持将边界定义为“keep”，例如，代替定义["<6h", ">=20hr"]，我可以使用[">=6h<20hr"]。

## 如何查找代码

这个项目还处于初期阶段，因此项目内容可能会有所变动。截止2024年5月23日，你可以在`mix_n_match/main.py`中找到`FilterDataBasedOnTime`。

[](https://github.com/namiyousef/mix-n-match?source=post_page-----fa9d5da734b3--------------------------------) [## GitHub - namiyousef/mix-n-match: 数据框处理的代码库

### 数据框处理的代码库。通过在GitHub上创建帐户，参与namiyousef/mix-n-match开发…

github.com](https://github.com/namiyousef/mix-n-match?source=post_page-----fa9d5da734b3--------------------------------)

*除非另有说明，所有代码、数据和图片均为作者所有*。
