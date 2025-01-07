# 谁真正拥有你预订的Airbnb房源？——营销认知与数据分析现实

> 原文：[https://towardsdatascience.com/who-really-owns-the-airbnbs-youre-booking-marketing-perception-vs-data-analytics-reality-94407a32679c?source=collection_archive---------0-----------------------#2024-10-03](https://towardsdatascience.com/who-really-owns-the-airbnbs-youre-booking-marketing-perception-vs-data-analytics-reality-94407a32679c?source=collection_archive---------0-----------------------#2024-10-03)

## 在过去的20年里，Airbnb投入了数十亿美元用于品牌建设，宣扬其作为一种真实、新颖、社区主导的替代酒店的形象。但，它真的是这样吗？

[](https://medium.com/@gordunanna?source=post_page---byline--94407a32679c--------------------------------)[![Anna Gordun Peiro](../Images/874ec5096d61e6345104026a4cf602e9.png)](https://medium.com/@gordunanna?source=post_page---byline--94407a32679c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--94407a32679c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--94407a32679c--------------------------------) [Anna Gordun Peiro](https://medium.com/@gordunanna?source=post_page---byline--94407a32679c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--94407a32679c--------------------------------) ·阅读时长8分钟·2024年10月3日

--

在这篇文章中，我将使用[InsideAirbnb](https://insideairbnb.com/)的数据来揭示Airbnb的所有权模式。接着，我会带你走一遍我得出结论的过程，这样你也可以对你所在的城市进行类似的分析。

> [**Inside Airbnb**](https://insideairbnb.com/)是一个使命驱动的项目，提供关于Airbnb对住宅社区影响的数据和倡导。所有数据都使用[创意共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可。

我相信你一定见过那些Airbnb广告，广告中是面带笑容的房东在充满旅游气息的邻里欢迎客人。如果你没见过，可以看一下这个：

我个人在Airbnb上已经待了超过5年，尽管我住过一些非常独特和真实的地方（我最喜欢的必须是门外有马的牧羊人小屋），但我最近意识到，我最近的体验并没有达到Airbnb广告所宣传的预期。

上周我在Airbnb住了一个周末。我进行了一次小型寻宝活动，解锁了许多挂锁才能找到钥匙。我打开门，进入了一个一尘不染的公寓，和照片中的一模一样。但我很惊讶地发现没有书籍或蜡烛。它完全空荡荡的，就像从来没有人住过一样。第二天，我没有遇到任何邻居。我没有收到房东的餐馆推荐。最后一天离开时，我把钥匙放在里面，没有人向我道别。我想，我是不是住在了一个酒店里？这就是Airbnb变成的样子吗？

# **Airbnb希望你认为的事情**

Airbnb专注于像当地人一样体验生活，或者他们所说的：“真正属于的感觉”，这一点在他们的许多广告活动中得到了体现：

住在那里，Airbnb的2016年广告活动

> 住在那里，因为当你住在巴黎时，你有自己的家。

这则精心策划的广告完美地体现了Airbnb的价值主张：像当地人一样生活，抛开那些没有灵魂、标准化的酒店预订。继承浪漫化的本地体验之后，Airbnb的营销描绘了房东与客人之间值得信赖和真诚的关系。

所以不奇怪，在我上次旅行时，我感觉有些被欺骗了。我错过了完整的Airbnb体验。这激励我研究Airbnb的房源数据，以回答我脑海中纷飞的问题：

+   我们的Airbnb体验是否已经变得标准化，并优化了最大利润？

+   Airbnb社区的本质消失了吗？

# **现实**

多房产所有权是我们这些客人是否能真正体验到“Airbnb体验”的转折点。

**为什么？**

因为，当我们从“房地产投资者”或拥有大量房源的房东那里预订住宿时，我们很可能是在入住一种类似酒店的商业模式，这些人购买了遍布城市的公寓来出租。在这种情况下，我们房东的动机主要是经济利益。我们的住宿将更多地被优化为盈利，而不是Airbnb通过其营销承诺的那种个人化、地方性的氛围。从本质上讲，唯一与酒店住宿的真实区别就是有一个厨房可供使用。

为了分析不同层次的多房产所有权，我将拥有1-2个房源的房东归类为个人，而拥有超过3个房源的则归类为专业房东。

为了证明我的观点——Airbnb体验可能只是一个感知，我访问了来自欧洲15个城市的数据集，并进行了数据分析，以捕捉专业房东与个人房东的普及情况（*所有详细信息和代码将在下一节提供*）。以下是结果：

![](../Images/a92b14256e5716d6095296620a117f55.png)

每个条形图代表一个不同的城市，且可视化被分为两部分：左侧（**绿色**）表示由**个人**拥有的房源百分比，右侧（**亮色**）表示由**专业**房东拥有的房源百分比。每个房东的房产数量被分为1, 2, +2, +5, +10和+100类。我选择的一些城市有严格的住宿监管政策，限制了每年可以出租的天数（例如：佛罗伦萨、伦敦、爱丁堡、罗马、巴黎、哥本哈根和阿姆斯特丹）。最后，图表的排序是根据专业房东拥有的份额从高到低。

从图表中我们可以看到：

+   在我的分析中，大多数Airbnb房源是由专业房东拥有的。

+   **巴塞罗那**的专业房东拥有份额最高，达到74%，而个人房东占26%。

+   **阿姆斯特丹**的专业房东拥有份额最低，仅为5%，其余95%的房源由个人拥有，这可能是由于政府严格的租赁法规，限制每年最多只能出租30天。

+   **放松的Airbnb监管**使得专业房东可以主导市场。

+   **严格Airbnb监管**的城市往往将专业房东排除在外。

+   似乎没有设定每个房东最多可以拥有多少房源的限制。

根据Airbnb的说法，我们与他们的体验应该是真实的。然而，通过查看数据，我们发现**五分之三的房源**是由租赁**投资者**拥有的类似酒店的公寓（除非租赁受到监管）。这种营销认知与现实之间的脱节，不仅突显了品牌营销在我们观感中的影响力，还强调了InsideAirbnb等举措的重要性。

我现在知道下次入住时该期待什么了——你呢？你会体验到完整的挂锁寻宝体验吗？如果你感兴趣，可以深入了解一下编码！

# **如何复制我的研究到你的城市？**

我写了一份指南，介绍了如何执行数据分析并生成上一节中的图表。我正在使用巴塞罗那市的数据集来说明不同的数据分析步骤。

下载了来自InsideAirbnb的listings.csv.gz文件后，我直接在Python中打开了它们，没有解压。我在这个项目中使用了Polars，只是为了熟悉相关命令（*你也可以使用Pandas*）：

```py
import polars as pl
df=pl.read_csv('listings.csv.gz')
```

以下是我用于分析的城市以及每个数据集中的房源数量：

![](../Images/e6ebbf09b2cb1ede2bb6782ea7caf50b.png)

用于分析的城市。图片由作者提供

如果你喜欢这个紧凑的气泡图，记得查看我上一篇文章：

[](/i-found-a-hidden-gem-in-matplotlibs-library-packed-bubble-charts-in-python-d0f5d892beb7?source=post_page-----94407a32679c--------------------------------) [## 我在Matplotlib库中发现了一个隐藏的宝石：Python中的紧凑气泡图

### 是否曾想过设计那些漂亮的基于Tableau的紧凑气泡图？跟随教程，学习如何使用Matplotlib…

[towardsdatascience.com](/i-found-a-hidden-gem-in-matplotlibs-library-packed-bubble-charts-in-python-d0f5d892beb7?source=post_page-----94407a32679c--------------------------------)

首次查看数据集，它大致是这样的：

![](../Images/458805f8e6b8da369b42f634448630ab.png)

数据框片段。图片由作者提供

内容基于每个房源URL中提供的数据，其中包含每个房源的行和75列，详细说明了从描述、邻里、卧室数量，到评分、最少入住天数和价格等信息。

如前所述，尽管该数据集具有无限潜力，但我将专注于多房源所有权的分析。

+   **数据分析**

下载数据后，数据清理工作几乎不用做：

1- 过滤“property_type”为“Entire rental units”以筛选掉房间类房源。

2- 过滤“has_availability”为“t”（True），以去除非活跃的房源。

```py
import polars as pl
#I renamed the listings.csv.gz file to cityname_listings.csv.gz.
df=pl.read_csv('barcelona_listings.csv.gz')
df=df.filter((pl.col('property_type')=="Entire rental unit")&(pl.col('has_availability')=="t"))
```

为了处理数据，我将原始数据转化为不同的结构，便于量化数据集中有多少房源是由同一位房东拥有。换句话说，衡量有多少巴塞罗那的房源是由多房东拥有的。这是我的处理方法：

+   对“host_id”列执行了`value_counts`操作，以统计同一host_id拥有多少个房源。

+   创建了5个不同的区间来量化多房源级别：1处房源、2处房源、+2处房源、+5处房源、+10处房源和+100处房源。

+   对每个host_id的房源数量（连续值）进行`polars.cut`操作，将其分成我设定的离散类别（区间）。

```py
host_count=df['host_id'].value_counts().sort('count')
breaks = [1,2,5,10,100] 
labels = ['1','2','+2','+5','+10','+100']
host_count = host_count.with_columns(
    pl.col("count").cut(breaks=breaks, labels=labels, left_closed=False).alias("binned_counts")
)
host_count
```

这是结果。Host_id、房源数量和分类。显示的数据对应于巴塞罗那市。

![](../Images/b69ac293e595f0df8b21503008cdbd6a.png)

房源数量区间统计。图片由作者提供

> 请花一点时间注意到host_id为346367515（列表中的最后一位）拥有406个房源？此时，Airbnb社区的氛围开始感觉像一种幻觉了吗？

为了得到一个不依赖于host_id的城市概况，我将host_count数据框与原始数据框合并，以便将每个房源匹配到正确的多房源标签。之后，只需要对每个房源标签进行简单的`value_counts()`操作，就能得到属于该类别的房源总数。

我还添加了一个百分比列，以量化每个标签的权重。

```py
df=df.join(host_count,on='host_id',how='left')

graph_data=df['binned_counts'].value_counts().sort('binned_counts')
total_sum=graph_data['count'].sum()
graph_data=graph_data.with_columns(((pl.col('count')/total_sum)*100).round().cast(pl.Int32).alias('percentage'))
```

![](../Images/51dc7c597be1a18da6d02d8ef09553bd.png)

最终数据结果。图片由作者提供

别担心，我也是一个视觉型的人，这里是表格的图形展示：

```py
import plotly.express as px

palette=["#537c78","#7ba591","#cc222b","#f15b4c","#faa41b","#ffd45b"]

# I wrote the text_annotation manually cause I like modifying the x position
text_annotation=['19%','7%','10%','10%','37%','17%']
text_annotation_xpos=[17,5,8,8,35,15]
text_annotation_ypos=[5,4,3,2,1,0]
annotations_text=[
    dict(x=x,y=y,text=text,showarrow=False,font=dict(color="white",weight='bold',size=20))
    for x,y,text in zip(text_annotation_xpos,text_annotation_ypos,text_annotation)

]

fig = px.bar(graph_data, x="percentage",y='binned_counts',orientation='h',color='binned_counts',
             color_discrete_sequence=palette,
            category_orders={"binned_counts": ["1", "2", "+2","+5","+10","+100"]}
            )
fig.update_layout(
    height=700,
    width=1100,
    template='plotly_white',
    annotations=annotations_text,
    xaxis_title="% of listings",
    yaxis_title="Number of listings owned by the same host",
    title=dict(text="Prevalence of multi-property in Barcelona's airbnb listings<br><sup>% of airbnb listings in Barcelona owned by multiproperty hosts</sup>",font=dict(size=30)),
    font=dict(
        family="Franklin Gothic"),
    legend=dict(
        orientation='h',
        x=0.5,
        y=-0.125,
        xanchor='center',
        yanchor='bottom',
        title="Number of properties per host"
    ))

fig.update_yaxes(anchor='free',shift=-10,
                tickfont=dict(size=18,weight='normal'))

fig.show()
```

![](../Images/4072dc64b528545c1e669e00d1d238eb.png)

巴塞罗那Airbnb的多房源现象。图片由作者提供

回到最初的问题：我怎么得出结论，巴塞罗那的Airbnb精髓已经丧失？

+   大多数房源（**64%**）属于拥有超过5处房源的房东。**17%**的房源由拥有超过100处房源的房东管理。

+   只有**26%**的房源属于仅拥有1或2处房源的房东。

如果你希望同时分析多个城市，可以使用一个一次性完成所有清理和处理的函数：

```py
import polars as pl

def airbnb_per_host(file,ptype,neighbourhood):
    df=pl.read_csv(file)
    if neighbourhood:
        df=df.filter((pl.col('property_type')==ptype)&(pl.col('neighbourhood_group_cleansed')==neighbourhood)&
                    (pl.col('has_availability')=="t"))
    else:
        df=df.filter((pl.col('property_type')==ptype)&(pl.col('has_availability')=="t"))

    host_count=df['host_id'].value_counts().sort('count')
    breaks=[1,2,5,10,100] 
    labels=['1','2','+2','+5','+10','+100']
    host_count = host_count.with_columns(
    pl.col("count").cut(breaks=breaks, labels=labels, left_closed=False).alias("binned_counts"))

    df=df.join(host_count,on='host_id',how='left')

    graph_data=df['binned_counts'].value_counts().sort('binned_counts')
    total_sum=graph_data['count'].sum()
    graph_data=graph_data.with_columns(((pl.col('count')/total_sum)*100).alias('percentage'))

    return graph_data
```

然后为你文件夹中的每个城市运行它：

```py
import os
import glob

# please remember that I renamed my files to : cityname_listings.csv.gz
df_combined = pl.DataFrame({
        "binned_counts": pl.Series(dtype=pl.Categorical),
        "count": pl.Series(dtype=pl.UInt32),
        "percentage": pl.Series(dtype=pl.Float64),
        "city":pl.Series(dtype=pl.String)
    })

city_files =glob.glob("*.csv.gz")

for file in city_files:
    file_name=os.path.basename(file)
    city=file_name.split('_')[0]
    print('Scanning started for --->',city)

    data=airbnb_per_host(file,'Entire rental unit',None)

    data=data.with_columns(pl.lit(city.capitalize()).alias("city"))

    df_combined = pl.concat([df_combined, data], how="vertical")

print('Finished scanning of ' +str(len(city_files)) +' cities')
```

请访问我的GitHub仓库，获取构建此图表的代码，因为代码有点长，无法在此附上：

![](../Images/a92b14256e5716d6095296620a117f55.png)

最终对比图。图片由作者提供

[](https://github.com/gopean-art/Airbnb-analysis?source=post_page-----94407a32679c--------------------------------) [## GitHub - gopean-art/Airbnb-analysis: airbnb文章

### airbnb文章。通过在GitHub上创建一个账户，参与gopean-art/Airbnb-analysis的开发。

github.com](https://github.com/gopean-art/Airbnb-analysis?source=post_page-----94407a32679c--------------------------------)

就这样！

在评论中告诉我你的想法，同时祝你在下次入住时，拥有一个非常充实和真实的Airbnb体验😉

*本文中的所有图片和代码均由作者提供*
