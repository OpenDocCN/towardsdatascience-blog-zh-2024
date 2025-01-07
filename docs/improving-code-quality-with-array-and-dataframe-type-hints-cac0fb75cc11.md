# 提升代码质量：数组和DataFrame类型提示

> 原文：[https://towardsdatascience.com/improving-code-quality-with-array-and-dataframe-type-hints-cac0fb75cc11?source=collection_archive---------5-----------------------#2024-09-19](https://towardsdatascience.com/improving-code-quality-with-array-and-dataframe-type-hints-cac0fb75cc11?source=collection_archive---------5-----------------------#2024-09-19)

## 泛型类型规范如何实现强大的静态分析和运行时验证

[](https://medium.com/@flexatone?source=post_page---byline--cac0fb75cc11--------------------------------)[![Christopher Ariza](../Images/35208ace15080724e4cd6690e43d6502.png)](https://medium.com/@flexatone?source=post_page---byline--cac0fb75cc11--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cac0fb75cc11--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cac0fb75cc11--------------------------------) [Christopher Ariza](https://medium.com/@flexatone?source=post_page---byline--cac0fb75cc11--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cac0fb75cc11--------------------------------) ·10分钟阅读·2024年9月19日

--

![](../Images/a75b36f65c66b95f33aea2faefea4dbb.png)

作者照片

随着Python类型注解（或类型提示）工具的发展，越来越复杂的数据结构可以被指定类型，从而提高了代码的可维护性和静态分析能力。数组和DataFrame作为复杂的数据容器，直到最近才开始在Python中支持完整的类型注解。NumPy 1.22引入了数组和数据类型（dtypes）的通用规格。在NumPy的基础上，[StaticFrame](https://github.com/static-frame/static-frame) 2.0引入了DataFrame的完整类型规格，采用了NumPy原语和可变泛型。本文展示了对数组和DataFrame进行完全类型提示的实际方法，并展示了如何通过静态分析和运行时验证来提高代码质量。

[StaticFrame](https://github.com/static-frame/static-frame)是一个开源的DataFrame库，我是该库的作者之一。

# 类型提示提升代码质量

类型提示（参见[PEP 484](https://peps.python.org/pep-0484/)）通过多种方式提升代码质量。与使用变量名或注释来传达类型不同，基于Python对象的类型注解提供了可维护且富有表现力的工具用于类型规范。这些类型注解可以通过类型检查工具（如`mypy`或`pyright`）进行测试，能够在不执行代码的情况下迅速发现潜在的bug。

相同的注解可以用于运行时验证。虽然在Python中依赖鸭子类型（duck-typing）而非运行时验证较为常见，但对于复杂数据结构（如数组和DataFrame），往往更需要运行时验证。例如，期望DataFrame作为参数的接口，如果传入一个Series，可能不需要显式验证，因为错误类型的使用很可能会引发异常。然而，期望二维浮点数组的接口，如果传入一个布尔值数组，则可能受益于验证，因为错误类型的使用可能不会引发异常。

许多重要的类型工具仅在最新版本的Python中可用。幸运的是，`typing-extensions`包为较旧版本的Python提供了标准库工具的后向移植。一个相关的挑战是，类型检查器可能需要一些时间才能完全支持新特性：这里展示的许多示例至少需要`mypy` 1.9.0。

# 基本类型注解

如果没有类型注解，Python函数签名无法指示期望的类型。例如，下面的函数可能接受和返回任何类型：

```py
def process0(v, q): ... # no type information
```

通过添加类型注解，函数签名向读者表明期望的类型。在现代Python中，可以使用用户定义的类和内置类来指定类型，更多的资源（如`Any`、`Iterator`、`cast()`和`Annotated`）可以在标准库`typing`模块中找到。例如，下面的接口通过明确指定期望类型来改进上述接口：

```py
def process0(v: int, q: bool) -> list[float]: ...
```

当与像`mypy`这样的类型检查器一起使用时，违反类型注解规范的代码将在静态分析时抛出错误（如下所示的评论）。例如，当需要布尔值时提供整数是一个错误：

```py
x = process0(v=5, q=20)
# tp.py: error: Argument "q" to "process0"
# has incompatible type "int"; expected "bool"  [arg-type]
```

静态分析只能验证静态定义的类型。运行时输入和输出的完整范围通常更加多样化，这意味着需要某种形式的运行时验证。通过重复使用类型注解进行运行时验证，可以实现两全其美。虽然有一些库可以做到这一点（例如，`typeguard`和`beartype`），但StaticFrame提供了专门用于全面数组和DataFrame类型注解验证的工具`CallGuard`。

Python装饰器非常适合利用注解进行运行时验证。`CallGuard`提供了两个装饰器：`[@CallGuard](http://twitter.com/CallGuard).check`，在出错时抛出详细的`Exception`，或者`[@CallGuard](http://twitter.com/CallGuard).warn`，在出错时发出警告。

通过在上面的`process0`函数中扩展`[@CallGuard](http://twitter.com/CallGuard).check`，可以使用相同的类型注解，当运行时对象违反类型注解的要求时，抛出`Exception`（如评论中再次显示）：

```py
import static_frame as sf

@sf.CallGuard.check
def process0(v: int, q: bool) -> list[float]:
    return [x * (0.5 if q else 0.25) for x in range(v)]

z = process0(v=5, q=20)
# static_frame.core.type_clinic.ClinicError:
# In args of (v: int, q: bool) -> list[float]
# └── Expected bool, provided int invalid
```

虽然类型注解必须是有效的 Python，但它们在运行时是无关的，且可能是错误的：有可能拥有正确验证的类型，但它们并未反映运行时的实际情况。如上所示，通过重用类型注解进行运行时检查可以确保注解的有效性。

# 数组类型注解

允许组件类型规范的 Python 类被称为“泛型”。组件类型通过位置型“类型变量”来指定。例如，一个整数列表的注解是`list[int]`；一个由整数和字符串元组作为键的浮点数字典的注解是`dict[tuple[int, str], float]`。

在 NumPy 1.20 版本中，`ndarray` 和 `dtype` 变得更加泛型化。泛型 `ndarray` 需要两个参数，一个是形状，另一个是 `dtype`。由于第一个参数的使用仍在开发中，因此通常使用 `Any`。第二个参数 `dtype` 本身是一个泛型，要求使用一个类型变量来指定 NumPy 类型，例如 `np.int64`。NumPy 还提供了更为通用的泛型类型，如 `np.integer[Any]`。

例如，一个布尔数组的注解是`np.ndarray[Any, np.dtype[np.bool_]]`；任何类型整数的数组的注解是`np.ndarray[Any, np.dtype[np.integer[Any]]]`。

由于带有组件类型规范的泛型注解可能变得冗长，因此将它们存储为类型别名（此处以“T”作为前缀）是实际的做法。以下函数指定了此类别名，并在函数中使用它们。

```py
from typing import Any
import numpy as np

TNDArrayInt8 = np.ndarray[Any, np.dtype[np.int8]]
TNDArrayBool = np.ndarray[Any, np.dtype[np.bool_]]
TNDArrayFloat64 = np.ndarray[Any, np.dtype[np.float64]]

def process1(
        v: TNDArrayInt8,
        q: TNDArrayBool,
        ) -> TNDArrayFloat64:
    s: TNDArrayFloat64 = np.where(q, 0.5, 0.25)
    return v * s
```

如前所述，当与 `mypy` 一起使用时，违反类型注解的代码将在静态分析期间引发错误。例如，在需要布尔值时提供整数是一个错误：

```py
v1: TNDArrayInt8 = np.arange(20, dtype=np.int8)
x = process1(v1, v1)
# tp.py: error: Argument 2 to "process1" has incompatible type
# "ndarray[Any, dtype[floating[_64Bit]]]"; expected "ndarray[Any, dtype[bool_]]"  [arg-type]
```

该接口要求使用 8 位有符号整数（`np.int8`）；尝试使用不同大小的整数也是错误的：

```py
TNDArrayInt64 = np.ndarray[Any, np.dtype[np.int64]]
v2: TNDArrayInt64 = np.arange(20, dtype=np.int64)
q: TNDArrayBool = np.arange(20) % 3 == 0
x = process1(v2, q)
# tp.py: error: Argument 1 to "process1" has incompatible type
# "ndarray[Any, dtype[signedinteger[_64Bit]]]"; expected "ndarray[Any, dtype[signedinteger[_8Bit]]]"  [arg-type]
```

尽管某些接口可能受益于如此狭窄的数值类型规范，但使用 NumPy 的泛型类型，如`np.integer[Any]`、`np.signedinteger[Any]`、`np.float[Any]`等，依然可以进行更广泛的规范。例如，我们可以定义一个新的函数，接受任何大小的有符号整数。静态分析现在通过了 `TNDArrayInt8` 和 `TNDArrayInt64` 数组。

```py
TNDArrayIntAny = np.ndarray[Any, np.dtype[np.signedinteger[Any]]]
def process2(
        v: TNDArrayIntAny, # a more flexible interface
        q: TNDArrayBool,
        ) -> TNDArrayFloat64:
    s: TNDArrayFloat64 = np.where(q, 0.5, 0.25)
    return v * s

x = process2(v1, q) # no mypy error
x = process2(v2, q) # no mypy error
```

正如上面所示的元素，使用泛型指定的 NumPy 数组可以在运行时进行验证，前提是它们使用了 `CallGuard.check` 装饰器：

```py
@sf.CallGuard.check
def process3(v: TNDArrayIntAny, q: TNDArrayBool) -> TNDArrayFloat64:
    s: TNDArrayFloat64 = np.where(q, 0.5, 0.25)
    return v * s

x = process3(v1, q) # no error, same as mypy
x = process3(v2, q) # no error, same as mypy
v3: TNDArrayFloat64 = np.arange(20, dtype=np.float64) * 0.5
x = process3(v3, q) # error
# static_frame.core.type_clinic.ClinicError:
# In args of (v: ndarray[Any, dtype[signedinteger[Any]]],
# q: ndarray[Any, dtype[bool_]]) -> ndarray[Any, dtype[float64]]
# └── ndarray[Any, dtype[signedinteger[Any]]]
#     └── dtype[signedinteger[Any]]
#         └── Expected signedinteger, provided float64 invalid
```

StaticFrame 提供了扩展运行时验证的工具，超出了类型检查的范围。使用 `typing` 模块的 `Annotated` 类（见 [PEP 593](https://peps.python.org/pep-0593/)），我们可以通过一个或多个 StaticFrame `Require` 对象来扩展类型规范。例如，要验证一个数组具有 `(24,)` 的一维形状，我们可以将 `TNDArrayIntAny` 替换为 `Annotated[TNDArrayIntAny, sf.Require.Shape(24)]`。要验证浮点数组没有 NaN，我们可以将 `TNDArrayFloat64` 替换为 `Annotated[TNDArrayFloat64, sf.Require.Apply(lambda a: ~a.insna().any())]`。

在实现一个新函数时，我们可以要求所有输入和输出数组的形状为 `(24,)`。调用这个函数时，如果使用先前创建的数组，则会引发错误：

```py
from typing import Annotated

@sf.CallGuard.check
def process4(
        v: Annotated[TNDArrayIntAny, sf.Require.Shape(24)],
        q: Annotated[TNDArrayBool, sf.Require.Shape(24)],
        ) -> Annotated[TNDArrayFloat64, sf.Require.Shape(24)]:
    s: TNDArrayFloat64 = np.where(q, 0.5, 0.25)
    return v * s

x = process4(v1, q) # types pass, but Require.Shape fails
# static_frame.core.type_clinic.ClinicError:
# In args of (v: Annotated[ndarray[Any, dtype[int8]], Shape((24,))], q: Annotated[ndarray[Any, dtype[bool_]], Shape((24,))]) -> Annotated[ndarray[Any, dtype[float64]], Shape((24,))]
# └── Annotated[ndarray[Any, dtype[int8]], Shape((24,))]
#     └── Shape((24,))
#         └── Expected shape ((24,)), provided shape (20,)
```

# DataFrame 类型注解

就像字典一样，DataFrame 是一个由许多组件类型组成的复杂数据结构：索引标签、列标签和列值都是不同的类型。

通用地指定 DataFrame 的挑战在于 DataFrame 有可变数量的列，每一列可能有不同的类型。Python 的 `TypeVarTuple` 可变参数泛型说明符（见 [PEP 646](https://peps.python.org/pep-0646/)），首次在 Python 3.11 中发布，允许定义可变数量的列类型变量。

在 StaticFrame 2.0 中，`Frame`、`Series`、`Index` 和相关容器变成了通用类型。`TypeVarTuple` 提供了对可变列类型定义的支持，通过在 `typing-extensions` 中的实现向下兼容到 Python 3.9。

通用的 `Frame` 需要两个或更多类型变量：索引类型、列类型，以及一个或多个通过 NumPy 类型指定的列值类型。通用的 `Series` 需要两个类型变量：索引类型和用于值的 NumPy 类型。`Index` 本身是通用的，也需要一个作为类型变量的 NumPy 类型。

使用通用规格，浮动类型的 `Series`，由日期索引，可以注解为 `sf.Series[sf.IndexDate, np.float64]`。一个以日期作为索引标签，字符串作为列标签，整数和浮动类型作为列值的 `Frame` 可以注解为 `sf.Frame[sf.IndexDate, sf.Index[np.str_], np.int64, np.float64]`。

给定一个复杂的`Frame`，推导注解可能会很困难。StaticFrame 提供了 `via_type_clinic` 接口，在运行时为任何组件提供完整的通用规格：

```py
>>> v4 = sf.Frame.from_fields([range(5), np.arange(3, 8) * 0.5],
columns=('a', 'b'), index=sf.IndexDate.from_date_range('2021-12-30', '2022-01-03'))
>>> v4
<Frame>
<Index>         a       b         <<U1>
<IndexDate>
2021-12-30      0       1.5
2021-12-31      1       2.0
2022-01-01      2       2.5
2022-01-02      3       3.0
2022-01-03      4       3.5
<datetime64[D]> <int64> <float64>

# get a string representation of the annotation
>>> v4.via_type_clinic
Frame[IndexDate, Index[str_], int64, float64]
```

如同数组一样，将注解存储为类型别名可以实现重用并使函数签名更简洁。以下是一个新函数的定义，带有完全注解的通用 `Frame` 和 `Series` 参数。由于并非所有操作都能静态解析它们的返回类型，因此需要进行 `cast`。

```py
TFrameDateInts = sf.Frame[sf.IndexDate, sf.Index[np.str_], np.int64, np.int64]
TSeriesYMBool = sf.Series[sf.IndexYearMonth, np.bool_]
TSeriesDFloat = sf.Series[sf.IndexDate, np.float64]

def process5(v: TFrameDateInts, q: TSeriesYMBool) -> TSeriesDFloat:
    t = v.index.iter_label().apply(lambda l: q[l.astype('datetime64[M]')]) # type: ignore
    s = np.where(t, 0.5, 0.25)
    return cast(TSeriesDFloat, (v.via_T * s).mean(axis=1))
```

这些更复杂的注解接口也可以通过 `mypy` 进行验证。下面，传入了一个没有预期列值类型的 `Frame`，导致 `mypy` 报错（如下面的注释所示）。

```py
TFrameDateIntFloat = sf.Frame[sf.IndexDate, sf.Index[np.str_], np.int64, np.float64]
v5: TFrameDateIntFloat = sf.Frame.from_fields([range(5), np.arange(3, 8) * 0.5],
columns=('a', 'b'), index=sf.IndexDate.from_date_range('2021-12-30', '2022-01-03'))

q: TSeriesYMBool = sf.Series([True, False],
index=sf.IndexYearMonth.from_date_range('2021-12', '2022-01'))

x = process5(v5, q)
# tp.py: error: Argument 1 to "process5" has incompatible type
# "Frame[IndexDate, Index[str_], signedinteger[_64Bit], floating[_64Bit]]"; expected
# "Frame[IndexDate, Index[str_], signedinteger[_64Bit], signedinteger[_64Bit]]"  [arg-type]
```

为了在运行时验证使用相同的类型提示，可以应用 `sf.CallGuard.check` 装饰器。以下提供了一个包含三列整数的 `Frame`，而预期的是一个包含两列的 `Frame`。

```py
# a Frame of three columns of integers
TFrameDateIntIntInt = sf.Frame[sf.IndexDate, sf.Index[np.str_], np.int64, np.int64, np.int64]
v6: TFrameDateIntIntInt = sf.Frame.from_fields([range(5), range(3, 8), range(1, 6)],
columns=('a', 'b', 'c'), index=sf.IndexDate.from_date_range('2021-12-30', '2022-01-03'))

x = process5(v6, q)
# static_frame.core.type_clinic.ClinicError:
# In args of (v: Frame[IndexDate, Index[str_], signedinteger[_64Bit], signedinteger[_64Bit]],
# q: Series[IndexYearMonth, bool_]) -> Series[IndexDate, float64]
# └── Frame[IndexDate, Index[str_], signedinteger[_64Bit], signedinteger[_64Bit]]
#     └── Expected Frame has 2 dtype, provided Frame has 3 dtype
```

为每一列的每个 `Frame` 注解可能不实际：接口通常需要处理具有可变列数的 `Frame`。`TypeVarTuple` 通过使用 `*tuple[]` 表达式（在 Python 3.11 中引入，并通过 `Unpack` 注解向后移植）来支持这一点。例如，上面的函数可以定义为接受任意数量的整数列，注解为 `Frame[IndexDate, Index[np.str_], *tuple[np.int64, ...]]`，其中 `*tuple[np.int64, ...]]` 表示零个或多个整数列。

相同的实现可以用更通用的列类型规范进行注解。下面，列值用 `np.number[Any]` 注解（允许任何类型的数值 NumPy 类型）和 `*tuple[]` 表达式注解（允许任意数量的列）：`*tuple[np.number[Any], …]`。现在，既不会有 `mypy` 错误，也不会有 `CallGuard` 错误。

```py
TFrameDateNums = sf.Frame[sf.IndexDate, sf.Index[np.str_], *tuple[np.number[Any], ...]]

@sf.CallGuard.check
def process6(v: TFrameDateNums, q: TSeriesYMBool) -> TSeriesDFloat:
    t = v.index.iter_label().apply(lambda l: q[l.astype('datetime64[M]')]) # type: ignore
    s = np.where(t, 0.5, 0.25)
    return tp.cast(TSeriesDFloat, (v.via_T * s).mean(axis=1))

x = process6(v5, q) # a Frame with integer, float columns passes
x = process6(v6, q) # a Frame with three integer columns passes
```

与 NumPy 数组类似，`Frame` 注解可以将 `Require` 规范包装在 `Annotated` 泛型中，从而允许定义额外的运行时验证。

# 与其他库的类型注解

虽然 StaticFrame 可能是第一个提供完整泛型规范并为静态类型分析和运行时类型验证提供统一解决方案的 DataFrame 库，但其他数组和 DataFrame 库也提供了相关的工具。

PyTorch（2.4.0）中的 `Tensor` 类和 TensorFlow（2.17.0）中的 `Tensor` 类都不支持泛型类型或形状规范。虽然这两个库都提供了可以用于进行运行时类型和形状验证的 `TensorSpec` 对象，但像 `mypy` 这样的工具不支持静态类型检查。

从 Pandas 2.2.2 开始，Pandas 的 `Series` 和 `DataFrame` 都不支持泛型类型规范。一些第三方包提供了部分解决方案。例如，`pandas-stubs` 库为 Pandas API 提供了类型注解，但并未使 `Series` 或 `DataFrame` 类变成泛型。Pandera 库允许定义 `DataFrameSchema` 类，可以用于 Pandas DataFrame 的运行时验证。对于与 `mypy` 进行静态分析，Pandera 提供了替代的 `DataFrame` 和 `Series` 子类，允许使用相同的 `DataFrameSchema` 类进行泛型规范。该方法无法利用使用泛型 NumPy 类型或解包操作符提供变参泛型表达式的表达能力。

# 结论

Python 类型注解可以使静态类型分析成为检查代码质量的宝贵工具，能够在代码执行之前发现错误。直到最近，接口可能接受数组或 DataFrame，但无法指定这些容器中包含的类型。现在，NumPy 和 StaticFrame 中已可以完整地指定组件类型，从而允许更强大的类型静态分析。

提供正确的类型注解是一项投资。复用这些注解进行运行时检查提供了两全其美的效果。StaticFrame 的 `CallGuard` 运行时类型检查器专门用于正确评估完全指定的泛型 NumPy 类型，以及所有泛型 StaticFrame 容器。
