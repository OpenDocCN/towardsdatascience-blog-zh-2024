# 组织 Jupyter Notebook 可视化的技巧与窍门

> 原文：[https://towardsdatascience.com/an-easier-way-to-wrangle-jupyter-notebook-visualizations-620a86cd9279?source=collection_archive---------2-----------------------#2024-01-22](https://towardsdatascience.com/an-easier-way-to-wrangle-jupyter-notebook-visualizations-620a86cd9279?source=collection_archive---------2-----------------------#2024-01-22)

## 通过自动化 matplotlib 输出，优化你的数据科学工作流程——只需要一行代码。下面是具体做法。

[](https://medium.com/@mmore500?source=post_page---byline--620a86cd9279--------------------------------)[![Matthew Andres Moreno](../Images/ca7da9fd9e5c744ee0ebd2aa5d979eb5.png)](https://medium.com/@mmore500?source=post_page---byline--620a86cd9279--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--620a86cd9279--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--620a86cd9279--------------------------------) [Matthew Andres Moreno](https://medium.com/@mmore500?source=post_page---byline--620a86cd9279--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--620a86cd9279--------------------------------) ·阅读时间 6 分钟·2024年1月22日

--

给事物命名很难。在长时间工作后，我们常常会命名为像“`graph7(1)_FINAL(2).png`”和“`output.pdf`”这样高度描述性的文件名。看起来很熟悉吧？

我们可以做得更好——而且实际上很容易。

当我们使用数据导向的“*seaborn*-风格”的绘图机制时，构成描述性文件名的要素就齐备了。一个典型的调用看起来像这样：

```py
sns.scatterplot(data=tips, x="total_bill", y="tip", hue="time")
```

在这里，我们可以看到“`total_bill`”位于***x 轴**上，“`time`”被**颜色编码**，等等。那么，如果我们利用**绘图函数名称**和这些语义化的**列键**来为我们组织输出文件呢？

使用***teeplot***工具时，工作流程是这样的：

```py
import seaborn as sns; import teeplot as tp
tp.save = {".eps": True, ".pdf": True}  # set custom output behavior
```

```py
tp.tee(sns.scatterplot,
   data=sns.load_data("tips"), x="total_bill", y="tip", hue="time")
```

![](../Images/2ff934bb137db56e6f6e5497ab52e969.png)

> teeplots/hue=time+viz=scatterplot+x=total-bill+y=tip+ext=.eps
> 
> teeplots/hue=time+viz=scatterplot+x=total-bill+y=tip+ext=.pdf

在这个例子中，我们实际上做了***三件事***——*1)* 我们在笔记本中渲染了图形，*2)* 我们将可视化保存到文件，并使用了有意义的文件名，*3)* 我们将可视化集成到一个框架中，使得笔记本输出可以在全局范围内进行***管理（***在这种情况下，启用了*eps/pdf* 输出*）。*

本文将解释如何利用***teeplot*** Python 包来更好地组织工作流程，释放你的思维负担，以便专注于更有趣的事情。

我是该项目的主要作者和维护者，我已经在自己的工作流程中使用了几年，并且认为足够有用，值得打包并与社区广泛分享。***teeplot*** 是一个基于 MIT 许可的开源项目。

# teeplot 工作流程

***teeplot*** 旨在简化与使用 *matplotlib*、*seaborn* 和 *pandas* 等库创建的数据可视化的工作。它作为您的绘图调用的封装器，帮助您处理输出管理。

![](../Images/947b3f58acf23f40400530b4c26d3d85.png)

teeplot 工作流示例

以下是 **使用 *teeplot*** 的 **3 个步骤**，

1.  **选择您的绘图函数：** 首先选择您喜欢的绘图函数，无论是来自 *matplotlib*、*seaborn*、*pandas* 等库，还是您自己编写的函数。

1.  **添加您的绘图参数：** 将您的绘图函数作为第一个参数传递给 `tee`，然后是您希望用于可视化的参数。

1.  **自动绘图与保存：** *teeplot* 捕捉您的绘图函数及其参数，执行绘图，然后为您处理图形输出。

就是这样！

接下来，让我们看 **3 个简短的示例**，展示：**a)** *基本使用*，**b)** *自定义后处理*，和 **c)** *自定义绘图函数*。

# 示例 1：使用内建的 pandas 绘图工具

在这个示例中，我们将 DataFrame `df` 的成员函数 `df.plot.box` 作为绘图工具，传入两个语义关键字：“age”和“gender”。*teeplot* 会处理其余部分。

```py
# adapted pandas.pydata.org/docs/reference/api/pandas.DataFrame.plot.box.html
import pandas as pd; from teeplot import teeplot as tp

age_list = [8, 10, 12, 14, 72, 74, 76, 78, 20, 25, 30, 35, 60, 85]
df = pd.DataFrame({"gender": list("MMMMMMMMFFFFFF"), "age": age_list})

tp.tee(df.plot.box,  # plotter...
    column="age", by="gender", figsize=(4, 3))  # ...forwaded to plotter
```

> teeplots/by=gender+column=age+viz=box+ext=.pdf
> 
> teeplots/by=gender+column=age+viz=box+ext=.png

![](../Images/020f0d97db5ab73a7ef2c6468a875d52.png)

# 示例 2：使用 Matplotlib 进行手动调整

无论喜欢与否，从 matplotlib 及其衍生库中获得好结果，通常需要在初次绘图调用后进行一些手动调整。

*teeplot* 完全支持这种模式。只需传递 `teeplot_callback` 参数，teeplot 会返回一个可调用的句柄，以及初始绘图调用的输出。完成图形调整后，只需调用句柄即可像往常一样保存并显示。

