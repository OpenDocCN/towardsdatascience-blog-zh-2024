# Pandas: 从杂乱到优雅

> 原文：[https://towardsdatascience.com/pandas-from-messy-to-beautiful-b03b0c32f767?source=collection_archive---------2-----------------------#2024-03-31](https://towardsdatascience.com/pandas-from-messy-to-beautiful-b03b0c32f767?source=collection_archive---------2-----------------------#2024-03-31)

## 这就是如何让你的 pandas 代码既易于阅读又坚不可摧。

[](https://medium.com/@azawadzka?source=post_page---byline--b03b0c32f767--------------------------------)[![Anna Zawadzka](../Images/bcdd9dfb9faeace44e831c057984bc63.png)](https://medium.com/@azawadzka?source=post_page---byline--b03b0c32f767--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b03b0c32f767--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b03b0c32f767--------------------------------) [Anna Zawadzka](https://medium.com/@azawadzka?source=post_page---byline--b03b0c32f767--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b03b0c32f767--------------------------------) ·阅读时间 9 分钟 ·2024年3月31日

--

![](../Images/85b2209904b2b7ed17ff3d5b5e960141.png)

在 pandas `DataFrame` 上编写脚本可能会变成一堆尴尬的（并不那么）老式意大利面式代码。我和我的同事们经常使用这个包，尽管我们尽力遵循良好的编程实践，比如将代码拆分成模块和进行单元测试，但有时我们还是会互相妨碍，编写出令人困惑的代码。

我收集了一些技巧和陷阱，帮助避免在编写 pandas 代码时出现问题，从而使代码更加清晰和万无一失。希望你也能从中受益。我们将借助 Robert C. Martin 的经典著作《Clean Code》，特别是结合 pandas 包的背景进行讨论。简短总结见文末。

# 不该做的事

让我们从一些受真实案例启发的错误模式开始分析。稍后，我们将尝试重构这些代码，以提升可读性和可控性。

## 可变性

Pandas `DataFrame` 是值**可变**的 [[2](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html#mutability-and-copying-of-data), [3]](https://realpython.com/python-mutable-vs-immutable-types/) 对象。每当你更改一个可变对象时，它会影响你最初创建的相同实例，并且它在内存中的物理位置保持不变。相比之下，当你修改一个**不可变**对象（例如字符串）时，Python 会创建一个全新的对象，并将其存储在新的内存位置，然后替换为对该新对象的引用。

这是关键点：在 Python 中，对象通过**赋值**传递给函数[[4](https://medium.com/techtofreedom/5-levels-of-understanding-the-mutability-of-python-objects-a5ed839d6c24), [5]](https://realpython.com/python-pass-by-reference/)**。** 看图：`df` 的值在作为参数传递给函数时被赋值给变量 `in_df`。即使它们使用不同的变量名，原始的 `df` 和函数内部的 `in_df` 都指向相同的内存位置（括号中的数字值）。在修改其属性时，可变对象的位置保持不变。现在，所有其他作用域也能看到这些变化——它们都指向相同的内存位置。

![](../Images/388fcc4c3aa872d7d1d0775d63fd45ee.png)

修改可变对象在 Python 内存中的情况。

实际上，由于我们已修改了原始实例，因此返回 `DataFrame` 并将其赋值给变量是多余的。这个代码有相同的效果：

![](../Images/5d40a39701f5dcc10a5cad4a1a5bbbce.png)

修改可变对象在 Python 内存中的情况，冗余赋值已被移除。

提醒：现在函数返回 `None`，因此要小心不要将 `df` 覆盖为 `None`，如果你确实执行赋值：`df = modify_df(df)`。

相反，如果对象是不可变的，它会在整个修改过程中改变内存位置，就像下面的例子一样。由于红色字符串无法修改（字符串是不可变的），绿色字符串是在旧字符串之上创建的，但它是一个全新的对象，声明了一个新的内存位置。返回的字符串不是同一个字符串，而返回的 `DataFrame` 是完全相同的 `DataFrame`。

![](../Images/749363a0bcce3c499a1c18844d6814c5.png)

修改不可变对象在 Python 内存中的情况。

关键是，在函数内修改 `DataFrame` 会产生**全局效果**。如果你没有牢记这一点，可能会：

+   意外修改或删除部分数据，以为操作只发生在函数作用域内——其实并非如此，

+   失去对何时向 `DataFrame` 添加内容的控制，例如在嵌套的函数调用中。

## 输出参数

我们稍后会解决这个问题，但在我们进入`do`之前，这里还有另一个`don't`

前一节的设计实际上是一种反模式，称为**输出参数**[1 p.45]。通常，函数的**输入**将用于创建**输出**值。如果传递一个参数给函数的唯一目的是修改它，使得输入参数改变其状态，那么这就挑战了我们的直觉。这种行为被称为函数的**副作用**[1 p.44]，应当做好文档记录并尽量减少，因为它们迫使程序员记住那些发生在后台的事情，从而使得脚本容易出错。

> *当我们阅读一个函数时，我们习惯了信息通过参数进入函数并通过返回值输出。我们通常不期望信息会通过参数输出。[1 p.41]*

如果函数有双重责任：既修改输入**又**返回输出，情况会变得更糟。考虑这个函数：

```py
def find_max_name_length(df: pd.DataFrame) -> int:
    df["name_len"] = df["name"].str.len()  # side effect
    return max(df["name_len"])
```

它的确返回了一个值，正如你所期望的，但它也永久修改了原始的`DataFrame`。这个副作用会让你感到惊讶——函数签名中没有任何东西表明我们的输入数据会被影响。在下一步中，我们将看到如何避免这种设计。

# 应该做的事

## 减少修改

为了消除副作用，在下面的代码中，我们创建了一个新的临时变量，而不是修改原始的`DataFrame`。符号`lengths: pd.Series`表示该变量的数据类型。

```py
def find_max_name_length(df: pd.DataFrame) -> int:
    lengths: pd.Series = df["name"].str.len()
    return max(lengths)
```

这种函数设计更好，因为它封装了中间状态，而不是产生副作用。

另一个提醒：请注意**深拷贝和浅拷贝**的区别[[6]](https://realpython.com/copying-python-objects/)。在上面的示例中，我们修改了原始`df["name"]` `Series`的每个元素，因此旧的`DataFrame`和新的变量没有共享的元素。然而，如果你直接将原始列之一赋值给一个新变量，底层元素在内存中仍然引用相同的对象。请参阅以下示例：

```py
df = pd.DataFrame({"name": ["bert", "albert"]})

series = df["name"]     # shallow copy
series[0] = "roberta"   # <-- this changes the original DataFrame

series = df["name"].copy(deep=True)
series[0] = "roberta"   # <-- this does not change the original DataFrame

series = df["name"].str.title()  # not a copy whatsoever
series[0] = "roberta"   # <-- this does not change the original DataFrame
```

你可以在每一步后打印出`DataFrame`，以观察效果。记住，创建深拷贝会分配新的内存，所以考虑一下你的脚本是否需要高效地使用内存。

## 分组相似的操作

也许由于某种原因，你想存储那个长度计算的结果。把它附加到函数内部的`DataFrame`中依然不是一个好主意，因为这违反了**副作用**的规则，并且使一个函数承担了**多重责任**。

我喜欢**每个函数一个抽象层次**的规则，它的意思是：

> *我们需要确保函数中的所有语句都处于相同的抽象层次。*
> 
> *在一个函数内混合不同的抽象层次总是令人困惑。读者可能无法分辨某个表达式是核心概念还是细节。[1 p.36]*

另外，尽管我们现在并不专注于面向对象代码，但我们还是可以借鉴OOP中的**单一职责原则**[1 p.138]。

为什么不提前准备好数据呢？我们将数据准备和实际计算分成不同的函数：

```py
def create_name_len_col(series: pd.Series) -> pd.Series:
    return series.str.len()

def find_max_element(collection: Collection) -> int:
    return max(collection) if len(collection) else 0

df = pd.DataFrame({"name": ["bert", "albert"]})
df["name_len"] = create_name_len_col(df.name)
max_name_len = find_max_element(df.name_len)
```

创建`name_len`列的单一任务已外包给另一个函数。它不会修改原始的`DataFrame`，并且**一次只执行一个任务**。稍后我们通过将新列传递给另一个专用函数来获取最大元素。注意，聚合函数对于`Collections`来说是通用的。

让我们通过以下步骤来优化代码：

+   我们可以使用`concat`函数，并将其提取到一个名为`prepare_data`的单独函数中，这样可以将所有数据准备步骤集中在一个地方，

+   我们还可以利用`apply`方法，处理单个文本，而不是`Series`中的文本，

+   让我们记住使用浅拷贝和深拷贝，具体取决于是否应该修改原始数据：

```py
def compute_length(word: str) -> int:
    return len(word)

def prepare_data(df: pd.DataFrame) -> pd.DataFrame:
    return pd.concat([
        df.copy(deep=True),  # deep copy
        df.name.apply(compute_length).rename("name_len"),
        ...
    ], axis=1)
```

## 可重用性

我们拆分代码的方式确实让我们以后很容易回到脚本，拿出整个函数并在另一个脚本中重用。我们喜欢这样！

还有一件事我们可以做，以提高可重用性：将列名作为参数传递给函数。重构有点过头，但有时为了灵活性或可重用性，还是值得的。

```py
def create_name_len_col(df: pd.DataFrame, orig_col: str, target_col: str) -> pd.Series:
    return df[orig_col].str.len().rename(target_col)

name_label, name_len_label = "name", "name_len"
pd.concat([
    df,
    create_name_len_col(df, name_label, name_len_label)
], axis=1)
```

## 可测试性

你是否曾在对预处理数据集进行几周的实验后，才发现预处理有问题？没有？真幸运。我实际上因为注释错误不得不重复一批实验，如果我在一开始测试过一些基本的函数，完全可以避免这种情况。

重要的脚本应该进行**测试** [1 p.121, 7]。即使这个脚本只是一个辅助工具，我现在也会尝试至少测试最关键的、最底层的函数。让我们回顾一下从一开始到现在的步骤：

1\. 我甚至不愿意考虑测试这个，它非常冗余，我们已经忽视了副作用。它还测试了许多不同的功能：计算名称长度和聚合最大元素的结果。而且它失败了，你预见到了吗？

```py
def find_max_name_length(df: pd.DataFrame) -> int:
    df["name_len"] = df["name"].str.len()  # side effect
    return max(df["name_len"])

@pytest.mark.parametrize("df, result", [
    (pd.DataFrame({"name": []}), 0),  # oops, this fails!
    (pd.DataFrame({"name": ["bert"]}), 4),
    (pd.DataFrame({"name": ["bert", "roberta"]}), 7),
])
def test_find_max_name_length(df: pd.DataFrame, result: int):
    assert find_max_name_length(df) == result
```

2\. 这样好多了——我们集中精力处理一个任务，因此测试变得简单。我们也不需要像之前那样过于关注列名。然而，我认为数据格式阻碍了验证计算正确性。

```py
def create_name_len_col(series: pd.Series) -> pd.Series:
    return series.str.len()

@pytest.mark.parametrize("series1, series2", [
    (pd.Series([]), pd.Series([])),
    (pd.Series(["bert"]), pd.Series([4])),
    (pd.Series(["bert", "roberta"]), pd.Series([4, 7]))
])
def test_create_name_len_col(series1: pd.Series, series2: pd.Series):
    pd.testing.assert_series_equal(create_name_len_col(series1), series2, check_dtype=False)
```

3\. 这里我们清理了桌面。我们彻底测试了计算函数，抛开了Pandas的封装。在专注于一个问题时，更容易想到边界情况。我发现我想测试`DataFrame`中可能出现的`None`值，最终我不得不改进我的函数，以使这个测试通过。一个bug被发现了！

```py
def compute_length(word: Optional[str]) -> int:
    return len(word) if word else 0

@pytest.mark.parametrize("word, length", [
    ("", 0),
    ("bert", 4),
    (None, 0)
])
def test_compute_length(word: str, length: int):
    assert compute_length(word) == length
```

4\. 我们只差对`find_max_element`的测试了：

```py
def find_max_element(collection: Collection) -> int:
    return max(collection) if len(collection) else 0

@pytest.mark.parametrize("collection, result", [
    ([], 0),
    ([4], 4),
    ([4, 7], 7),
    (pd.Series([4, 7]), 7),
])
def test_find_max_element(collection: Collection, result: int):
    assert find_max_element(collection) == result
```

我从不忘提到的单元测试的另一个好处是，它是**文档化代码**的一种方式，因为不懂代码的人（比如**未来的你**）可以仅通过查看测试，轻松了解输入和期望输出，包括边界情况。双重收获！

# 结论

这些是我在编写代码和审查别人代码时发现有用的一些技巧。我并不是告诉你某种编码方式就是唯一正确的——你可以从中选择你需要的，你决定是要一个快速的临时解决方案，还是要一个高度精炼和经过测试的代码库。我希望这篇文章能帮助你更好地组织脚本，使你对它们更加满意并对它们的可靠性感到更加自信。

如果你喜欢这篇文章，请告诉我。我很高兴能得到反馈。祝你编码愉快！

> 摘要
> 
> 没有一种唯一正确的编码方式，但以下是一些使用pandas进行脚本编写的灵感：
> 
> 禁止事项：
> 
> - 在函数内尽量不要频繁修改你的`*DataFrame*`，因为这样你可能会失去对其中内容的控制，无法知道哪些数据被追加或移除。
> 
> - 不要编写修改`*DataFrame*`并且不返回任何结果的方法，因为这会让人感到困惑。
> 
> 必做事项：
> 
> - 创建新对象，而不是修改原始的`*DataFrame*`，并记得在需要时进行深拷贝，
> 
> - 在一个函数内只执行类似层次的操作，
> 
> - 设计灵活且可重用的函数，
> 
> - 测试你的函数，因为这能帮助你设计更简洁的代码，防止出现错误和边界情况，还能免费为代码生成文档。

# 参考文献

+   [1] Robert C. Martin，《代码整洁之道：敏捷软件工艺手册》（2009），Pearson Education, Inc.

+   [2] pandas文档 - 包概述 — 数据的可变性与拷贝，[https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html#mutability-and-copying-of-data](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html#mutability-and-copying-of-data)

+   [3] Python的可变类型与不可变类型：有何区别？，[https://realpython.com/python-mutable-vs-immutable-types/](https://realpython.com/python-mutable-vs-immutable-types/)

+   [4] 理解Python对象的可变性：5个层次，[https://medium.com/techtofreedom/5-levels-of-understanding-the-mutability-of-python-objects-a5ed839d6c24](https://medium.com/techtofreedom/5-levels-of-understanding-the-mutability-of-python-objects-a5ed839d6c24)

+   [5] Python中的引用传递：背景与最佳实践，[https://realpython.com/python-pass-by-reference/](https://realpython.com/python-pass-by-reference/)

+   [6] Python对象的浅拷贝与深拷贝，[https://realpython.com/copying-python-objects/](https://realpython.com/copying-python-objects/)

+   [7] Brian Okken，《Python Testing with pytest》，第二版（2022），The Pragmatic Programmers, LLC.

这些图表是我使用[Miro](https://miro.com/)制作的。封面图片也是我使用[Titanic](https://www.openml.org/search?type=data&sort=runs&id=40945&status=active)数据集和GIMP（涂抹效果）制作的。
