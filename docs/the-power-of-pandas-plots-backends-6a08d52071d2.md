# Pandas 图表的威力：后端

> 原文：[https://towardsdatascience.com/the-power-of-pandas-plots-backends-6a08d52071d2?source=collection_archive---------9-----------------------#2024-08-30](https://towardsdatascience.com/the-power-of-pandas-plots-backends-6a08d52071d2?source=collection_archive---------9-----------------------#2024-08-30)

## 从 Pandas 中轻松创建交互式图形

[](https://medium.com/@petoulemonde?source=post_page---byline--6a08d52071d2--------------------------------)[![Pierre-Etienne Toulemonde](../Images/95fdda47e79e3c194fc96ef29f3a2188.png)](https://medium.com/@petoulemonde?source=post_page---byline--6a08d52071d2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6a08d52071d2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6a08d52071d2--------------------------------) [Pierre-Etienne Toulemonde](https://medium.com/@petoulemonde?source=post_page---byline--6a08d52071d2--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6a08d52071d2--------------------------------) ·5分钟阅读·2024年8月30日

--

![](../Images/d5b796700b79a971847f9d847e9373d9.png)

图片由[Lukas Blazek](https://unsplash.com/@goumbik?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 索引

1.  上下文

1.  后端

    2.1\. Matplotlib

    2.2\. Plotly

    2.3\. Hvplot

1.  讨论

1.  离开之前的一句话

# 1\. **上下文**

Python 有许多可视化包，其中三个最著名的是：[Matplotlib](https://matplotlib.org/)（以及 seaborn）、[Plotly](https://plotly.com/) 和 [Hvplot](https://hvplot.holoviz.org/)。这三种包各有其优点，但都需要付出学习成本，甚至有时相当可观。

本文的灵感来源于我发现的[《Pandas 方法思维导图》](https://blog.dailydoseofds.com/p/interactive-mind-map-of-all-pandas)，该导图由[Daily Dose of Data science newsletter](https://blog.dailydoseofds.com/)提供（这是我强烈推荐的一个通讯）。当时，我正好在探索Hvplot可视化包。我认为，像Hvplot这样轻松切换不同可视化后端的想法非常棒（[这里](https://hvplot.holoviz.org/user_guide/Plotting_with_Plotly.html)有一个示例，展示如何从Hvplot切换到Plotly）。看到我们也可以用pandas实现这一点，我觉得这个想法太有趣，不能不分享出来。

Pandas 是 Python 数据科学的核心，我们都知道如何使用它。但集成到 Pandas 中的 Matplotlib 已经显得有些老旧，并且在易用性和展示效果上被其他包超越。Pandas 可视化后端的强大之处在于，它让你能够利用最新的可视化包进行数据探索和结果呈现，而无需花时间学习这些包，尽管这些包非常强大！

# **2\. 后端**

Pandas 是基于两个包构建的，Numpy 和 Matplotlib。这也解释了为什么我们使用 Matplotlib 脚本来生成图表，因此生成的图表是 Matplotlib 图表。

自从 Pandas 创建以来，它不断发展，并提供了用户修改 Pandas 使用的可视化后端的可能性。

我在研究过程中找到的 6 个可用的后端是：

+   Plotnine（ggplot2）

+   Plotly

+   Altair

+   Holoviews

+   Hvplot

+   Pandas_bokeh

+   Matplotlib（默认后端）

有几种方法可以更改后端：

+   2 个全局方法：

```py
pd.set_option("plotting.backend", '<name of backend>')
# OR
pd.options.plotting.backend = '<name of backend>' 
```

+   1 个本地方法：

```py
df.plot(backend='<name of backend>', x='...')
```

注意：更改后端需要 Pandas >= 0.25，有时还需要一些特定的依赖项，例如下面的 Hvplot。

这里有 2 个示例：

+   使用 Plotly：

```py
import pandas as pd # Basic packages

pd.options.plotting.backend = "plotly"

df = pd.DataFrame(dict(a=[1,3,2], b=[3,2,1]))
fig = df.plot()
fig.show()
```

+   使用 Hvplot：

```py
import numpy as np
import pandas as pd # Basic packages

import hvplot
import hvplot.pandas # ! Specific dependency to install

pd.options.plotting.backend = 'hvplot' # Backend modification

data = np.random.normal(size=[50, 2])
df = pd.DataFrame(data, columns=['x', 'y'])

df.plot(kind='scatter', x='x', y='y') # Plotting
```

## **2.1\. Matplotlib**

[Matplotlib](https://matplotlib.org/) 是 Pandas 的默认可视化后端。换句话说，如果不指定后端，将使用 Matplotlib。它是一个高效的包，可以快速可视化数据以进行探索或提取结果，但它已经老化，并且在易用性和渲染能力上被其他包赶超。

Matplotlib 的优势在于，由于 Pandas 从创建之初就基于 Matplotlib，因此 Matplotlib 与 Pandas 的集成非常完美，所有 Matplotlib 函数都可以在 Pandas 中使用。

提醒一下，这里是集成到 Pandas 中的 11 个 Matplotlib 显示方法：

+   “area” 用于区域图，

+   “bar” 用于垂直条形图，

+   “barh” 用于水平条形图，

+   “box” 用于箱形图，

+   “hexbin” 用于 hexbin 图，

+   “hist” 用于直方图，

+   “kde” 用于核密度估计图，

+   “density” 是 “kde” 的别名，

+   “line” 用于折线图，

+   “pie” 用于饼图，

+   “scatter” 用于散点图。

## **2.2\. Plotly**

[Plotly](https://plotly.com/) 是由 Plotly 公司开发的可视化包。该公司开发了框架 Plotly.js，用于在 Python 中进行交互式数据可视化。Plotly 公司还提供了 Python 仪表板包 [Dash](https://dash.plotly.com/)。

要在 Pandas 中使用 Plotly，只需导入 *Plotly express* 并更改后端：

```py
import pandas as pd
import plotly.express as px # Import packages

df = pd.read_csv("iris.csv")

# Modifying locally Pandas backend
df.plot.scatter(backend = "plotly", x = "sepal.length", y = "sepal.width")
```

Pandas 返回一个与 Plotly 相同类型的对象：

```py
df.plot.scatter(backend = "plotly", x = "sepal.length", y = "sepal.width") 
# → <class 'plotly.graph_objs._figure.Figure'>

px.scatter(x=df["sepal.length"], y = df["sepal.width"]) 
# → <class 'plotly.graph_objs._figure.Figure'>
```

其优势在于你可以将 Pandas 中创建的图形直接集成到 Plotly 宇宙中，尤其是 Dash！

一个限制是，Plotly 与 Pandas 的集成尚不完美，详细信息请参见 Plotly 网站（[Plotly 网站详情](https://plotly.com/python/pandas-backend/)）。

## **2.3\. Hvplot**

[Hvplot](https://hvplot.holoviz.org/) 是一个基于 bokeh 的交互式可视化包。

这是一个令人兴奋的包，我是前些时候发现的，至今仍令我着迷，既因为 Hvplot 集成了类似于 Pandas 的后端概念，也因为 [Holoviz 套件](https://holoviz.org/) 以及相关包如 [Panel](https://panel.holoviz.org/) 用于创建动态客户端网站。

即使没有 Pandas 后端的概念，Hvplot 也不需要过多的学习就能开始使用，只需将 Pandas 的 *.plot()* 替换为 *.hvplot()*：

```py
import pandas as pd
import hvplot

df = pd.read_csv("iris.csv")

# Plot with Pandas
df.plot.scatter(backend = "hvplot", x = "sepal.length", y = "sepal.width") 

# Same plot with hvplot
df.hvplot.scatter(backend = "hvplot", x = "sepal.length", y = "sepal.width") 
```

使用 Hvplot 后端的方式与使用 Plotly 后端相同，只需要导入 Hvplot 包的依赖项：

```py
import numpy as np
import pandas as pd # Basic packages

import hvplot 
import hvplot.pandas # Specific dependency to install

pd.options.plotting.backend = 'hvplot' # Backend modification

data = np.random.normal(size=[50, 2])
df = pd.DataFrame(data, columns=['x', 'y'])

df.plot(kind='scatter', x='x', y='y') # Plotting
```

与 Plotly 类似，通过 hvplot 后端从 Pandas 生成的图表是 Hvplot 类型的：

```py
df.plot.scatter(backend = "hvplot", x = "sepal.length", y = "sepal.width") 
# → <class 'holoviews.element.chart.Curve'>

df.hvplot.scatter(backend = "hvplot", x = "sepal.length", y = "sepal.width") 
# → <class 'holoviews.element.chart.Curve'>
```

Hvplot 是强大的 Holoviz 套件的一部分，套件中还有许多其他相关工具，可以推动数据分析的深度，例如 [Panel](https://panel.holoviz.org/)、[geoviews](https://geoviews.org/)、[datashader](https://datashader.org/) 等等。这种一致性使得我们可以从 pandas 创建图表，同时仍然能够利用 Holoviz 套件的优势。

# **3. 结论**

Pandas 后端是一个非常高效的解决方案，可以发现并利用最新的 Python 可视化包，而无需投入太多时间：只需 18 个字符（包括空格），就可以将一个标准的 matplotlib 图表本地转化为交互式的 Plotly 图表，从而享受这种可视化类型的所有好处。

然而，这种解决方案也有一定的局限性：它不适用于那些需要大量定制化的高级可视化目标，比如数据新闻中的高级可视化，因为 Pandas 中的包集成尚不完善。此外，这个解决方案仅涵盖了基于 Pandas 构建的可视化包，排除了像 D3.js 这样的其他可视化解决方案。

Hvplot 目前是我最喜欢的可视化包：它一开始非常容易上手，能与所有主要的数据处理包（如 Polars、Dask、Xray 等）兼容，并且是一个持续应用的部分，可以让你从图表扩展到动态的全客户端网站。

# **4. 离开前的一句话**

在我的研究过程中，我没有找到预期的那么多文档。我认为这个概念很棒，所以我本以为会有很多相关文章。如果你觉得这个解决方案真的有用，或者只是一个看起来很酷但没有实际用途的东西，欢迎在评论中告诉我。

感谢阅读！
