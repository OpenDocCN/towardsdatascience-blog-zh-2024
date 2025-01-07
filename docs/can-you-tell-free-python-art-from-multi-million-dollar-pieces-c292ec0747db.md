# 你能从免费的Python艺术作品和价值数百万美元的艺术品中分辨出区别吗？

> 原文：[https://towardsdatascience.com/can-you-tell-free-python-art-from-multi-million-dollar-pieces-c292ec0747db?source=collection_archive---------6-----------------------#2024-11-13](https://towardsdatascience.com/can-you-tell-free-python-art-from-multi-million-dollar-pieces-c292ec0747db?source=collection_archive---------6-----------------------#2024-11-13)

## 跟随我一起，进行一次Python生成艺术的教程，灵感来自皮特·蒙德里安和约瑟夫·阿尔伯斯。从代码到画布，第1部分。

[](https://medium.com/@gordunanna?source=post_page---byline--c292ec0747db--------------------------------)[![Anna Gordun Peiro](../Images/874ec5096d61e6345104026a4cf602e9.png)](https://medium.com/@gordunanna?source=post_page---byline--c292ec0747db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c292ec0747db--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c292ec0747db--------------------------------) [Anna Gordun Peiro](https://medium.com/@gordunanna?source=post_page---byline--c292ec0747db--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c292ec0747db--------------------------------) ·阅读时间：6分钟·2024年11月13日

--

![](../Images/e5c8f2acf35495527ed7830b88b6d0a9.png)

哪三幅作品是真的？图像由作者从[Ioana Cristiana](https://unsplash.com/es/@yoyoqua)的照片创作而来。皮特·蒙德里安的作品属于公共领域。艺术作品在艺术家去世70年后进入**公共领域**。

其中一幅作品是由Python生成的，其余的则是皮特·蒙德里安的原作。哪一幅是与众不同的？我将在几段后给你答案，但首先我需要告诉你为什么我使用Python来创作艺术，而不是使用某个高级的生成式AI工具。

作为一个天生没有艺术天赋的创意艺术爱好者，我把DALL-E和其他工具的发布视为一个机会，让我无需掌握画笔就能在我的整间公寓里挂上“我的”杰作。

事实并非如此，我的墙依然是空白的画布。我没能创作出任何值得展示的作品，但最重要的是——DALL-E破坏了创作的氛围。

为什么？

因为艺术的魔力大多数来自于我们在创作过程中通过感觉去探索的过程。这是一个旅程——而不仅仅是一个结果。AI艺术对我来说显得太过机械，太过随机，太冷冰冰的。

这让我开始思考：是否存在一个理想的中间点？是否有办法创作出既随机又可控的生成艺术，同时还能获得完成作品时的那份多巴胺/自豪感？更重要的是——没有实际的艺术技巧？

在这篇文章中，我将展示如何创作出两幅值得进入博物馆的艺术作品，我们将揭示哪一幅是伪作。

# 如何创作皮特·蒙德里安的伪作

对于我的第一幅生成艺术作品，我从抽象艺术先驱皮特·蒙德里安（Piet Mondrian）那里获得了灵感。他的作品是由线条、颜色和形状的抽象组合呈现的。

这是他一些最具代表性的作品的小样本：

![](../Images/dd87d7b154512660e181305b8079d5ec.png)

图片由作者创建。皮特·蒙德里安的作品属于公有领域。

> 你已经知道哪个是冒名顶替者了吗？

如果你有兴趣尝试，只需安装“[mondrian-maker](https://pypi.org/project/mondrian-maker/#description)”Python包，就能像这样绘制新的作品：

> mondrian-maker包由Andrew Bowen创建，并在GNU通用公共许可证下发布。

```py
from mondrian_maker.mondrian import mondrian

m = mondrian()
m.make_mondrian()
```

![](../Images/b546b73a88ce231dfb4c4c3d66240222.png)

由作者生成的构成

乐趣的一部分在于每次调用`make_mondrian()`时都会生成一幅新的作品。并非所有作品都“值得画出来”，所以我生成了100个并选择了我最喜欢的。

```py
for i in range(0,100):
    f,ax=m.make_mondrian()
    f.savefig(f"{i}_mondrian.png")
```

那么Python或原始游戏的答案是什么？冒名顶替者是**从左边数起的第三个**😉。剩下的作品是（从左到右）：*红蓝构成一号（1938）*；*红黄蓝构成（1942）*；*构成十号（1939）*

![](../Images/540fdff222e4e84711eed0e0899e6e73.png)

拼图解决方案！图片由作者生成。皮特·蒙德里安的作品属于公有领域

> 你猜对了吗？在评论中告诉我！

如果你想知道如何重现另一幅价值千美元的艺术品，继续阅读：

# 约瑟夫·阿尔伯斯——《方形的致敬》

虽然蒙德里安的作品确实引起了我的注意，但我想从零开始，做一些属于我自己的东西。这就是为什么我转向了约瑟夫·阿尔伯斯的*《方形的致敬》*系列。我被他玩弄透视和色彩的方式所吸引，而且他的“简单”外观给人一种感觉，仿佛是进入的正确地方。自己判断一下吧：

现在，在我们开始绘制方块之前，有两个关于Python生成艺术的关键秘密你应该知道：

+   **可重复性：** 我们希望我们的艺术作品具有随机性，但同时也能再次生成完全相同的画作。通过使用*numpy.random.seed()*，我们可以确保随机数在不同的运行中保持一致。

```py
import numpy as np

constant=12
np.random.seed(constant)

# From now on all generated random numbers are reproducible if constant=12
# To get different random numbers, choose a new constant
```

+   **色彩理论：** 艺术家使用色彩组合来生成视觉上令人愉悦的色彩调色板。实现这一目标的编程秘密是使用[*MetBrewer*](https://github.com/BlakeRMills/MetBrewer)——一个Python库，包含56种美丽的调色板，灵感来自纽约大都会艺术博物馆的作品。

![](../Images/496a58fb224ac8df7d72207c266c8f0e.png)

来自[MetBrewer](https://github.com/BlakeRMills/MetBrewer)的截图（创作共用许可证）

```py
from met_brewer import met_brew
palette=met_brew(name="Hokusai3", brew_type="discrete")
```

🎨现在我们准备开始绘画啦！🎨

> 剧透警告：接下来的代码块揭示了如何创建类似“方形致敬”的画作，如果你更愿意自己先试试，可以跳过这些代码。

1- 我首先构建一个函数来生成以下内容：

+   方块的四个边缘（x0，x1，y0，y1）

+   每个方块的随机颜色

```py
from numpy import random

def rectangle_generator(palette): 
    rectangle=[]

    big={'x0': 0, 'y0': 0, 'x1': 1, 'y1': 1,'color':palette[random.randint(len(palette))]} 
    rectangle.append(big)

    middle={'x0': 0.1, 'y0': 0.05, 'x1': 0.9, 'y1': 0.85,'color':palette[random.randint(len(palette))]}
    rectangle.append(middle)

    small={'x0': 0.2, 'y0': 0.1, 'x1': 0.8, 'y1': 0.7,'color':palette[random.randint(len(palette))]}
    rectangle.append(small)

    tiny={'x0': 0.3, 'y0': 0.15, 'x1': 0.7, 'y1': 0.55,'color':palette[random.randint(len(palette))]}
    rectangle.append(tiny)

    return rectangle
```

2- 然后，我用 Plotly 绘制了每个正方形的坐标  

```py
import plotly.graph_objects as go
import plotly.express as px
import numpy as np
from met_brewer import met_brew
import plotly.io as pio

#For reproducibility
np.random.seed(73)

#Choose a beautiful palette from met_brewer
palette=met_brew(name="Morgenstern", n=30,brew_type="continuous")       

# Generate rectangles with defined palette
rectangles=rectangle_generator(palette)

# Plot!

# Setting canvas

fig=go.Figure()

fig.update_layout(
    autosize=False,
    width=800,
    height=800,
    )

fig.update_xaxes(range=[0, 1], showgrid=False,visible=False)
fig.update_yaxes(range=[0, 1],visible=False)

# Start painting

for rect in rectangles:
    fig.add_shape(
        type="rect",
        x0=rect['x0'],y0=rect['y0'],
        x1=rect['x1'],y1=rect['y1'],
        line=dict(color=rect['color'],
                    width=2,),
        fillcolor=rect['color']
        )

fig.update_shapes(dict(xref='x', yref='y'))
fig.show()
pio.write_image(fig, "73morgensternplot.png", format="png", width=800, height=800, scale=3)
```

这是最终的作品！

![](../Images/e01969a8020f49d9b21c4baf14d2ca89.png)  

我称这幅画为：阳光明媚的一天的冰激凌。图片由作者提供  

让我告诉你为什么我真心喜欢设计这件艺术品——也希望你能喜欢：  

首先，我必须破解正方形尺寸的代码，确保它们与原作的透视相匹配。接着是有趣（且略微有些痴迷）的部分：玩弄颜色调色板，等待那个魔法般的“啊哈”时刻，直到一切都恰如其分地融合在一起。  

我并没有停在那里。我生成了超过100幅不同种子常数的画作，基本上成了自己的艺术策展人，找到“那幅画”。  

最棒的部分？我跳过了几个小时的画画挫败感，最终得到了一个“还不错”的作品。而且，我没有被一个被过度炒作的生成AI工具所打击。相反，我让自己的想象力驰骋，创作出了我愿意自豪地挂在墙上的作品——甚至可能会购买。  

在我看来，艺术品加上画框看起来更加高雅，也更显贵气：  

![](../Images/b8c25fa2dd9f102d561aca3ca58d48e8.png)  

装框画作，顺便提一下，我用 levelframes.com 生成了这幅画。图片由作者提供  

![](../Images/1d74d070fbb88b16045bd0fbe047330a.png)  

图片由作者生成  

这是一个新系列的第一篇文章：**从代码到画布**。我愿意接受关于你希望我编码再现的艺术作品的建议，随时留下评论！别忘了关注——你的空白墙壁会感谢你。  

*本文中的所有图片均由作者提供，除非是皮特·蒙德里安的作品，这些作品属于公共领域。*  
