# Pandas 2.2 新特性

> 原文：[https://towardsdatascience.com/whats-new-in-pandas-2-2-e3afe6f341f5?source=collection_archive---------3-----------------------#2024-01-30](https://towardsdatascience.com/whats-new-in-pandas-2-2-e3afe6f341f5?source=collection_archive---------3-----------------------#2024-01-30)

## 关于新版本最有趣的内容

[](https://medium.com/@patrick_hoefler?source=post_page---byline--e3afe6f341f5--------------------------------)[![Patrick Hoefler](../Images/35ca9ef1100d8c93dbadd374f0569fe1.png)](https://medium.com/@patrick_hoefler?source=post_page---byline--e3afe6f341f5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e3afe6f341f5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e3afe6f341f5--------------------------------) [Patrick Hoefler](https://medium.com/@patrick_hoefler?source=post_page---byline--e3afe6f341f5--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e3afe6f341f5--------------------------------) ·5分钟阅读·2024年1月30日

--

![](../Images/9eaf581fc856e2a9299e0181e4191394.png)

由[Zoe Nicolaou](https://unsplash.com/@lekneuro?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，图片来源于[Unsplash](https://unsplash.com/photos/panda-climbing-on-tree-44g_jwn3JzY?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

pandas 2.2于2024年1月22日发布。让我们看看这一版本带来了哪些新特性，以及它如何帮助我们改善pandas工作负载。它包含了一系列提升用户体验的改进。

pandas 2.2带来了一些额外的改进，这些改进依赖于Apache Arrow生态系统。此外，我们还增加了一些弃用内容，为pandas 3.0中将“写时复制”（Copy-on-Write）设置为默认选项所做的必要更改做准备。让我们深入了解这对你意味着什么。我们将详细探讨一些最重要的更改。

我是pandas核心团队的一员，同时也是[Coiled](https://www.coiled.io)的开源工程师，我在这里负责Dask的工作，包括改进pandas的集成。

## 改进的PyArrow支持

我们在pandas 2.0中引入了基于PyArrow的DataFrame，并且从那时起持续改进集成，以实现与pandas API的无缝对接。pandas为某些数据类型提供了访问器，支持特定的操作，例如字符串访问器，提供了许多字符串方法。过去，列表和结构体是作为NumPy对象数据类型表示的，这使得处理这些数据类型变得非常繁琐。现在，Arrow数据类型后端为列表和结构体提供了定制的访问器，使得处理这些对象变得更加简便。

让我们看一个例子：

```py
import pyarrow as pa

series = pd.Series(
    [
        {"project": "pandas", "version": "2.2.0"},
        {"project": "numpy", "version": "1.25.2"},
        {"project": "pyarrow", "version": "13.0.0"},
    ],
    dtype=pd.ArrowDtype(
        pa.struct([
            ("project", pa.string()),
            ("version", pa.string()),
        ])
    ),
)
```

这是一个包含字典的系列，每一行都有一个字典。以前，这只有在使用 NumPy 对象 dtype 时才可行，并且从这些行中访问元素需要进行迭代。现在，`struct` 访问器使得可以直接访问某些属性：

```py
series.struct.field("project")

0     pandas
1      numpy
2    pyarrow
Name: project, dtype: string[pyarrow]
```

下一版本将带来基于 Arrow 类型的 CategoricalAccessor。

## 集成 Apache ADBC 驱动程序

历史上，pandas 依赖 SqlAlchemy 从 Sql 数据库中读取数据。这种方式非常可靠，但速度较慢。SqlAlchemy 按行读取数据，而 pandas 是列式布局，这使得读取数据并将其移动到 DataFrame 中的过程比必要的更慢。

来自 Apache Arrow 项目的 [ADBC 驱动程序](https://arrow.apache.org/docs/format/ADBC.html)使用户能够以列式布局读取数据，这带来了巨大的性能提升。它将数据读取并存储到 Arrow 表中，该表用于转换为 pandas DataFrame。如果设置 `dtype_backend="pyarrow"`，则可以实现零拷贝转换。

让我们看一个例子：

```py
import adbc_driver_postgresql.dbapi as pg_dbapi

df = pd.DataFrame(
   [
       [1, 2, 3],
       [4, 5, 6],
   ],
   columns=['a', 'b', 'c']
)
uri = "postgresql://postgres:postgres@localhost/postgres"
with pg_dbapi.connect(uri) as conn:
   df.to_sql("pandas_table", conn, index=False)

# for round-tripping
with pg_dbapi.connect(uri) as conn:
   df2 = pd.read_sql("pandas_table", conn)
```

ADBC 驱动程序当前支持 Postgres 和 Sqlite。如果你使用 Postgres，强烈建议切换到这个驱动程序，它比 SqlAlchemy 快得多，并且完全避免了通过 Python 对象的回程，从而更可靠地保留了数据库类型。这是我个人最为激动的功能。

## 将 `case_when` 添加到 pandas API 中

从 Sql 转到 pandas，用户通常会错过 `case-when` 语法，它提供了一种简单清晰的方式来有条件地创建新列。pandas 2.2 添加了一个新的 `case_when` 方法，它是在 Series 上定义的。其操作方式类似于 Sql。

让我们看一个例子：

```py
df = pd.DataFrame(dict(a=[1, 2, 3], b=[4, 5, 6]))

default=pd.Series('default', index=df.index)
default.case_when(
    caselist=[
        (df.a == 1, 'first'),
        (df.a.gt(1) & df.b.eq(5), 'second'),
    ],
)
```

该方法接受一个条件列表，这些条件将按顺序进行评估。然后在条件评估为 True 的行中，用这些值创建新对象。该方法应该能显著简化我们创建条件列的工作。

## Copy-on-Write

Copy-on-Write 最初在 pandas 1.5.0 中引入。此模式将在 3.0 版本中成为默认行为，希望这是下一个 pandas 发布版本。这意味着我们必须将代码调整到符合 Copy-on-Write 规则的状态。pandas 2.2 引入了操作的弃用警告，这些操作会改变行为。

```py
df = pd.DataFrame({"x": [1, 2, 3]})

df["x"][df["x"] > 1] = 100
```

这将引发 `FutureWarning`。

```py
FutureWarning: ChainedAssignmentError: behaviour will change in pandas 3.0!
You are setting values through chained assignment. Currently this works in certain cases, but when 
using Copy-on-Write (which will become the default behaviour in pandas 3.0) this will never work to 
update the original DataFrame or Series, because the intermediate object on which we are setting 
values will behave as a copy. A typical example is when you are setting values in a column of a 
DataFrame, like:

df["col"][row_indexer] = value

Use `df.loc[row_indexer, "col"] = values` instead, to perform the assignment in a single step and 
ensure this keeps updating the original `df`.
```

我写过 [一篇早期的文章](https://medium.com/towards-data-science/deep-dive-into-pandas-copy-on-write-mode-part-iii-c024eaa16ed4)，详细介绍了如何迁移代码以及应该期待什么。Copy-on-Write 还新增了一种警告模式，用于对所有改变行为的情况发出警告：

```py
pd.options.mode.copy_on_write = "warn"
```

对大多数 pandas 用户来说，这些警告大多只是噪音，这也是为什么它们被隐藏在一个选项后面的原因。

```py
pd.options.mode.copy_on_write = "warn"

df = pd.DataFrame({"a": [1, 2, 3]})
view = df["a"]
view.iloc[0] = 100
```

这将引发一条长警告，解释将会发生什么变化：

```py
FutureWarning: Setting a value on a view: behaviour will change in pandas 3.0.
You are mutating a Series or DataFrame object, and currently this mutation will
also have effect on other Series or DataFrame objects that share data with this
object. In pandas 3.0 (with Copy-on-Write), updating one Series or DataFrame object
will never modify another.
```

这的简要总结是：更新`view`将永远不会更新`df`，无论使用何种操作。大多数情况下，这与大多数人无关。

我建议启用该模式并简要查看警告，但如果你确信自己没有依赖于同时更新两个不同对象，可以不必过于关注这些警告。

我建议查看[写时复制迁移指南](https://pandas.pydata.org/docs/dev/user_guide/copy_on_write.html#migrating-to-copy-on-write)，该指南更详细地解释了必要的更改。

## 升级到新版本

你可以通过以下方式安装新的 pandas 版本：

```py
pip install -U pandas
```

或者：

```py
mamba install -c conda-forge pandas=2.2
```

这将在你的环境中提供新版本。

## 结论

我们已经查看了一些改进，这些改进将提升 pandas 在某些方面的性能和用户体验。最令人兴奋的新特性将在 pandas 3.0 中推出，其中默认启用写时复制（Copy-on-Write）。

感谢阅读。欢迎随时联系，分享你的想法和反馈。
