# 从默认Python折线图到期刊级质量信息图

> 原文：[https://towardsdatascience.com/from-default-python-line-chart-to-journal-quality-infographics-80e3949eacc3?source=collection_archive---------2-----------------------#2024-12-30](https://towardsdatascience.com/from-default-python-line-chart-to-journal-quality-infographics-80e3949eacc3?source=collection_archive---------2-----------------------#2024-12-30)

## 将乏味的默认Matplotlib折线图转变为令人惊艳的定制可视化

[](https://medium.com/@vzhyvov?source=post_page---byline--80e3949eacc3--------------------------------)[![Vladimir Zhyvov](../Images/419b3d81707b445c95ea554b0a017769.png)](https://medium.com/@vzhyvov?source=post_page---byline--80e3949eacc3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--80e3949eacc3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--80e3949eacc3--------------------------------) [Vladimir Zhyvov](https://medium.com/@vzhyvov?source=post_page---byline--80e3949eacc3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--80e3949eacc3--------------------------------) ·4分钟阅读·5天前

--

![](../Images/9baee349eeb0c00bd49abee4f17d213f.png)

封面，图片由作者提供

每个使用过Matplotlib的人都知道默认图表有多难看。在这系列文章中，我将分享一些技巧，帮助你的可视化脱颖而出，并展现你个人的风格。

我们将从一个简单的折线图开始，这种图表被广泛使用。主要亮点是在线图下方添加渐变填充——这个任务并不完全简单。

那么，让我们深入并逐步走过这次转换的所有关键步骤吧！

让我们先进行所有必要的导入。

```py
import pandas as pd
import numpy as np
import matplotlib.dates as mdates
import matplotlib.pyplot as plt
import matplotlib.ticker as ticker
from matplotlib import rcParams
from matplotlib.path import Path
from matplotlib.patches import PathPatch

np.random.seed(38)
```

现在我们需要为可视化生成示例数据。我们将创建类似股票价格的图表。

```py
dates = pd.date_range(start='2024-02-01', periods=100, freq='D')
initial_rate = 75
drift = 0.003
volatility = 0.1
returns = np.random.normal(drift, volatility, len(dates))
rates = initial_rate * np.cumprod(1 + returns)

x, y = dates, rates
```

让我们看看使用默认Matplotlib设置时它的样子。

```py
fix, ax = plt.subplots(figsize=(8, 4))
ax.plot(dates, rates)
ax.xaxis.set_major_locator(mdates.DayLocator(interval=30))
plt.show()
```

![](../Images/ae51668ab816134bd1ef655e631997a7.png)

默认图，图片由作者提供

真的不太吸引人，对吧？但我们将逐步改进它，让它看起来更好。

+   设置标题

+   设置通用图表参数——大小和字体

+   将Y轴的刻度移到右侧

+   更改主线的颜色、样式和宽度

```py
# General parameters
fig, ax = plt.subplots(figsize=(10, 6))
plt.title("Daily visitors", fontsize=18, color="black")
rcParams['font.family'] = 'DejaVu Sans'
rcParams['font.size'] = 14

# Axis Y to the right
ax.yaxis.tick_right()
ax.yaxis.set_label_position("right")

# Plotting main line
ax.plot(dates, rates, color='#268358', linewidth=2)
```

![](../Images/0dfe063bd1de2749ecef86453875713a.png)

一般参数已应用，图片由作者提供

好的，现在看起来干净了一些。

现在，我们想在背景中添加极简网格，去除边框以使外观更加干净，并移除Y轴上的刻度。

```py
# Grid
ax.grid(color="gray", linestyle=(0, (10, 10)), linewidth=0.5, alpha=0.6)
ax.tick_params(axis="x", colors="black")
ax.tick_params(axis="y", left=False, labelleft=False) 

# Borders
ax.spines["top"].set_visible(False)
ax.spines['right'].set_visible(False)
ax.spines["bottom"].set_color("black")
ax.spines['left'].set_color('white')
ax.spines['left'].set_linewidth(1)

# Remove ticks from axis Y
ax.tick_params(axis='y', length=0)
```

![](../Images/80175d4f2523f488ff7429f2879a0a7b.png)

已添加网格，图片由作者提供

现在我们添加了一个小的美学细节——在X轴的第一个刻度附近加上年份。同时，我们还将刻度标签的字体颜色变得更浅。

```py
# Add year to the first date on the axis
def custom_date_formatter(t, pos, dates, x_interval):
    date = dates[pos*x_interval]
    if pos == 0:
        return date.strftime('%d %b \'%y')  
    else:
        return date.strftime('%d %b')  
ax.xaxis.set_major_formatter(ticker.FuncFormatter((lambda x, pos: custom_date_formatter(x, pos, dates=dates, x_interval=x_interval))))

# Ticks label color
[t.set_color('#808079') for t in ax.yaxis.get_ticklabels()]
[t.set_color('#808079') for t in ax.xaxis.get_ticklabels()]
```

![](../Images/60fd2791a28bedc91e2ff7bf1624137c.png)

第一个日期附近的年份，图片由作者提供

我们离最棘手的部分越来越近——如何在曲线下方创建渐变。实际上，Matplotlib并没有直接提供这样的选项，但我们可以通过创建渐变图像并将其与图表裁剪来模拟这一效果。

```py
# Gradient
numeric_x = np.array([i for i in range(len(x))])
numeric_x_patch = np.append(numeric_x, max(numeric_x))
numeric_x_patch = np.append(numeric_x_patch[0], numeric_x_patch)
y_patch = np.append(y, 0)
y_patch = np.append(0, y_patch)

path = Path(np.array([numeric_x_patch, y_patch]).transpose())
patch = PathPatch(path, facecolor='none')
plt.gca().add_patch(patch)

ax.imshow(numeric_x.reshape(len(numeric_x), 1),  interpolation="bicubic",
                cmap=plt.cm.Greens, 
                origin='lower',
                alpha=0.3,
                extent=[min(numeric_x), max(numeric_x), min(y_patch), max(y_patch) * 1.2], 
                aspect="auto", clip_path=patch, clip_on=True)
```

![](../Images/afc5c1fd0ee710440ce009002af8e301.png)  

添加了渐变，图片由作者提供  

现在看起来干净整洁了。我们只需要使用任何编辑器（我偏爱Google Slides）添加几个细节——标题、圆角边框和一些数字指示器。  

![](../Images/fa35cc0196b358b936f3ff2966471271.png)  

最终可视化效果，图片由作者提供  

下面是重现该可视化效果的完整代码：  
