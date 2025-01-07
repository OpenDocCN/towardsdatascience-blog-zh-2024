# Python 和 Excel VBA 中的数组

> 原文：[https://towardsdatascience.com/arrays-in-python-and-excel-vba-efd73b649d6d?source=collection_archive---------14-----------------------#2024-01-23](https://towardsdatascience.com/arrays-in-python-and-excel-vba-efd73b649d6d?source=collection_archive---------14-----------------------#2024-01-23)

## 通过简单的例子学习数组

[](https://medium.com/@himalaya.birshrestha?source=post_page---byline--efd73b649d6d--------------------------------)[![Himalaya Bir Shrestha](../Images/9766140c1c44381029d0a78154217775.png)](https://medium.com/@himalaya.birshrestha?source=post_page---byline--efd73b649d6d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--efd73b649d6d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--efd73b649d6d--------------------------------) [Himalaya Bir Shrestha](https://medium.com/@himalaya.birshrestha?source=post_page---byline--efd73b649d6d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--efd73b649d6d--------------------------------) ·阅读时间 8 分钟·2024 年 1 月 23 日

--

作为一个没有接受过正式编程教育的人，我的编程旅程一直由自学塑造。意识到回顾基础编程概念的重要性，我发现扎实的基础能够提升整体编程体验。在本教程中，我们将深入探讨一个基本概念——数组。具体来说，我们将通过简单的例子，探讨 Python 和 Excel VBA 中的数组概念。让我们开始吧。

![](../Images/f66043ed20c7b741ea145dcadfe8031d.png)

图片由 [Nathan Dumlao](https://unsplash.com/@nate_dumlao?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## **1\. Python 中的数组**

数组是一个特殊的变量，可以保存一个或多个任何数据类型的值。在 Python 中，与类似的数据类型（如列表）不同，[没有内置的数组支持](https://www.w3schools.com/python/python_arrays.asp)。然而，可以使用 [numpy](https://numpy.org/doc/stable/index.html) 包的 array 模块来创建数组。numpy 数组对象的索引总是从 0 开始。可以通过引用 -1 来访问 numpy 数组中的最后一个项。一个 numpy 数组可以包含某一特定数据类型的变量或多种数据类型。

下面的代码片段展示了这一点。代码片段还展示了如何从 numpy 数组中访问形状（维度，即行、列）、大小（元素个数）和长度（容器中的项目数量，即行数）。

```py
import numpy as np

simple_array = np.array([1, 2, 3])
mixed_array = np.array([1, 2, 3, "a", "b", "c", 4.5])
print ("Simple array: ", simple_array)
print ("First element of simple_array: ", simple_array[0])
print ("Shape of simple_array: ", simple_array.shape)
print ("Size of simple_array; ", simple_array.size)
print ("\n")
print ("Mixed array: ", mixed_array)
print ("Last element of mixed_array: ", mixed_array[-1])
print ("Length of mixed_array: ", len(mixed_array))
```

**1.1 使用 numpy 数组进行代数矩阵运算**

由于其灵活的结构，numpy 数组在创建不同维度的矩阵对象并对其进行操作时非常方便。上面的截图展示了 1 维数组对象的例子。

在下面，我创建了两个数组对象 `a` 和 `b`，它们都是二维数组，可以看作是 2*2 的矩阵。计算这两个矩阵的点积就像执行 `np.dot(a, b)` 一样简单。在点积中，`a` 和 `b` 被视为向量（既有大小又有方向的对象）。在矩阵乘法中，矩阵 `a` 中的每个元素与矩阵 `b` 中对应的元素相乘。例如，a11（第一行第一列的元素）与 b11 相乘，以此类推。

```py
a = np.array([[0, 1],[2,3]])
b = np.array([[3,4],[5,6]])
print ("Dot Product of a and b: \n", np.dot(a,b))
print ("Matrix multiplication of a and b \n",a*b)
```

此外，还可以执行其他矩阵操作，如加法、减法和转置。要获得矩阵的行列式，可以使用 `np.linalg.det(a)`。要获得矩阵的乘法逆，可以使用 `np.linalg.inv(a)`。

```py
print (“Addition of a and b:\n”, np.add(a, b))
print ("Also addition of a and b:\n", a+b)
print ("Transpose of a:\n", a.T)
print ("Determinant of a:\n", np.linalg.det(a))
print ("Inverse of a:\n", np.linalg.inv(a))
```

**1.2 从列表对象创建 m*n 形状的 numpy 数组**

我有两个列表，分别叫做 `countries_lived` 和 `capitals`，它们包含我曾居住过的国家及其对应的首都。

```py
countries_lived = [“Nepal”,”India”,”Germany”,”Netherlands”]
capitals = [“Kathmandu”,”New Delhi”,”Berlin”,”Amsterdam”]
```

要创建一个包含这些列表对象的数组，我可以使用 `np.array([countries_lived, capitals])`。这将返回一个形状为 2*4（即 2 行 4 列）的数组。如果我希望每一行包含一个国家及其对应的首都，我只需转置该数组即可。

```py
array1 = np.array([countries_lived, capitals])
print ("array1:\n", array1)
print ("Shape of array1:\n", array1.shape)
print ("Size of array1:\n", array1.size)

array2 = np.array([countries_lived, capitals]).T
print ("array2:\n", array2)
print ("Shape of array2:\n", array2.shape)
print ("Size of array2:\n", array2.size)
```

## 1.3 向 numpy 数组追加一个元素并创建一个 dataframe

比如说，我想将 France 和 Paris 作为新的一行追加到 `array2` 中，可以使用语法 `np.append(arr, values, axis = None)` 来实现。values 必须与 `arr` 具有相同的形状，轴（axis）除外。如果未指定轴，arr 和 values 会在使用之前被展平。

如下所示，我将新元素作为新的一行追加到数组中。最后，形状为 (5,2) 的 `array2` 被用来创建一个包含 Country 和 Capital 列的数据框对象 `df`。

```py
array2 = np.append(array2, [[“France”,”Paris”]], axis = 0)
print ("array2 after appening new row: \n", array2)

import pandas as pd

df = pd.DataFrame(array2,
                 columns = ["Country", "Capital"])

df
```

## 2. Excel VBA 中的数组

与 Python 类似，Excel VBA 中的数组也是一组变量。数组的下界可以从 0 或 1 开始，Excel VBA 的默认下界是 0。但是，可以通过在每个模块顶部声明 `Option Base 0` 或 `Option Base 1` 来指定数组的下界。

要检测数组的下界和上界，可以分别使用 `Lbound(array_name)` 和 `Ubound(array_name)`。

**2.1 声明数组**

数组可以通过使用 `Public` 关键字声明为公共（即全局）数组。在 Excel VBA 中将数组或任何其他变量声明为公共变量，允许在任何模块或子程序中使用，无需重新声明。

```py
Public countries(1 to 4) as String
Public capitals(4) as String
Public countries_visited() as String
```

另外，数组也可以在子程序内局部声明，只需使用 `Dim` 关键字即可。这样声明的数组只能在特定的子程序内部使用。

```py
Dim countries(1 to 4) as String
Dim capitals(4) as String
```

在上述示例中，数组的大小也被指定。指定1到4或仅指定4都表示数组的大小为4。

**2.2 一维数组**

一维数组是通过声明行数（例如，从1到5），即数组包含的元素数量来赋值的。下面给出了一个创建我曾经居住过的四个国家的一维数组的示例。它将把这些国家的名称打印到Excel文件工作表的A列中。

```py
Option Base 1

Sub array_1d()

countries(1) = "Nepal"
countries(2) = "India"
countries(3) = "Germany"
countries(4) = "Netherlands"

Dim i As Integer
Range("A1").Value = "Country"

For i = 1 To 4
    Range("A" & i + 1).Value = countries(i)
Next i

End Sub
```

运行`array_1d`子程序的输出如下：

![](../Images/98813e9fa922046d745c6bd04c89ee36.png)

array_1d子程序的输出。图片来源：作者。

**2.2 二维数组**

二维数组通过声明行数和列数来定义。在以下示例中，我声明了一个名为`country_capital`的二维数组。每一行的第一个元素对应上一节中声明的`countries`数组中的元素。每一行的第二个元素对应于这些国家的首都，它们已经在下面的代码中单独声明。

```py
Sub array_2d()

Dim country_capital(4, 2) As String

For i = 1 To 4
    country_capital(i, 1) = countries(i)
Next i

country_capital(1, 2) = "Kathmandu"
country_capital(2, 2) = "New Delhi"
country_capital(3, 2) = "Berlin"
country_capital(4, 2) = "Amsterdam"

Range("B1").Value = "Capital"

For i = 1 To 4
    Range("A" & i + 1).Value = country_capital(i, 1)
    Range("B" & i + 1).Value = country_capital(i, 2)

Next i

End Sub
```

运行此子程序返回以下结果：

![](../Images/bf21b342e5a6abf274ac8e8deed484e9.png)

array_2d子程序的输出。图片来源：作者。

**2.3 动态数组**

动态数组在无法确定数组大小并且数组大小可能在未来发生变化的情况下非常有用。在下面的代码中，我声明了两个数组`countries_visited`和`population`，但没有指定数组的大小。在`dynamic_array`子程序内，我通过使用`ReDim`语句将这两个数组的大小指定为4。接下来，我根据我访问过的四个国家及其人口分别指定了数组的每个元素。

```py
Option Base 1

Public countries_visited() As String
Public population() As Long

Sub dynamic_array()

Dim wb As Workbook
Dim ws2 As Worksheet
Set wb = ThisWorkbook
Set ws2 = wb.Worksheets("Sheet2")

ReDim countries_visisted(4)
ReDim population(4)

countries_visited(1) = "France"
population(1) = 68

countries_visited(2) = "Spain"
population(2) = 48

countries_visited(3) = "Iran"
population(3) = 88

countries_visited(4) = "Indonesia"
population(4) = 274

End Sub 
```

一段时间后，我意识到我还访问了一个新国家（葡萄牙）。我在保留这些数组原始内容/元素的情况下重新定义了数组的大小。我通过增加数组的大小1来进行操作。为此，我使用了`ReDim Preserve`语句，如下所示。

```py
ReDim Preserve countries_visited(1 to 5)
ReDim Preserve population(1 to 5)
```

完整代码如下：

```py
Option Base 1

Public countries_visited() As String
Public population() As Long

Sub dynamic_array()

Dim wb As Workbook
Dim ws2 As Worksheet
Set wb = ThisWorkbook
Set ws2 = wb.Worksheets("Sheet2")

ReDim countries_visisted(4)
ReDim population(4)

countries_visited(1) = "France"
population(1) = 68

countries_visited(2) = "Spain"
population(2) = 48

countries_visited(3) = "Iran"
population(3) = 88

countries_visited(4) = "Indonesia"
population(4) = 274

ws2.Range("A1").Value = "Countries visited"
ws2.Range("B1").Value = "Population (million)"

ReDim Preserve countries_visited(5)
ReDim Preserve population(5)

countries_visited(5) = "Portugal"
population(5) = 10

Dim i As Integer
For i = 2 To 6
    Range("A" & i).Value = countries_visited(i - 1)
    Range("B" & i).Value = population(i - 1)

Next i

End Sub
```

上述代码的输出如下所示：

![](../Images/53b0ee04a2d02ab0aaa51c25d3e5f899.png)

动态数组子程序的输出。图片来源：作者。

**2.4 声明数组以存储不同数据类型的变量**

在上面的章节中，`countries_visited`数组声明用于存储*String*数据类型的变量，`population`数组声明用于存储*Long*数据类型的变量。与Python的numpy数组类似，在Excel VBA中也可以在数组中存储不同数据类型的变量。在这种情况下，数组必须声明为`Variant`类型。

在下面的示例中，声明了一个名为`test`的数组作为*Variant*。其大小通过`ReDim`语句指定为3。`test`中的三个元素分别为*String*、*Integer*和*Date*类型。通过将变量传递给`TypeName()`函数，可以识别这些数据类型。

```py
Option Base 0

Sub variant_test()

Dim test() As Variant
ReDim test(3)

test = Array("Germany population in million: ", 83, Date)

Dim i As Integer
For i = 0 To 2 
     Debug.Print "Element " & i & " of test array is: " & test(i) & " of type " & TypeName(test(i))
Next i

End Sub
```

输出如下所示：

![](../Images/11ecf26f56ad68da974831b0b645b8b2.png)

variant_test 子程序的输出。图片由作者提供。

## 结论

数组是由一个或多个数据类型的值/变量组成的集合。每个变量都与数组中的特定索引号相关联。数组可以是单维的、二维的或多维的。在Python中，没有内建的数组支持，但可以使用numpy包创建数组。除了存储值，numpy数组在进行矩阵运算时也非常有用。在Excel VBA中，数组在处理大型数据元素数据库时非常有用。在Excel VBA中，数组可以是静态的，即数组的大小是预定义的。或者，数组也可以是动态的，即数组的大小不是预定义的，但我们可以在使用过程中指定其大小，甚至在保持已存储元素的情况下调整数组大小。

这个 [GitHub 仓库](https://github.com/hbshrestha/Data_Analytics)提供了Python笔记本、Excel工作簿以及VBA脚本。感谢阅读！
