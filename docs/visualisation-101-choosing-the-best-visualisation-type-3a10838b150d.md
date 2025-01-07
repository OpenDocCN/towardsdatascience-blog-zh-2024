# 可视化101：选择最佳的可视化类型

> 原文：[https://towardsdatascience.com/visualisation-101-choosing-the-best-visualisation-type-3a10838b150d?source=collection_archive---------0-----------------------#2024-01-12](https://towardsdatascience.com/visualisation-101-choosing-the-best-visualisation-type-3a10838b150d?source=collection_archive---------0-----------------------#2024-01-12)

## 不同可视化应用场景的综合指南

[](https://miptgirl.medium.com/?source=post_page---byline--3a10838b150d--------------------------------)[![Mariya Mansurova](../Images/b1dd377b0a1887db900cc5108bca8ea8.png)](https://miptgirl.medium.com/?source=post_page---byline--3a10838b150d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3a10838b150d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3a10838b150d--------------------------------) [Mariya Mansurova](https://miptgirl.medium.com/?source=post_page---byline--3a10838b150d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3a10838b150d--------------------------------) ·16分钟阅读·2024年1月12日

--

![](../Images/faf6d89696dd0f596ffa5a5ab4b2ced9.png)

图片来自DALL-E 3

我认为分析师的主要目标是帮助产品团队基于数据做出正确的决策。这意味着，分析师工作的主要结果不仅仅是得到一些数字或仪表盘，而是影响合理的数据驱动决策。因此，展示我们研究的结果是分析师日常工作中至关重要的一部分。

你是否曾经经历过，直到你创建了一个图表，才注意到某些显而易见的异常？你不是唯一的。几乎没有人能从枯燥的数字表格中提取出洞察力。这就是为什么我们需要可视化来揭示数据中的洞察。作为数据和产品团队之间的桥梁，数据分析师需要在可视化方面表现出色。

这就是为什么我想讨论数据可视化，并从框架开始，帮助你为你的应用场景选择最合适的图表类型。

# 为什么我们需要可视化？

仅通过总结性统计数据来查看数据可能会很有诱惑力。你可以通过均值和方差比较数据集，而根本不查看数据本身。然而，这可能会导致对数据的误解和错误的决策。

其中一个最著名的例子是[安斯科姆四重奏](https://en.wikipedia.org/wiki/Anscombe%27s_quartet)。它由统计学家弗朗西斯·安斯科姆创建，包含四个数据集，这些数据集的描述性统计几乎相同：均值、方差和相关性。然而，当我们查看数据时，我们可以看到这些数据集是多么不同。

![](../Images/93b7d87c94d12f2a4c9e101b4dc437ae.png)

作者提供的可视化

> 你可以在[这里](https://www.research.autodesk.com/publications/same-stats-different-graphs/)找到更多令人震惊的例子（甚至是恐龙），它们使用相同的描述性统计数据。

这个例子清晰地展示了异常值如何扭曲你的汇总统计数据，以及为什么我们需要对数据进行可视化。

除了异常值，视觉化还是呈现研究结果的更好方式。图表更易理解，并且能整合大量数据。因此，分析师应特别关注这一领域。

# 背景是起点

当我们开始为任务考虑可视化时，我们需要明确其主要目标或可视化的背景。

创建图表有两种重要的应用场景：探索性分析和解释性分析。

**探索性可视化**是你与数据的“私人对话”，用于发现洞察并理解其内部结构。对于这样的可视化，你可能不太关注设计和细节，例如省略标题或不在图表间使用一致的颜色方案，因为这些可视化只是供你个人查看。

我通常会先做一些快速的图表原型。然而，即便在这种情况下，你仍然需要考虑最合适的图表类型。恰当的可视化可以帮助你发现洞察，而错误的可视化可能隐藏线索。所以，做出明智的选择。

**解释性可视化**旨在向观众传达信息。在这种情况下，你需要更加关注细节和背景，以实现你的目标。

当我在进行解释性可视化时，我通常会思考以下问题，以明确我的目标：

+   我的观众是谁？他们有什么背景？我需要向他们解释什么信息？他们感兴趣的是什么？

+   我想达成什么目标？我的观众可能有哪些顾虑？我可以展示什么信息来实现我的目标？

+   我是否展示了全貌？我是否需要从另一个角度来看这个问题，以便为观众提供所有信息，帮助他们做出明智的决定？

此外，你对可视化的决定可能会取决于传播媒介，不管你是做现场演示，还是仅仅通过Slack或电子邮件发送。以下是一些例子：

+   在现场演示的情况下，你可以对图表做较少的注释，因为你可以谈论所有必要的背景信息，而在电子邮件中，最好提供所有的细节。

+   一个包含大量数字的表格不适合现场展示，因为信息过多的幻灯片可能会分散观众对演讲的注意力。而对于书面沟通来说，这是完全可以接受的，因为观众可以按自己的节奏浏览所有数字。

所以，在选择图表类型时，我们不应该孤立地考虑可视化。我们需要考虑我们的主要目标和观众，请牢记这一点。

# 可视化的感知

你知道多少种不同类型的图表？我敢打赌你能说出不少：线性图、条形图、桑基图、热力图、箱线图、气泡图等。但你有没有更细致地思考过可视化：它们的构建模块是什么？你的读者是如何感知这些图表的？

William S. Cleveland 和 Robert McGill 在他们的文章中调查了这个问题，[《Graphical Perception: Theory, Experimentation, and Application to the Development of Graphical Methods》](https://doi.org/10.2307/2288400)，发表于 *《美国统计学会杂志》*，1984年9月。这篇文章聚焦于视觉感知——解码图表中呈现信息的能力。作者们提出了一组可视化构建模块——视觉编码——例如，位置、长度、面积或颜色饱和度。不出所料，不同的视觉编码对人们的解读难度不同。

作者们尝试通过实验假设并测试人们从图表中提取信息的准确性，测试不同元素使用下，人们判断的有效性。他们的目标是验证人们判断的准确度。

他们通过以往的心理学研究和实验，对不同的可视化构建模块进行了排名，从最准确到最不准确。以下是这个列表：

+   位置——例如，散点图；

+   长度——例如，条形图；

+   方向或斜率——例如，线图；

+   角度——例如，饼图；

+   面积——例如，气泡图；

+   体积——3D 图表；

+   色相和饱和度——例如，热力图。

> 我只突出了文章中最常见的元素，适用于日常的分析任务。

如我们之前所讨论的，视觉化的主要目标是传达信息，我们需要关注我们的受众以及他们如何感知这个信息。因此，我们关注的是人们是否能够正确理解。这就是为什么我通常会尽量选择列表顶部的视觉编码，因为它们更容易为人们所解读。

# 可视化工具

我们将在下面看到许多图表示例，所以我们先快速讨论一下我用来制作图表的工具。

可视化有很多选择：

+   Excel 或 Google Sheet，

+   像 Tableau 或 Superset 这样的 BI 工具，

+   Python 或 R 中的库。

在大多数情况下，我更喜欢使用 Python 的 Plotly 库，因为它可以轻松创建外观精美的交互式图表。在少数情况下，我使用 Matplotlib 或 Seaborn。例如，我更喜欢在制作直方图时使用 Matplotlib（如你所见），因为默认情况下，它能提供我所需要的效果，而 Plotly 则不能做到这一点。

现在，让我们跳到实践部分，讨论使用案例，以及如何选择最合适的可视化方式来解决这些问题。

# 该使用哪种图表类型？

你可能经常会困惑，应该选择哪种图表来处理你的使用案例，因为有太多种类型。

有一些有价值的工具，比如在《数据讲故事》博客中描述的非常实用的[图表选择器](https://www.storytellingwithdata.com/blog/2013/04/chart-chooser)。它可以帮助你获得一些起步的灵感。

斯蒂芬·费尤提出了我认为非常有帮助的另一种方法。他写了[一篇文章](https://www.perceptualedge.com/articles/ie/the_right_graph.pdf)，*《Eenie, Meenie, Minie, Moe: 选择合适的图表来传达你的信息》*。在这篇文章中，他列出了数据可视化的七种常见使用场景，并提出了应对这些场景的可视化类型。

以下是这些使用场景的列表：

+   时间序列

+   名义比较

+   偏差

+   排名

+   部分与整体

+   频率分布

+   关联性

我们将逐一讨论这些情况，并举一些每种情况的可视化示例。我并不完全同意作者关于可视化类型的建议，我会分享我对此的看法。

> 以下图表示例基于合成数据，除非明确提到。

## 时间序列

**什么是使用场景？** 它是可视化中最常见的使用场景。我们经常希望观察一个或多个指标随时间变化的情况。

**图表建议**

最直接的选择（尤其是当你有多个指标时）是使用折线图。它突出了趋势，并为观众提供了数据的全面概览。

例如，我使用了折线图来展示每个平台的会话数量随时间变化的情况。我们可以看到，iOS是增长最快的部分，而其他平台的增长则相对停滞不前。

![](../Images/aa98b641f2b188f6240cb6274e091a1f.png)

按作者分类的可视化

使用折线图（而不是散点图）至关重要，因为折线图通过斜率强调趋势。

使用Plotly，你可以轻松创建这样的图表。我们有一个包含每月会话数量的数据集。

![](../Images/7849b0668d2791d14ab9f648f83f40cb.png)

然后，我们可以使用[Plotly Express](https://plotly.com/python/plotly-express/)来创建折线图，传入数据、标题，并覆盖标签。

```py
import plotly.express as px

px.line(
  ts_df, 
  title = '<b>Sessions by platforms</b>',
  labels = {'value': 'sessions', 'os': 'platform', 'month_date': 'month'},
  color_discrete_map={
      'Android': px.colors.qualitative.Vivid[1],
      'Windows': px.colors.qualitative.Vivid[2],
      'iOS': px.colors.qualitative.Vivid[4]
  }
)
```

> 这里我们不会详细讨论设计以及如何在Plotly中调整它，因为这是一个相当庞大的话题，值得单独撰写一篇文章。

我们通常将时间放在折线图的x轴上，并在数据点之间使用相等的时间间隔。

常见的误解之一是我们必须让y轴从零开始（必须包含0）。然而，对于折线图而言，这并不适用。在某些情况下，这样做甚至可能会妨碍你从数据中获得洞察。

例如，比较下面的两张图表。在第一张图表中，会话数量看起来相当稳定，而在第二张图表中，12月中旬的下降非常明显。然而，这完全是同一个数据集，唯一不同的是y轴的范围。

![](../Images/02964e548df194cb1810651ab5f5ffd6.png)

按作者分类的可视化

![](../Images/710b27d49b56227b831718ba3f62d0e5.png)

按作者分类的可视化

对于时间序列数据，你的选择不仅限于折线图。有时，柱状图可能是更好的选择，例如，当你有较少的数据点，并且希望强调单个值而不是趋势时。

![](../Images/318dadb71f086863f5bc9185d8a01977.png)

作者提供的可视化

在Plotly中创建柱状图也非常简单。

```py
fig = px.bar(
    df, 
    title = '<b>Sessions</b>',
    labels = {'value': 'sessions', 'os': 'platform', 'month_date': 'month'}, 
    text_auto = ',.6r' # specifying format for bar labels
)

fig.update_layout(xaxis_type='category') 
# to prevent converting string to dates
fig.update_layout(showlegend = False) 
# hiding ledend since we don't need it
```

## 名义比较

**什么是用例？** 这是你想要在不同细分市场之间比较一个或多个指标的情况。

**图表建议**

如果你只有几个数据点，你可以仅使用文本中的数字，而不是图表。我喜欢这种方法，因为它简洁而不杂乱。

![](../Images/f4e5c5311721a35300d3bb82697e39da.png)

作者提供的可视化

在许多情况下，柱状图将是比较指标的便捷工具。尽管垂直柱状图通常更常见，但当细分市场的名称较长时，水平柱状图将是更好的选择。

例如，我们可以比较不同地区每个客户的年[GMVs](https://en.wikipedia.org/wiki/Gross_merchandise_volume)（总商品价值）。

![](../Images/c1e2ede25d2ed4f1fd71d29951894206.png)

作者提供的可视化

要将柱状图改为水平的，你只需要设置`orientation = "h"`。

```py
fig = px.bar(df,
  text_auto = ',.6r', 
  title = '<b>Average annual GMV</b> (Gross Merchandise Value)',
  labels = {'country': 'region', 'value': 'average GMV in GBP'}, 
  orientation = 'h'
)

fig.update_layout(showlegend = False)
fig.update_xaxes(visible = False) # to hide x-axes
```

重要提示：始终为柱状图使用零为基准的坐标轴。否则，你可能会误导观众。

当柱状图中的数字过多时，我更喜欢使用热力图。在这种情况下，我们使用颜色饱和度来编码数字，这样并不是非常精确，因此我们还会保留标签。例如，让我们为我们的平均GMV视图添加另一个维度。

![](../Images/26148de8ea2c456ef8ae61dd8b17c46c.png)

作者提供的可视化

毫不奇怪，你也可以在Plotly中创建热力图。

```py
fig = px.imshow(
  table_df.values, 
  x = table_df.columns, # labels for x-axis
  y = table_df.index, # labels for y-axis 
  text_auto=',.6r', aspect="auto",
  labels=dict(x="age group", y="region", color="GMV in GBP"), 
  color_continuous_scale='pubugn',
  title = '<b>Average annual GMV</b> (Gross Merchandise Value) in GBP'
)

fig.show()
```

## 偏差

**什么是用例？** 这是我们想要突出显示值与基准线（例如基准或预测）之间差异的情况。

**图表建议**

在比较不同细分市场的指标时，使用图表传达这个想法的最佳方式是结合柱状图和基准线。

我们在[我之前的一篇文章](/topics-per-class-using-bertopic-252314f2640)中做了这样的可视化，研究了酒店评论中的主题建模。我比较了每个酒店连锁与基准线（所有评论的平均评分）相比，提及特定主题的客户评论比例。我还用颜色突出显示了与其他部分显著不同的部分。

![](../Images/fa3f6b0d3e7f9708719889738a4036cf.png)

此外，我们经常需要展示与预测的偏差。我们可以使用折线图来比较预测数据和实际数据的动态变化。我更喜欢将预测展示为虚线，以突出它不像实际数据那样确定。

![](../Images/42e322bba5cc3a7b0cee358211f89e6e.png)

作者提供的可视化

这个线图的案例比我们上面讨论的要复杂一些。所以，我们需要使用 Plotly 图形对象（Plotly Graphical Objects）而不是 Plotly Express 来定制图表。

```py
import plotly.graph_objects as go

# creating a figure
fig = go.Figure() 

# adding dashed line trace for forecast
fig.add_trace(
    go.Scatter(
        mode='lines',
        x=df.index,
        y=df.forecast,
        line=dict(color='#696969', dash='dot', width = 3),
        showlegend=True,
        name = 'forecast'
    )
)

# adding solid line trace for factual data
fig.add_trace(
    go.Scatter(
        mode='lines',
        x=df.index,
        y=df.fact,
        marker=dict(size=6, opacity=1, color = 'navy'),
        showlegend=True,
        name = 'fact'
    )
)

# setting title and size of layout
fig.update_layout(
  width = 800, 
  height = 400, 
  title = '<b>Daily Active Users:</b> forecast vs fact'
)

# specifying axis labels
fig.update_xaxes(title = 'day')
fig.update_yaxes(title = 'number of users')
```

## 排名

**什么是用例？** 这个任务类似于名义比较。我们也想比较几个细分领域中的指标，但我们希望强调排名——各细分领域的顺序。例如，这可能是年均 GMV 最高的前三个地区，或 ROI 最高的前三个营销活动。

**图表推荐**

毫不意外，我们可以使用与名义比较相似的柱状图。唯一需要记住的重要细节是按你关心的指标对图表上的各细分领域进行排序。例如，我们可以可视化年均 GMV 最高的前三个地区。

![](../Images/59721b2e2d60436c0e10e04ed32a8b44.png)

作者提供的可视化

## 部分与整体

**什么是用例？** 目标是理解某些子区的总和如何拆分。你可能想针对一个细分领域，或同时对多个细分领域进行此操作，以比较它们的结构。

**图表推荐**

最直接的解决方案是使用柱状图显示每个类别或子区的份额。值得注意的是，将类别按降序排列会使可视化更容易解读。

![](../Images/2aa176d558d69f49612ae1d902e3614e.png)

作者提供的可视化

上述方法适用于一个或多个细分领域。然而，有时使用堆积柱状图来比较结构会更简单。例如，我们可以查看不同地区按年龄段划分的客户份额。

![](../Images/4b23e76f7f867d30cccc2a90ae13a78f.png)

作者提供的可视化

饼图在这种情况下经常被使用。但我不建议这么做。根据视觉感知研究的结果，比较角度或面积比比较长度更具挑战性。因此，柱状图会更可取。

此外，我们可能需要查看随时间变化的结构。理想的选择是区域图。它将同时显示各子区的分布和趋势的斜率（这就是为什么它比单纯的柱状图（以月份为类别）更好的原因）。

![](../Images/2ef065b93344d940094b48a7b3bf8136.png)

作者提供的可视化

要创建区域图，你可以使用 Plotly 中的 `px.area` 函数。

```py
px.area(
  df, 
  title = '<b>Customer age</b> in Switzerland',
  labels = {'value': 'share of users, %', 
            'age_group': 'customer age', 'month': 'month'},  
  color_discrete_sequence=px.colors.diverging.balance
)
```

## 频率分布

**什么是用例？** 我通常在处理新数据时以这种可视化方式开始。目标是理解价值如何分布：

+   它是正态分布的吗？

+   它是单峰的吗？

+   我们的数据中有异常值吗？

**图表推荐**

对于频率分布，首选是直方图（通常是没有类别间隔的垂直条形图）。我通常更喜欢归一化直方图，因为它们比绝对值更容易解读。

如果你想查看多个指标的频率分布，你可以同时绘制多个直方图。在这种情况下，使用标准化直方图非常关键。否则，如果组内的对象数量不同，你将无法比较分布。

例如，我们可以可视化来自英国和瑞士客户的年度GMV分布。

![](../Images/0fbd61f59c7343229795a290c32abe5f.png)

作者提供的可视化

对于这个可视化，我使用了`matplotlib`。相比Plotly，我更喜欢`matplotlib`绘制直方图，因为我喜欢它们的默认设计。

```py
from matplotlib import pyplot

hist_range = [0, 10000]
hist_bins = 100

pyplot.hist(
    distr_df[distr_df.region == 'United Kingdom'].value.values,
    label = 'United Kingdom',
    alpha = 0.5, range = hist_range, bins = hist_bins,
    color = 'navy',
    # calculating weights to get normalised histogram
    weights = np.ones_like(distr_df[distr_df.region == 'United Kingdom'].index)*100/distr_df[distr_df.region == 'United Kingdom'].shape[0]
)

pyplot.hist(
    distr_df[distr_df.region == 'Switzerland'].value.values,
    label = 'Switzerland',
    color = 'red',
    alpha = 0.5, range = hist_range, bins = hist_bins,
    weights = np.ones_like(distr_df[distr_df.region == 'Switzerland'].index)*100/distr_df[distr_df.region == 'Switzerland'].shape[0]
)

pyplot.legend(loc = 'upper right')
pyplot.title('Distribution of customers GMV')
pyplot.xlabel('annual GMV in GBP')
pyplot.ylabel('share of users, %')
pyplot.show()
```

如果你想比较多个类别的分布，阅读同一图表上的多个直方图将会很有挑战性。所以，我建议你使用箱线图。它们显示的信息较少（只有中位数、四分位数和异常值），并且需要观众具备一定的基础知识。然而，在面对许多类别时，它可能是你最好的选择。

例如，我们来看一下按地区划分的站点停留时间分布。

![](../Images/4ab7391a49fe4a02582674ba2fbf24b6.png)

作者提供的可视化

如果你不记得如何解读箱线图，这里有一个图示可以给你一些提示。

![](../Images/a071357468998d488e54ce3dc08bea0c.png)

图片来自维基百科（[来源](https://en.wikipedia.org/wiki/Box_plot#/media/File:Boxplot_vs_PDF.svg)）| [CC BY-SA 2.5](https://creativecommons.org/licenses/by-sa/2.5) 许可

那么，让我们一起来看看箱线图可视化的所有构建块：

+   可视化中的框表示IQR（四分位间距）——25%和75%的分位数，

+   盒子中间的线表示中位数（50%的分位数），

+   须状线等于1.5 * IQR，或者如果数据集中的最小/最大值较小，则等于最小/最大值，

+   如果有任何数字超过1.5 * IQR（异常值），它们将以点的形式显示在图表上。

这是生成箱线图的Plotly代码。我使用了图形对象（Graphical Objects）而不是Plotly Express，以消除可视化中的异常值。当你的数据集有极端异常值或异常值过多时，这种方法会非常有用。

```py
fig = go.Figure()

fig.add_trace(go.Box(
    y=distr_df[distr_df.region == 'United Kingdom'].value,
    name="United Kingdom",
    boxpoints=False, # no data points
    marker_color=px.colors.qualitative.Prism[0],
    line_color=px.colors.qualitative.Prism[0]
))

fig.add_trace(go.Box(
    y=distr_df[distr_df.region == 'Germany'].value,
    name="Germany",
    boxpoints=False, # no data points
    marker_color=px.colors.qualitative.Prism[1],
    line_color=px.colors.qualitative.Prism[1]
))

fig.add_trace(go.Box(
    y=distr_df[distr_df.region == 'France'].value,
    name="France",
    boxpoints=False, # no data points
    marker_color=px.colors.qualitative.Prism[2],
    line_color=px.colors.qualitative.Prism[2]
))

fig.add_trace(go.Box(
    y=distr_df[distr_df.region == 'Switzerland'].value,
    name="Switzerland",
    boxpoints=False, # no data points
    marker_color=px.colors.qualitative.Prism[3],
    line_color=px.colors.qualitative.Prism[3]
))
fig.update_layout(title = '<b>Time spent on site</b> per month')
fig.update_yaxes(title = 'time spent in minutes')
fig.update_xaxes(title = 'region')
fig.show()
```

## 相关性

**什么是使用案例？** 目标是理解两个数值数据集之间的关系，是否一个值随着另一个值的增加而增加。

**图表推荐**

散点图是显示值之间相关性的最佳解决方案。你也可以添加趋势线来突出指标之间的关系。

![](../Images/4e0b0b503f99fd92cbd6fc0ed2d3d3e0.png)

作者提供的可视化

如果你有大量数据点，可能会遇到散点图的问题：当数据点过多时，它们会相互重叠，导致无法看清数据结构。在这种情况下，减少透明度可能有助于揭示关系。

例如，比较下面的两个图表。第二个图表能更好地理解数据分布。

![](../Images/d1f908492ade2f11eca77b2c68344f90.png)

作者提供的可视化

我们将使用 Plotly 图形对象来绘制此图，因为它非常自定义。要创建这样的图表，我们需要指定两个轨迹——一个用于散点图，另一个用于回归线。

```py
import plotly.graph_objects as go

# scatter plot
fig = go.Figure()
fig.add_trace(
    go.Scatter(
        mode='markers',
        x=corr_df.x,
        y=corr_df.y,
        marker=dict(size=6, opacity=0.1, color = 'grey'),
        showlegend=False
    )
)

# regression line
fig.add_trace(
    go.Scatter(
        mode='lines',
        x=linear_corr_df.x,
        y=linear_corr_df.linear_regression,
        line=dict(color='navy', dash='dash', width = 3),
        showlegend=False
    )
)

fig.update_layout(width = 600, height = 400, 
    title = '<b>Correlation</b> between revenue and customer tenure')
fig.update_xaxes(title = 'months since registration')
fig.update_yaxes(title = 'monthly revenue, GBP')
```

将回归线放在第二个轨迹上是至关重要的，因为否则它将被散点图覆盖。

此外，展示两个变量的频率分布可能会提供有价值的见解。这看起来并不容易，但你可以使用 `seaborn` 库中的联合图轻松实现。以下是相关代码。

```py
import seaborn as sns

sns.set_theme(style="darkgrid")

g = sns.jointplot(
  x="x", y="y", data=corr_df,
  kind="reg", truncate=False, 
  joint_kws = {'scatter_kws':dict(alpha=0.15), 'line_kws':{'color':'navy'}},
  color="royalblue", height=7)

g.set_axis_labels('months since registration', 'monthly revenue, GBP')
```

![](../Images/0b0bcf300335cf194d0c9183b08cb9f6.png)

作者可视化图

我们已经涵盖了所有数据可视化的使用场景。

## 这些是我需要了解的所有可视化类型吗？

我必须坦白说，时不时地，我会遇到上述建议不足以应对的任务，这时我需要一些其他的图表。

以下是一些例子：

+   Sankey 图或 Sunburst 图用于客户旅程图，

+   Choropleth 图，用于显示地理数据，

+   词云图，用于提供文本的高层次视图，

+   如果你想查看多条线的趋势，可以使用小型图（Sparklines）。

我通常会参考流行的可视化库的画廊获取灵感，例如，[Plotly](https://plotly.com/python/) 或 [seaborn](https://seaborn.pydata.org/examples/index.html)。

此外，你可以随时询问 ChatGPT 关于展示数据的可能选项，它会提供相当合理的指导。

![](../Images/27f4572ef9a091c84f60623f35775bea.png)

作者截图

# 总结

在本文中，我们讨论了数据可视化的基础知识：

+   为什么我们需要进行数据可视化？

+   在开始进行可视化工作之前，你应该问自己什么问题？

+   什么是数据可视化的基本构建块？哪些是观众最容易理解的？

+   数据可视化的常见使用场景是什么？你可以使用哪些图表类型来应对这些场景？

我希望提供的框架能够帮助你在面对各种选项时不至于感到迷茫，从而为你的观众创造更好的可视化效果。

> 非常感谢您阅读本文。如果您有任何后续问题或评论，请在评论区留言。