```py
# adapted from https://matplotlib.org/stable/tutorials/pyplot.html
from matplotlib import pyplot as plt
import numpy as np; from teeplot import teeplot as tp

# tee output format can be configured globally (or per call to tp.tee)
tp.save = {".eps": True}  # make calls output to .eps only

# set up example data
df = {'weight': np.arange(50), 'profit': np.random.randint(0, 50, 50),
        'carbon': np.random.randn(50)}
df['price'], = df['weight'] + 10 * np.random.randn(50)
df['carbon'] = np.abs(df['carbon']) * 100

# ----- begin plotting -----
saveit, __ = tp.tee(  # --- "saveit" is callback to finalize output
    plt.scatter,  # plotter...
    data=df,  # then plotting kwargs
    x='weight', y='price', c='profit', s='carbon',
    teeplot_callback=True)  # defer plotting to callback

# tweak visualization as you usually would...
plt.xlabel('entry a')  
plt.ylabel('entry b')
plt.gcf().set_size_inches(5, 3)
saveit()  # dispatch output callback
```

> teeplots/c=profit+s=carbon+viz=scatter+x=weight+y=price+ext=.eps

![](../Images/ff2943c73549e3d6bd14cb4efc52cfdc.png)

请注意从上面的 `tp.tee` 调用中解包的 `__` 值。这是因为 `plt.scatter` 的返回值是一个线条集合，对我们的调整没有用处。

# 示例 3：自定义绘图工具

自定义绘图工具的工作方式与外部库的绘图工具相同 —— *teeplot* 能根据绘图函数的名称推断 `viz=` 输出关键字。

```py
from matplotlib import pyplot as plt; import seaborn as sns
from teeplot import teeplot as tp

def cuteplot(subject, descriptor, amount):  # custom plotter
    sns.dogplot()
    plt.gca().text(10, 400, 
        f"{subject} \n is a {descriptor} dog" + "!" * amount,
        color="white", size=40)

tp.tee(cuteplot,  # plotter
    amount=4, subject="who", descriptor="good",  # plotting args
    teeplot_outinclude="amount")  # override to use numeric kwarg in filename
```

> teeplots/amount=4+descriptor=good+subject=who+viz=cuteplot+ext=.png
> 
> teeplots/amount=4+descriptor=good+subject=who+viz=cuteplot+ext=.pdf

![](../Images/93d5c7a746f966f71dc9282d108a9152.png)

通过 seaborn-data，BSD 许可证/版权 © 2012–2023, Michael L. Waskom

向 `sns.dogplot` 致敬……总是那么吵闹！

# 进一步信息

![](../Images/637567441a096bd6d8840f8f4185fc29.png)

就这么简单！

我过去两年一直在定期使用这个工具，最近决定花时间将其打包并分享。希望它能成为社区的一个资产。

*teeplot*库具有一些额外的高级功能，超出了本文的讨论内容，例如通过环境变量进行配置（在CI中非常有用！）。您可以在项目的[*使用指南*](https://github.com/mmore500/teeplot/blob/master/README.rst#usage)和[*API列表*](https://github.com/mmore500/teeplot/blob/master/README.rst#api)中了解更多信息。该项目是一个开源项目，托管在GitHub上的[*mmore500/teeplot*](https://github.com/mmore500/teeplot)*，请考虑留下一个⭐️！*

可以通过`python3 -m pip install teeplot`安装*teeplot*。

# 作者身份

本教程由我贡献，[Matthew Andres Moreno](https://mmore500.com/)。

![](../Images/6a74bc3879e65ad0ded64e1e434af933.png)

我目前在[密歇根大学](https://umich.edu)担任博士后研究员，我的工作得到了Eric和Wendy Schmidt AI in Science博士后奖学金的资助，这是一个Schmidt Futures项目。

我的任命分配在大学的生态与进化生物学系、复杂性研究中心和密歇根数据科学研究所。

在Twitter上找到我 [@MorenoMatthewA](https://twitter.com/morenomatthewa)，在GitHub上找到我 [@mmore500](https://github.com/mmore500/)。

*免责声明*：我就是`teeplot`库的作者。

# 引用

*J. D. Hunter，“Matplotlib：二维图形环境”，《计算科学与工程》，第9卷，第3期，90–95页，2007年\。* [*https://doi.org/10.1109/MCSE.2007.55*](https://doi.org/10.1109/MCSE.2007.55)

*数据结构用于python中的统计计算，McKinney，《第9届科学会议中的Python》，第445卷，2010年\。* [*https://doi.org/*](https://doi.org/) *10.25080/Majora-92bf1922–00a*

*Matthew Andres Moreno。（2023）。mmore500/teeplot。Zenodo。* [*https://doi.org/10.5281/zenodo.10440670*](https://doi.org/10.5281/zenodo.10440670)

*Waskom, M. L.，（2021）。seaborn：统计数据可视化。《开放源软件期刊》，6(60)，3021，* [*https://doi.org/10.21105/joss.03021.*](https://doi.org/10.21105/joss.03021.)

# 附录

要安装本文示例的依赖项，

```py
python3 -m pip install \
    matplotlib `# ==3.8.2`\
    numpy `# ==1.26.2` \
    teeplot `# ==1.0.1` \
    pandas `# ==2.1.3` \
    seaborn `# ==0.13.0`
```

*除非另有说明，所有图片均为作者作品。“dogplot”图片来自seaborn，您可以在此查看seaborn的许可* [*here*](https://github.com/mwaskom/seaborn/blob/master/LICENSE.md)*。*
