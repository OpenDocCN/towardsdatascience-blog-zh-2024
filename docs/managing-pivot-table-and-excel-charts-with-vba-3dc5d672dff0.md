# 使用VBA管理数据透视表和Excel图表

> 原文：[https://towardsdatascience.com/managing-pivot-table-and-excel-charts-with-vba-3dc5d672dff0?source=collection_archive---------12-----------------------#2024-06-18](https://towardsdatascience.com/managing-pivot-table-and-excel-charts-with-vba-3dc5d672dff0?source=collection_archive---------12-----------------------#2024-06-18)

## 通过使用VBA自动化数据透视表和图表的操作，节省宝贵的时间

[](https://medium.com/@himalaya.birshrestha?source=post_page---byline--3dc5d672dff0--------------------------------)[![Himalaya Bir Shrestha](../Images/9766140c1c44381029d0a78154217775.png)](https://medium.com/@himalaya.birshrestha?source=post_page---byline--3dc5d672dff0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3dc5d672dff0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3dc5d672dff0--------------------------------) [Himalaya Bir Shrestha](https://medium.com/@himalaya.birshrestha?source=post_page---byline--3dc5d672dff0--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3dc5d672dff0--------------------------------) ·9分钟阅读·2024年6月18日

--

随着数字化和创新的推进，处理大数据集已经成为大学、研究机构和各行各业的常态。自动化常见的任务可以为分析师节省数小时的时间，让他们能花更多的时间分析数据并生成洞察，而不是处理和管理数据。

数据透视表就是这样一个工具，它允许从堆叠的数据集中生成多个见解。只需根据需求调整数据透视表的字段设置，就能进行大量的分析。如果有人有许多堆叠的数据集，可以将其转换为数据透视表，那么如何让这些数据透视表自动刷新呢？如何根据系列名称自动选择图表颜色并管理其他图表元素？这篇博客通过一步步的示例给出了这些问题的答案。

虽然在开始时设置代码可能需要相当大的努力，但一旦设置完成，它将变得非常方便且节省时间，特别是对那些每天处理大量数据集的分析师们来说。让我们开始吧。

![](../Images/f4ecc8bd15caabeef8b391591514fc84.png)

图片来自[Norbert Levajsics](https://unsplash.com/@levajsics?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 数据

我从[Excel-Easy](https://www.excel-easy.com/data-analysis/pivot-tables.html)网站上获取了示例数据。这个网站包含了适合初学者的Excel和VBA功能教程。数据集包含了2016年和2017年特定国家的水果和蔬菜销售记录。数据集包含六个字段：订单ID、产品、类别、金额、日期和国家。我将这个数据集分为两个CSV文件，分别命名为`results1.csv`和`results2.csv`，用于创建数据透视表。

![](../Images/f5bf4bd47288e4652e7b60d3b12e1dd7.png)

数据集结构。图示由作者提供。

我在`RawData`工作表中提取了这个数据集。我创建了一个动态范围，名为`raw_data_source`，其公式如下：

**=OFFSET(RawData!$A$1, 0, 0, COUNTA(RawData!$A:$A), COUNTA(RawData!$1:$1))**

创建动态范围的原因是，数据集的大小（行数）在不同的CSV文件中不同，我希望使用整个数据集作为数据透视表的源。

![](../Images/787818780f73d2439d1ba882f8f9a452.png)

创建动态命名范围作为数据透视表的数据源。图示由作者提供。

## 数据透视表和图表

`RawData`工作表中的`raw_data_source`范围的数据集被用作数据源，创建了两个简单的数据透视表和图表。

![](../Images/afe820de943480d30e9dd8eb9d2c2e50.png)

指定数据透视表的数据源。图示由作者提供。

第一个图表展示了按产品过滤的各国水果和蔬菜总销售额，字段设置如下所示。

![](../Images/3ce903eacb27581bcafc363ed7fc36d4.png)

第一个数据透视图及其设置。图示由作者提供。

第二个图表展示了各国的水果和蔬菜总销售额。

![](../Images/db3162aae0e2636485d4dfa74618719c.png)

第二个数据透视图及其设置。图示由作者提供。

## 1. 基于新数据集自动刷新数据透视表

在这一步，我想要自动刷新来自我指定文件的数据集。我在`Admin`工作表中创建了一个占位符，用于放置我想要提取并刷新数据透视表的CSV数据集的路径。我将单元格`A2`命名为`filepath`，如下所示：

![](../Images/d18a0b9e83d709020bfa5db1e8f8755a.png)

数据集文件路径占位符。图示由作者提供。

此步骤的代码在`UpdateRawData`子程序中给出。我声明了变量`wb`、`ws_admin`、`ws_rawdata`和`filepath`，分别代表工作簿、Admin工作表、RawData工作表和包含数据集的文件路径。首先，我清空了RawData工作表的内容。然后，我根据文件名进入源工作簿，选择相应的工作表，复制其内容，返回`ws_rawdata`工作表，并将内容粘贴为值。最后，我使用`wb.RefreshAll`代码刷新了工作簿，刷新了包括数据透视表和图表的整个工作簿。

```py
Sub UpdateRawData()

Dim wb As Workbook
Dim ws_admin As Worksheet
Dim ws_rawdata As Worksheet
Dim filepath As String

Set wb = ThisWorkbook
Set ws_admin = wb.Worksheets(“Admin”)
Set ws_rawdata = wb.Worksheets(“RawData”)

'Clear Rawdata sheet
ws_rawdata.Activate
Cells.Clear

'get file path
filepath = ws_admin.Range(“filepath”)

Application.DisplayAlerts = False

'Open source file and select all contents
Dim src_wb As Workbook
Dim src_ws As Worksheet

Set src_wb = Workbooks.Open(filepath)
Set src_ws = src_wb.Sheets(1)
src_ws.UsedRange.Select

'Copy all
Selection.Copy

'Paste all
ws_rawdata.Range(“A1”).PasteSpecial xlPasteValues

'Close source file
src_wb.Close SaveChanges:=False

wb.RefreshAll

Application.DisplayAlerts = True

End Sub
```

我将这个子程序链接到**刷新文件和图表**按钮。一旦我在占位符中指定了文件名并点击按钮，数据集和数据透视表就会自动更新。

## 2\. 使用VBA创建颜色表

我在`Admin`工作表中创建了一个包含数据集中具体水果和蔬菜列表的表格。在F列中，我指定了每种水果或蔬菜的初步十六进制颜色代码。我希望使用这些颜色来更新数据透视图中的颜色。首先，我希望用每个单元格中指定的颜色填充F列。

![](../Images/cef0874d1ee3959d227d299d7955504e.png)

创建数据集中可用的水果和蔬菜列表，以及它们对应的十六进制颜色代码。图示由作者提供。

## 十六进制颜色代码

每种颜色的十六进制代码是一个6位的十六进制数（0到9或A到F），以16为基数。在十六进制颜色代码`RRGGBB`中，每一对两个字母表示红色、绿色和蓝色的不同色调。在RGB（红绿蓝）系统中，每种色调的值范围从0到255。

例如，对于十六进制颜色代码`ffab23`，我通过以下计算得出相应的RGB代码。十六进制代码`ffab23`在十六进制系统中转换为（255, 171, 35），即代表红色、绿色和蓝色的色彩组件。

![](../Images/4e0c4c79ddb75cf914723fe4accf4610.png)

手动计算十六进制颜色代码ffab23的RGB值。图示由作者提供。

这也可以通过以下方式在Excel中的“自定义颜色”选项中进行可视化：

![](../Images/6aaf8d00e234ed7ed48d7edb7a2a7d8a.png)

演示十六进制颜色代码ffab23的RGB组件和实际颜色。图示由作者提供。

在Excel VBA中，使用`**&H**`与一个值组合意味着它是一个十六进制数，而`Val()`函数返回相应的十进制数。在下面的立即窗口中，`r`、`g`和`b`分别代表红色、绿色和蓝色的相应十进制值。

![](../Images/cb9331018b2f160b63822fa597e82fc8.png)

立即窗口显示了如何从十六进制代码在VBA中推导出相应的十进制值。图示由作者提供。

在下面的代码中，我为包含每种水果或蔬菜的十六进制颜色代码的表格创建了一个命名范围`color_code_range`。我遍历了选择中的每个单元格，推导出红、绿、蓝组件的十进制值，并用相同的RGB颜色代码填充单元格的内部。

```py
Sub refresh_color_table()

Dim wb As Workbook
Dim ws_admin As Worksheet

Set wb = ThisWorkbook
Set ws_admin = wb.Sheets(“Admin”)
ws_admin.Range("color_code_range").Select

Dim c As Range
Dim r, g, b As Long
Dim Hex As String

For Each c In Selection

     Hex = c.Value

     r = Val("&H" & Mid(Hex, 1, 2))
     g = Val("&H" & Mid(Hex, 3, 2))
     b = Val("&H" & Mid(Hex, 5, 2))
     c.Interior.Color = RGB(r, g, b)

Next c

End Sub
```

当我运行上述子程序时，F列会被涂上与颜色代码相同的颜色，如下所示：

![](../Images/c271a6cf5daa5b7f63d663750f671282.png)

用上述颜色代码填充单元格。图示由作者提供。

如果更改了颜色代码，并且再次运行代码，它将在表格中生成新的颜色。是不是很酷？

## 3. 在VBA中使用字典

在下一步中，我希望根据我之前选择的自定义颜色为数据透视图表分配颜色。为此，我创建了一个字典，包含产品名称作为键，十六进制颜色代码作为值。

在VBA中创建字典对象的前提条件是首先启用Microsoft Scripting Runtime。为此，您可以进入工具 -> 引用 -> 勾选Microsoft Scripting Runtime旁边的框并点击确定。

![](../Images/1bbd6b917235f828e43963dc49cd8128.png)

在Excel VBA中使用字典的前提条件。图示由作者提供。

在下面的代码中，我创建了一个名为`colorMap`的字典对象。我遍历了`Admin`工作表中E2:F10的范围，将E列的内容作为键，F列的内容作为相应的值。

```py
Sub create_dict()

Dim wb As Workbook
Dim ws_admin As Worksheet

Set wb = ThisWorkbook
Set ws_admin = wb.Sheets(“Admin”)

Dim colorMap As Dictionary
Set colorMap = New Dictionary

Dim i As Integer

For i = 2 To 10
    If Not colorMap.Exists(Range("E" & i).Value) Then
        colorMap.Add Range("E" & i).Value, Range("F" & i).Value
    End If
Next i

For Each Key In colorMap.Keys()
     Debug.Print Key & ": " & colorMap(Key)
Next Key

End Sub
```

在上面的第二个for循环中，我遍历了colorMap字典的每个键，并在即时窗口中打印了键和值，如下所示：

![](../Images/a30fc8c8a5eeefcdc4e7761894af41bc.png)

打印colorMap字典对象的内容。图示由作者提供。

## 4. 使用VBA管理数据透视图表元素

基于之前步骤中的学习，我想更进一步，使用VBA更新数据透视图表元素。在这种情况下，我希望根据单元格的值自动设置图表标题，并将`Admin`工作表中定义的特定水果和蔬菜的颜色应用到数据透视图表中。

在这一步中，我将`Plot1`和`Plot2`工作表分配给一个名为`sheetNames`的数组。我声明了`chartObj`为ChartObject。在每个工作表中，我遍历了所有ChartObjects中的每个ChartObject。

**注意：** [ChartObject](https://learn.microsoft.com/en-us/office/vba/api/excel.chartobject)是VBA中用于控制嵌入图表在工作表中大小和外观的[Chart](https://learn.microsoft.com/en-us/office/vba/api/excel.chart(object))对象的容器。它是[ChartObjects](https://learn.microsoft.com/en-us/office/vba/api/excel.chartobjects)集合的成员。理解每个对象在VBA中的方法和属性差异是非常重要的。

遍历每个chartObj之后，我根据E1单元格中的值为每个图表设置标题。接下来，我遍历Chart对象的整体Series Collection中的每个系列。我将系列名称（即水果或蔬菜的名称）分配给名为`itemName`的变量，并从`colorMap`字典中获取相应的颜色代码。与步骤2类似，我获取了颜色代码的红、绿、蓝分量的十进制数值，并用RGB颜色填充了系列条形。

```py
Sub refresh_plots()

Dim wb As Workbook
Dim ws_admin As Worksheet
Dim ws As Worksheet

Set wb = ThisWorkbook
Set ws_admin = wb.Sheets("Admin")

Dim colorMap
Set colorMap = CreateObject("Scripting.Dictionary")
Dim i As Integer

For i = 2 To 10
     If Not colorMap.Exists(Range("E" & i).Value) Then
          colorMap.Add Range("E" & i).Value, Range("F" & i).Value
     End If
Next i

Dim sheetNames As Variant
Dim sheetName As Variant
sheetNames = Array("Plot1", "Plot2")

Dim hex_color_code As String
Dim r, g, b As Integer

Dim chartObj As ChartObject

For Each sheetName In sheetNames
     Set ws = wb.Sheets(sheetName)

     For Each chartObj In ws.ChartObjects

          chartObj.Chart.HasTitle = True
          chartObj.Chart.ChartTitle.Text = ws.Range("E1").Value

          For Each Series In chartObj.Chart.SeriesCollection

               itemName = Series.Name
               hex_color_code = colorMap(itemName)

               r = Val("&H" & Mid(hex_color_code, 1, 2))
               g = Val("&H" & Mid(hex_color_code, 3, 2))
               b = Val("&H" & Mid(hex_color_code, 5, 2))
               Series.Format.Fill.ForeColor.RGB = RGB(r, g, b)

          Next Series
     Next chartObj
Next sheetName

End Sub
```

下面展示了使用此代码的示意图。

![](../Images/cdfe81230c6b410e79176524e22ccd66.png)

使用VBA代码转换数据透视图表元素。图示由作者提供。

## 结论

在本文中，我阐述了如何使用VBA自定义和自动化处理数据透视表和图表。我展示了四个关键任务的自动化：用新数据集刷新数据透视表和图表；根据十六进制颜色代码创建颜色表；如何在VBA中使用字典；以及如何使用VBA管理和更新数据透视图表元素。在第二步中，我详细讲解了如何使用Excel和VBA将十六进制颜色代码转换为相应的RGB颜色代码，并在随后的步骤中使用了这一技术。

本文的代码和Excel文件可以在这个GitHub [仓库](https://github.com/hbshrestha/Data_Analytics)中找到。感谢您的阅读！
