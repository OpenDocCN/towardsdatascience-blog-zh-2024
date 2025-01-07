# 如何在Python中制作高级蛛网图

> 原文：[https://towardsdatascience.com/how-to-make-an-advanced-spider-chart-in-python-adbdb6c24a66?source=collection_archive---------3-----------------------#2024-09-05](https://towardsdatascience.com/how-to-make-an-advanced-spider-chart-in-python-adbdb6c24a66?source=collection_archive---------3-----------------------#2024-09-05)

## 逐步讲解，最后提供一个易于使用的函数

[](https://medium.com/@zvonimir.boban.mef?source=post_page---byline--adbdb6c24a66--------------------------------)[![Zvonimir Boban](../Images/cbad06b7e1f5d021ce9b2dc31b8a6a65.png)](https://medium.com/@zvonimir.boban.mef?source=post_page---byline--adbdb6c24a66--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--adbdb6c24a66--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--adbdb6c24a66--------------------------------) [Zvonimir Boban](https://medium.com/@zvonimir.boban.mef?source=post_page---byline--adbdb6c24a66--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--adbdb6c24a66--------------------------------) ·8分钟阅读·2024年9月5日

--

![](../Images/dbce2d785e6cd17f6370f715c8437a17.png)

图片由[Divyadarshi Acharya](https://unsplash.com/@lincon_street?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来源于[Unsplash](https://unsplash.com/photos/selective-focus-photography-of-spider-on-web-Qbs6liSxjr8?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 💡动机

有多种Python库可以用来制作经典的蛛网图/雷达图。这些库的共同点在于它们只提供带有单一比例刻度轴的蛛网图，通常显示的刻度范围是从0到100。

当然，为了能够比较特征值，将其重新调整为一个公共刻度是必要的，但这样做却忽略了每个特征的绝对值范围。由于这些信息无法从图表中获得，我们不得不回到数据中去查找。在最好的情况下，这个过程既耗时又繁琐；而在最坏的情况下，我们可能无法访问原始数据，这意味着我们无法获得充分理解比较所需的关键信息。

一个合乎逻辑的进阶做法是制作一个带有显示每个特征绝对值的轴的蛛网图——这种图表被称为**多轴蛛网图**。你可能会认为很多库也会提供这种图表，但我搜索了许多资料后依然没有找到相关结果。受此启发，我决定自己制作一个解决方案，并通过这篇逐步指南分享给大家，最后会提供一个易于使用的函数供你使用。

# 🕸️ 图表

为了演示如何制作多轴蜘蛛图，我将使用著名的`mtcars`数据集的一小部分。该数据集来源于1974年《Motor Trend》杂志，并在Henderson和Velleman的1981年研究中首次发布[1]。让我们加载数据和所需的库。

```py
import pandas as pd
import numpy as np
from sklearn.preprocessing import MinMaxScaler
from plotnine import *

mtcars_values = {
    'mpg': [19.7, 15, 21.4],
    'cyl': [6, 8, 4],
    'disp': [145, 301, 121],
    'hp': [175, 335, 109],
    'drat': [3.62, 3.54, 4.11],
    'wt': [2.77, 3.57, 2.78],
    'qsec': [15.5, 14.6, 18.6],
    'vs': [0, 0, 1],py
    'am': [1, 1, 1],
    'gear': [5, 5, 4],
    'carb': [6, 8, 2],
    'wtdip': [401.65, 1074.57, 336.38]
}

mtcars = pd.DataFrame(mtcars_values, 
index=["Ferrari Dino", "Maserati Bora", "Volvo 142E"])

# Select and rename columns
p_data = mtcars.reset_index().rename(columns={'index': 'group'})[['group', 'mpg', 'cyl', 'hp', 'wt', 'qsec']]
p_data.columns = ['group', 'Miles per Gallon', 'Cylinders', 
'Horsepower', 'Weight', 'Quarter mile\ntime']
```

如你所见，我将使用`plotnine`库来创建图表。受`ggplot2`启发，`plotnine`库也基于图形语法的概念，通过将多个图层叠加在一起来创建图表。这个强大的概念使我们能够创建几乎任何我们能想到的可视化图形。

![](../Images/38276e502f62184d2de0ceb05a94f33f.png)

使用图形语法方法构建图表。图片由作者提供

分层方法要求分别构建图表的不同方面。首先我们将创建图表轮廓。由于蜘蛛图处理的是极坐标，我编写了一个函数，根据数据集中变量的数量来计算多边形顶点的坐标。

```py
# Calculate the coordinates of polygon tips
def circle_coords(r, n_axis=len(p_data.columns) - 1):
    fi = np.linspace(0, 2*np.pi, n_axis+1) + np.pi/2
    x = r * np.cos(fi)
    y = r * np.sin(fi)
    return pd.DataFrame({'x': x, 'y': y, 'r': r})

central_distance = 0.2
axis_name_offset = 0.2

circle_df = pd.concat([circle_coords(r) for r in np.arange(0, 1.25, 0.25) + central_distance])

step_1 = (ggplot(circle_df, aes('x', 'y')) +
  geom_polygon(data=circle_coords(1 + central_distance, p_data.shape[1] - 1), alpha=1, fill='beige') +
  geom_path(aes(group='r'), linetype='dashed', alpha=0.5) +
  theme_void() +
  theme(legend_title=element_blank(),
        legend_direction='horizontal',
        legend_position='bottom',
        legend_box_spacing=0))
```

![](../Images/49dd29a82fb73dd36d2b747086e0d49f.png)

第一步：为图表创建背景。图片由作者提供

接下来，我们需要计算轴的坐标并将它们添加到图表中。

```py
# Calculate the coordinates for the axis lines
def axis_coords(n_axis):
    fi = np.linspace(0, 2*np.pi*(1-1/n_axis), n_axis) + np.pi/2
    x1 = central_distance * np.cos(fi)
    y1 = central_distance * np.sin(fi)
    x2 = (1 + central_distance) * np.cos(fi)
    y2 = (1 + central_distance) * np.sin(fi)
    return pd.DataFrame({'x': np.concatenate([x1, x2]), 
                         'y': np.concatenate([y1, y2]), 
                         'id': np.tile(np.arange(1, n_axis + 1), 2)})

step_2 = (step_1 + geom_line(data=axis_coords(p_data.shape[1] - 1), mapping=aes(x='x', y='y', group='id'), alpha=0.3))
```

![](../Images/4704a33e8e066a82bbc62bfe6929671d.png)

为数据集中的每个变量添加轴。图片由作者提供

现在我们可以叠加重新缩放后的数据点了。

```py
# Calculate the rescaled coordinates for each point

n_axis = len(p_data.columns) - 1  # Subtract 1 to exclude the group column

scaler = MinMaxScaler()

rescaled_data = p_data.copy()
rescaled_data.iloc[:, 1:] = scaler.fit_transform(rescaled_data.iloc[:, 1:])
rescaled_data['copy'] = rescaled_data.iloc[:, 1]

melted_data = rescaled_data.melt(id_vars=['group'], var_name='parameter', value_name='value', ignore_index=False)

melted_data['parameter'] = pd.Categorical(melted_data['parameter'], categories=np.array(rescaled_data.columns[1:]), ordered=True)
melted_data = melted_data.sort_values(by = ['group', 'parameter'])
melted_data['fi'] = np.tile(np.linspace(0, 2 * np.pi, num=n_axis+1) + np.pi / 2, p_data.shape[0])
melted_data['x'] = (melted_data['value'] + central_distance)*np.cos(melted_data['fi'])
melted_data['y'] = (melted_data['value'] + central_distance)*np.sin(melted_data['fi'])

rescaled_coords_data = melted_data

step_3 = (step_2 +
geom_point(data=rescaled_coords_data, mapping=aes(x='x', y='y', group='group', color='group'), size=3) +
        geom_path(data=rescaled_coords_data, mapping=aes(x='x', y='y', group='group', color='group'), size=1) +
        geom_polygon(data=rescaled_coords_data, mapping=aes('x', 'y', group = 'group', color = 'group', fill = 'group'), size = 1, alpha = 0.05, show_legend = False))
```

![](../Images/864cc565f13cd3e145eb42ce014ffe05.png)

将数据点叠加到图表上。图片由作者提供

剩下要做的就是添加轴的文本标签和名称。

```py
# Radius and corresponding feature value for each feature
text_data = pd.DataFrame({col: np.linspace(p_data[col].min(), p_data[col].max(), 5) 
                          for col in p_data.columns if col != 'group'})
text_data['r'] = np.arange(0, 1.25, 0.25)
text_data = text_data.melt(id_vars=['r'], var_name='parameter', value_name='value')
text_data['parameter'] = pd.Categorical(text_data['parameter'], categories=np.array(p_data.columns[1:]), ordered=True)
text_data = text_data.sort_values(by = ['r', 'parameter'])

def text_coords(r, n_axis=len(p_data.columns) - 1):
    fi = np.linspace(0, 2*np.pi*(1-1/n_axis), n_axis) + np.pi/2 + 0.01*2*np.pi/r
    x = r * np.cos(fi)
    y = r * np.sin(fi)
    return pd.DataFrame({'x': x, 'y': y, 'r': r - central_distance})

# Coordinates for the axis labels
labels_data = pd.concat([text_coords(r) for r in np.arange(0, 1.25, 0.25) + central_distance])

# Combine with text_data
labels_data = pd.concat([labels_data.reset_index(drop=True), 
                         text_data.drop('r', axis=1).reset_index(drop=True)], 
                        axis=1)

labels_data['value']=labels_data['value'].round(2) 

step_4 = (step_3 +
        geom_text(data=labels_data, mapping=aes(x='x', y='y', label='value'), alpha=0.65, size=8, 
         color='#303030') +
        geom_text(data=text_coords(1 + central_distance + axis_name_offset, p_data.shape[1] - 1),
         mapping=aes(x='x', y='y'), 
         label=[param for param in p_data.columns[1:]],
         size=9) +
        labs(color='', title = 'Comparison of car properties'))
```

![](../Images/6311b6fb070dfa78ef0fc924fffd52de.png)

添加轴名称和标签。图片由作者提供

以及一些最后的美学修饰……

```py
#Final aesthetic touches
step_5 = (step_4 +
          labs(color='', title='Comparison of car properties') +
             theme(legend_position='bottom',
                   legend_text=element_text(size=7, face='bold'),
                   legend_box_margin=0,
                   legend_margin=-20,
                   plot_title=element_text(size=10, margin={'b': -30}, face='bold')) +
             lims(x=(-1.75, 1.75), y=(-1.5, 1.8)))
```

![](../Images/7ea387aa2024833e4e1a8367eb94b492.png)

最终的图表。图片由作者提供

让我们再花一点时间评论一下显示的数字。即使是一个普通的蜘蛛图，也能明显看出沃尔沃是最慢的车。然而，在这里我们还可以看到确切的绝对差异——沃尔沃用了18.6秒才走完四分之一英里的距离，而三者中最快的玛莎拉蒂则少用了整整四秒。当然，预期的结果是，沃尔沃在燃油消耗方面最为经济，每加仑油能行驶比玛莎拉蒂Bora多6英里。作为跑车，玛莎拉蒂Bora和法拉利Dino也拥有更多的气缸和马力，且比沃尔沃重。

# 其他示例

这是另一个使用泰坦尼克号数据集并且自定义了字体的蜘蛛图示例。

![](../Images/c506c533342c409e4405ad4567fe9b64.png)

另一个使用泰坦尼克号数据集的示例。图片由作者提供

这张图清晰地显示了头等舱乘客是三者中最年长且最富有的。三等舱乘客中男女乘客最多，且是最年轻的一组——可能大多数是寻求更好生活的年轻人和家庭。然而，头等舱乘客的生还率最高，三等舱则最低。这可能部分是因为头等舱靠近船甲板，部分是由于该舱位女性的比例较高（因为妇女和儿童优先获救）。

# ➕功能

如承诺所示，这里是封装上述所有代码的函数。第一个参数是格式化后的数据框，其中第一列包含组的 ID，其他列是要绘制的组特征。两个额外的参数确定内圈空白多边形的半径和图表中轴标题的偏移量。

```py
# Wrapping the above code into an easy-to-use function
def multiaxis_radar(p_data, central_distance=0.2, axis_name_offset=0.2):
    def circle_coords(r, n_axis=len(p_data.columns) - 1):
        fi = np.linspace(0, 2*np.pi, n_axis+1) + np.pi/2
        x = r * np.cos(fi)
        y = r * np.sin(fi)
        return pd.DataFrame({'x': x, 'y': y, 'r': r})

    circle_df = pd.concat([circle_coords(r) for r in np.arange(0, 1.25, 0.25) + central_distance])

    def axis_coords(n_axis):
        fi = np.linspace(0, 2*np.pi*(1-1/n_axis), n_axis) + np.pi/2
        x1 = central_distance * np.cos(fi)
        y1 = central_distance * np.sin(fi)
        x2 = (1 + central_distance) * np.cos(fi)
        y2 = (1 + central_distance) * np.sin(fi)
        return pd.DataFrame({'x': np.concatenate([x1, x2]), 
                             'y': np.concatenate([y1, y2]), 
                             'id': np.tile(np.arange(1, n_axis + 1), 2)})

    n_axis = len(p_data.columns) - 1

    scaler = MinMaxScaler()
    rescaled_data = p_data.copy()
    rescaled_data.iloc[:, 1:] = scaler.fit_transform(rescaled_data.iloc[:, 1:])
    rescaled_data['copy'] = rescaled_data.iloc[:, 1]

    melted_data = rescaled_data.melt(id_vars=['group'], var_name='parameter', value_name='value', ignore_index=False)
    melted_data['parameter'] = pd.Categorical(melted_data['parameter'], categories=np.array(rescaled_data.columns[1:]), ordered=True)
    melted_data = melted_data.sort_values(by=['group', 'parameter'])
    melted_data['fi'] = np.tile(np.linspace(0, 2 * np.pi, num=n_axis+1) + np.pi / 2, p_data.shape[0])
    melted_data['x'] = (melted_data['value'] + central_distance)*np.cos(melted_data['fi'])
    melted_data['y'] = (melted_data['value'] + central_distance)*np.sin(melted_data['fi'])

    rescaled_coords_data = melted_data

    text_data = pd.DataFrame({col: np.linspace(p_data[col].min(), p_data[col].max(), 5) 
                              for col in p_data.columns if col != 'group'})
    text_data['r'] = np.arange(0, 1.25, 0.25)
    text_data = text_data.melt(id_vars=['r'], var_name='parameter', value_name='value')
    text_data['parameter'] = pd.Categorical(text_data['parameter'], categories=np.array(p_data.columns[1:]), ordered=True)
    text_data = text_data.sort_values(by=['r', 'parameter'])

    def text_coords(r, n_axis=len(p_data.columns) - 1):
        fi = np.linspace(0, 2*np.pi*(1-1/n_axis), n_axis) + np.pi/2 + 0.01*2*np.pi/r
        x = r * np.cos(fi)
        y = r * np.sin(fi)
        return pd.DataFrame({'x': x, 'y': y, 'r': r - central_distance})

    labels_data = pd.concat([text_coords(r) for r in np.arange(0, 1.25, 0.25) + central_distance])
    labels_data = pd.concat([labels_data.reset_index(drop=True), 
                             text_data.drop('r', axis=1).reset_index(drop=True)], 
                            axis=1)
    labels_data['value'] = labels_data['value'].round(2)

    plot = (ggplot(circle_df, aes('x', 'y')) +
            geom_polygon(data=circle_coords(1 + central_distance, p_data.shape[1] - 1), alpha=1, fill='beige') +
            geom_path(aes(group='r'), linetype='dashed', alpha=0.5) +
            theme_void() +
            theme(legend_title=element_blank(),
            legend_direction='horizontal',
            legend_position='bottom',
            legend_box_spacing=0) +
            geom_line(data=axis_coords(p_data.shape[1] - 1), mapping=aes(x='x', y='y', group='id'), alpha=0.3) +
            geom_point(data=rescaled_coords_data, mapping=aes(x='x', y='y', group='group', color='group'), size=3) +
            geom_path(data=rescaled_coords_data, mapping=aes(x='x', y='y', group='group', color='group'), size=1) +
            geom_polygon(data=rescaled_coords_data, mapping=aes('x', 'y', group='group', color='group', fill='group'), size=1, alpha=0.05, show_legend=False) +
            geom_text(data=labels_data, mapping=aes(x='x', y='y', label='value'), alpha=0.65, size=8, 
             color='#303030') +
            geom_text(data=text_coords(1 + central_distance + axis_name_offset, p_data.shape[1] - 1),
             mapping=aes(x='x', y='y'), 
             label=[param for param in p_data.columns[1:]],
             size=9) +
             labs(color='', title='Comparison of car properties') +
             theme(legend_position='bottom',
                   legend_text=element_text(size=7, face='bold'),
                   legend_box_margin=0,
                   legend_margin=-20,
                   plot_title=element_text(size=10, margin={'b': -30}, face='bold')) +
             lims(x=(-1.75, 1.75), y=(-1.5, 1.8)))

    return plot

# Use the function to recreate the above plot
multiaxis_radar(p_data, central_distance=0.2, axis_name_offset=0.25)
```

# 🏁 结论

本文展示了如何在 Python 中从零开始构建一个高级的多轴蜘蛛图。尽管我所知道的任何 Python 包目前都不支持这种图表，但利用 `plotnine` 包中的图形语法方法，给了我自己创建这种图表的工具。当然，仍然有进步的空间，因为最终的函数可以通过添加更多参数和选项来进一步定制，控制图表的各个方面，例如线条和背景颜色、字体大小等，但我暂时把这些留给读者😉。

就这些了，希望你觉得这篇文章有用，并且能够运用它制作更多精美的蜘蛛图。享受吧！

# 参考文献

[1] Henderson, H. V., & Velleman, P. F. (1981). 交互式构建多重回归模型。《生物统计学》，37，391–411。
