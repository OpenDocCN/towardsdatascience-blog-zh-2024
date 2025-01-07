# 我在 Matplotlib 的库中发现了一个隐藏的宝藏：Python 中的打包气泡图。

> 原文：[https://towardsdatascience.com/i-found-a-hidden-gem-in-matplotlibs-library-packed-bubble-charts-in-python-d0f5d892beb7?source=collection_archive---------0-----------------------#2024-07-28](https://towardsdatascience.com/i-found-a-hidden-gem-in-matplotlibs-library-packed-bubble-charts-in-python-d0f5d892beb7?source=collection_archive---------0-----------------------#2024-07-28)

## 曾经希望设计那些美丽的基于 Tableau 的打包气泡图吗？跟随本教程，了解 *Matplotlib* 的解决方案。

[](https://medium.com/@gordunanna?source=post_page---byline--d0f5d892beb7--------------------------------)[![Anna Gordun Peiro](../Images/874ec5096d61e6345104026a4cf602e9.png)](https://medium.com/@gordunanna?source=post_page---byline--d0f5d892beb7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d0f5d892beb7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d0f5d892beb7--------------------------------) [Anna Gordun Peiro](https://medium.com/@gordunanna?source=post_page---byline--d0f5d892beb7--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d0f5d892beb7--------------------------------) ·阅读时间 7 分钟·2024年7月28日

--

![](../Images/29a20958991f74913d6a2bd0c40f86c8.png)

气泡图展示了 2020 年夏季奥运会中女性参与的比例。

# 什么是打包气泡图？

打包气泡图用于展示以圆圈群集形式显示的数据。每个数据项都展示为一个独立的圆圈，可以使用两个主要变量：气泡的大小和颜色。

这是我最喜欢的一些打包气泡图：

**乌克兰粮食出口**：颜色用于显示国家收入组，气泡大小表示粮食的吨数。

**关键和前线工人：** 颜色用于定义工人群体，气泡大小表示工人数，集群用于表示行业部门。

尽管这些示例并非用 Python 完成的，但是否有可能仅使用 Matplotlib 来构建一个更简单的打包气泡图？让我们看看：

# **气泡图的 Python 代码**

上周，我在 Matplotlib 文档的杂项部分偶然看到一个示例 [[链接]](https://matplotlib.org/stable/gallery/misc/packed_bubbles.html)。

> 这是 Matplotlib 杂项文档部分中的一个隐藏宝藏吗？

这是附带代码的示例：

![](../Images/d4ab8f8f5daac342b3c45420bf05cc3c.png)

气泡图教程示例。

# **它是如何工作的？**

使用起来出奇的顺畅，下面是如何创建你的第一个气泡图：

```py
#ADD YOUR DATA HERE
browser_market_share = {
    'browsers': ['firefox', 'chrome', 'safari', 'edge', 'ie', 'opera'],
    'market_share': [8.61, 69.55, 8.36, 4.12, 2.76, 2.43],
    'color': ['#5A69AF', '#579E65', '#F9C784', '#FC944A', '#F24C00', '#00B825']
}
#STEP 3
bubble_chart = BubbleChart(area=browser_market_share['market_share'],
                           bubble_spacing=0.1)
#STEP 4
bubble_chart.collapse()

#STEP 5

fig, ax = plt.subplots(subplot_kw=dict(aspect="equal"))
bubble_chart.plot(
    ax, browser_market_share['browsers'], browser_market_share['color'])
ax.axis("off")
ax.relim()
ax.autoscale_view()
ax.set_title('Browser market share')

plt.show()
```

1- 添加你自己的数据，或者使用示例中提供的数据。你需要一个变量来表示气泡大小，另一个变量来表示标签和颜色。

2- 复制、粘贴并运行提供代码中的所有函数。

3- 通过调用*BubbleChart*并将气泡大小作为变量来创建气泡分布。

4- 折叠所有气泡，使它们相互接触但不重叠

5- 创建图表并添加颜色、标签和标题。

**重要：**

+   外观必须保持为*equal*，否则你的气泡不会是完美的圆形。

+   *relim*() 和 *autoscale_view*() 也必须保留，因为你无法选择气泡在网格中的显示位置。

> 我同意它看起来并不完美，尤其是在看到之前那些漂亮的例子之后。所以我花了几天时间将其改进，以下是我所做的改进：

# **图表定制：**

对于我的图表，我使用了来自Olympedia.org的奥运历史数据集，该数据集由Joseph Cheng在[Kaggle](https://www.kaggle.com/datasets/josephcheng123456/olympic-historical-dataset-from-olympediaorg)上共享，且具有公共领域许可证。

![](../Images/c0195a7a8a47069b4f3689148040561c.png)

数据集截图

它包含从1896年雅典到2022年北京的奥运会的运动员级别的事件结果。经过探索性数据分析（EDA），我将其转换为一个数据集，详细记录了每年每个项目/赛事中女性运动员的数量。我的气泡图的想法是展示哪些运动项目具有50/50的男女运动员比例，以及这种比例随时间的变化。

我的绘图数据由两个不同的数据集组成，每个数据集代表一个年份：**2020**年和**1996**年。对于每个数据集，我计算了每个项目中参加的运动员总数（*athlete_sum*），以及这个总数与所有运动员（男性+女性）总数相比的差异（*difference*）。以下是数据的截图：

![](../Images/6e8fbc2fc66be383a150729ea13db5cf.png)

绘图数据集的截图

这是我用来可视化它的方法：

+   **大小比例。** 使用气泡的半径来比较每项运动的运动员数量。较大的气泡代表竞争激烈的项目，如田径。

+   **多变量解释**。利用颜色表示女性运动员的比例。浅绿色气泡将代表性别比例为50/50的项目，如曲棍球。

这是我的起点（使用上面的代码和方法）：

![](../Images/fa4d2fdc067970e969d7087a77fbb1a8.png)

第一个结果

一些简单的修复：增加图形大小，并且如果气泡的大小小于250，则将标签设置为空，以避免文字溢出气泡外部。

```py
fig, ax = plt.subplots(figsize=(12,8),subplot_kw=dict(aspect="equal"))

#Labels edited directly in dataset 
```

![](../Images/bab961d1ad7748271c92f5c0cd8b1f9e.png)

第二个结果

好吧，现在至少可以阅读了。但为什么*田径*是粉色而*拳击*是蓝色的呢？让我们添加一个图例来说明颜色与女性代表性之间的关系。

> 因为这不是常规的条形图，*plt.legend()* 在这里不起作用。

使用matplotlib的Annotation Bbox，我们可以创建矩形（或圆形）来显示每种颜色背后的含义。我们还可以做同样的事情来显示气泡的比例。

```py
import matplotlib.pyplot as plt
from matplotlib.offsetbox import (AnnotationBbox, DrawingArea,
                                  TextArea,HPacker)
from matplotlib.patches import Circle,Rectangle

# This is an example for one section of the legend

# Define where the annotation (legend) will be
xy = [50, 128]

# Create your colored rectangle or circle
da = DrawingArea(20, 20, 0, 0)
p = Rectangle((10 ,10),10,10,color="#fc8d62ff")
da.add_artist(p)

# Add text 

text = TextArea("20%", textprops=dict(color="#fc8d62ff", size=14,fontweight='bold'))

# Combine rectangle and text
vbox = HPacker(children=[da, text], align="top", pad=0, sep=3)

# Annotate both in a box (change alpha if you want to see the box)
ab = AnnotationBbox(vbox, xy,
                    xybox=(1.005, xy[1]),
                    xycoords='data',
                    boxcoords=("axes fraction", "data"),
                    box_alignment=(0.2, 0.5),
                    bboxprops=dict(alpha=0)
                    )
#Add to your bubble chart
ax.add_artist(ab)
```

我还通过使用*plt.text()*添加了副标题和图表下方的文字描述。

# 然后，完成：

![](../Images/29a20958991f74913d6a2bd0c40f86c8.png)

最终可视化

图表的简洁且用户友好的解读：

+   大部分气泡是浅绿色的 → 绿色表示50%的女性参与 → 大多数奥运会项目有着均衡的男女比例*(耶🙌)*

+   只有一种运动（棒球），以深绿色表示，且没有女性参与。

+   有3项运动仅有女性参与，但运动员数量相对较少。

+   按运动员数量排名前三的运动（游泳、田径和体操）非常接近50/50的男女比例

# 奖励可视化。

![](../Images/c12088d38b9bbcd9ff40ad9d5b85fb60.png)

2020年与1996年女性参与比例的比较

在这里，我使用封装气泡图来展示一个额外的变量：**时间**。左侧的图表表示的是2020年奥运会的参与情况，右侧的是1996年奥运会的参与情况。将它们并排放置带来了一些有趣的见解：

+   左侧的气泡明显比右侧更多 → 2020年奥运会的项目比1996年奥运会更多

+   1996年奥运会几乎没有浅绿色气泡 → 1996年女性参与度远低于2020年，且远未达到50/50的男女比例

+   拳击在1996年女性参与度为0%（深绿色），而2020年为30%（蓝色）。

可视化并比较两个数据集可能会很复杂，尤其是在需要同时比较三个变量时。然而，封装气泡图不仅在视觉上吸引观众，还因其易用性和直观性而让人印象深刻。

> 我还收集了一些我没有使用的自定义选项，下面可以查看它们。

# **其他自定义选项**

增加气泡间距，以获得更放松的视图（2与0.1相比）

```py
bubble_chart = BubbleChart(area=browser_market_share['market_share'],
                           bubble_spacing=2)
```

![](../Images/9a27dfc8b1cac685e7b0e5d0582612d7.png)

气泡间距的影响

也可以通过更新def_init来仅获得水平视图。此修改会根据每个气泡及其前一个气泡的半径和气泡间距来计算新的x坐标。y坐标设为0。

```py
 def __init__(self, area, bubble_spacing=0):
        area = np.asarray(area)
        r = np.sqrt(area / np.pi)

        self.bubble_spacing = bubble_spacing
        self.bubbles = np.ones((len(area), 4))
        self.bubbles[:, 2] = r
        self.bubbles[:, 3] = area

        # UPDATE: Position the bubbles in a horizontal row, touching each other
        self.bubbles[:, 0] = np.cumsum(r * 2 + self.bubble_spacing) - r - self.bubble_spacing / 2
        self.bubbles[:, 1] = 0 
```

![](../Images/e0ffdb119dabefcb86a1cf0a2a441ea7.png)

水平自定义

# 潜在改进✨

我希望在Plotly中看到类似的效果，因为这个图表非常需要一些互动性（尤其是对于那些太小以至于没有标签的气泡），也许可以加入一个滑块来切换年份并自动更新图表。

# 最后的结论

matplotlib的封装气泡图小巧解决方案将为您节省大量绘制圆圈的时间，并且有潜力成为Tableau的强大替代工具。在本文中，我们探索了如何创建它们、如何自定义以及如何添加图例。通过这些操作，我们最终得到了一个既具视觉吸引力又易于阅读的最终可视化，能够帮助讲述一个故事并吸引观众的注意力。

我还给你带来了奥运会性别平等的一些信息，以及过去20年它的快速进展。

> 仅供参考，巴黎2024将是历史上首次男女参赛人数相等的奥运会

祝你编码愉快，观奥运愉快！

*本文中的所有图片均由作者提供*
