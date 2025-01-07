# 《神秘网络》

> 原文：[https://towardsdatascience.com/the-arcane-network-95d3f19749be?source=collection_archive---------3-----------------------#2024-12-02](https://towardsdatascience.com/the-arcane-network-95d3f19749be?source=collection_archive---------3-----------------------#2024-12-02)

![](../Images/4302fc9ae4ee9be837e375bae43e5b52.png)

## 如何使用网络科学和Python绘制出这部流行剧集

[](https://medium.com/@janosovm?source=post_page---byline--95d3f19749be--------------------------------)[![Milan Janosov](../Images/b7ede67b165cdd368d96f13f46c68ccb.png)](https://medium.com/@janosovm?source=post_page---byline--95d3f19749be--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--95d3f19749be--------------------------------)[![面向数据科学](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--95d3f19749be--------------------------------) [Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--95d3f19749be--------------------------------)

·发布于[面向数据科学](https://towardsdatascience.com/?source=post_page---byline--95d3f19749be--------------------------------) ·阅读时长7分钟·2024年12月2日

--

《神秘之境》第二季是Netflix最近的一部热门剧集，改编自全球最受欢迎的在线电子游戏之一《英雄联盟》的宇宙。剧集设定在一个重蒸汽朋克风格的幻想世界中，以惊人的视觉效果和创纪录的预算收尾。作为一名网络和数据科学家，特别热衷于将流行文化项目转化为数据可视化，这就是我在完成最后一季后所需要的一切，目的是绘制出隐藏的联系，并将《神秘之境》的故事情节转化为网络可视化——使用Python。因此，在本教程结束时，您将掌握如何创建并可视化《神秘之境》背后的网络。

然而，这些技能和方法绝不是这部故事所特有的。事实上，它们突出了网络科学提供的一般方法，用于绘制、设计、可视化和解释任何复杂系统的网络。这些系统可以从交通和COVID-19传播的网络模式，到大脑网络，再到各种社交网络，例如《神秘之境》系列中的网络。

*所有图像由作者创作。*

# 1. 收集角色列表

由于我们在这里要绘制出所有角色背后的联系，首先，我们需要获取每个角色的列表。为此，《神秘之境》[粉丝维基](https://arcane.fandom.com/wiki/Category:Characters)网站是一个很好的免费使用信息来源（CC BY-SA 3.0），我们可以通过简单的网页抓取技术轻松访问。具体来说，我们将使用urllib进行下载，使用BeautifulSoup提取每个角色在主角页面上列出的名字和粉丝维基个人资料链接。

首先下载角色列表网站的HTML：

```py
import urllib
import bs4 as bs
from urllib.request import urlopen

url_char = 'https://arcane.fandom.com/wiki/Category:Characters'

sauce = urlopen(url_char).read()
soup  = bs.BeautifulSoup(sauce,'lxml')
```

然后，我提取了所有潜在相关的名字。通过右键点击一个想要的元素（在这个案例中是角色档案），并选择浏览器中的元素检查选项，您可以轻松找出要传递给解析的html（存储在‘soup’变量中）的标签。

从中，我了解到，角色的名字和网址存储在包含‘title=’的行中，但不包含‘:’（对应于类别）。此外，我创建了一个`still_character`标志，它帮助我确定角色列表页面上的哪些子页面仍属于故事中的合法角色。

```py
import re

chars = soup.find_all('li')
still_character = True
names_urls = {}

for char in chars:

    if '" title="' in str(char) and ':' not in char.text and still_character:

        char_name = char.text.strip().rstrip()

        if char_name == 'Arcane': 
            still_character = False        

        char_url = 'https://arcane.fandom.com' + re.search(r'href="([^"]+)"', str(char)).group(1)

        if still_character:
            names_urls[char_name] = char_url
```

前面的代码块将创建一个字典（‘names_urls’），它以每个角色的名字和网址作为键值对存储。现在，让我们快速查看一下我们得到的内容，并打印出名字-网址字典以及它的总长度：

```py
for name, url in names_urls.items():
    print(name, url)
```

这个代码块的输出样本，我们可以测试每个链接——指向每个角色的传记档案：

![](../Images/3ad33d86437ab3146554e91a80d70500.png)

```py
print(len(names_urls))
```

哪个代码单元返回67的结果，这意味着我们需要处理的命名角色总数。这表示我们已经完成了第一项任务——我们有一个全面的角色列表，并且可以轻松访问它们在粉丝维基网站上的完整文本档案。

# 2. 收集档案

为了绘制两个角色之间的关系，我们需要找出一种方法来量化两个角色之间的关系。为了捕捉这一点，我依赖于这两个角色的传记相互提及的频率。在技术层面上，为了实现这一点，我们需要收集我们刚刚获得链接的完整传记。我们将通过简单的网页抓取技术再次获取这些信息，然后将每个网站的源代码分别保存在本地文件中，如下所示。

```py
# output folder for the profile htmls
import os
folderout = 'fandom_profiles'
if not os.path.exists(folderout):
    os.makedirs(folderout)

# crawl and save the profile htmls
for ind, (name, url) in enumerate(names_urls.items()):
    if not os.path.exists(folderout + '/' + name + '.html'):
        fout = open(folderout + '/' + name + '.html', "w")
        fout.write(str(urlopen(url).read()))
        fout.close()
```

到了本节末，我们的文件夹‘fandom_profiles’应包含每个《Arcane》角色的粉丝维基档案——准备好在我们构建《Arcane》网络的过程中进行处理。

# 3. 《Arcane》网络

为了建立角色之间的网络，我们假设两个角色之间的互动强度由每个角色的档案提到另一个角色的次数来表示。因此，网络的节点是角色，节点之间通过基于每个角色的维基网站源引用其他角色维基的次数的强度不同的连接链接。

## 构建网络

在下面的代码块中，我们构建了边列表——包含每个连接的源节点和目标节点（角色），以及这两个角色之间的权重（共同引用频率）的连接列表。此外，为了有效地进行档案内搜索，我创建了一个`names_ids`，它只包含每个角色的特定标识符，而不包括其余的网页地址。

```py
# extract the name mentions from the html sources
# and build the list of edges in a dictionary
edges = {}
names_ids  = {n : u.split('/')[-1] for n, u in names_urls.items()}

for fn in [fn for fn in os.listdir(folderout) if '.html' in fn]:

    name = fn.split('.html')[0]

    with open(folderout + '/' + fn) as myfile:
        text = myfile.read()
        soup  = bs.BeautifulSoup(text,'lxml')
        text = ' '.join([str(a) for a in soup.find_all('p')[2:]])
        soup = bs.BeautifulSoup(text,'lxml')

        for n, i in names_ids.items():

            w = text.split('Image Gallery')[0].count('/' + i) 
            if w>0:
                edge = '\t'.join(sorted([name, n]))
                if edge not in edges:
                    edges[edge] = w
                else:
                    edges[edge] += w

len(edges)
```

当这个代码块运行时，它应该返回大约180条边。

接下来，我们使用 NetworkX 图分析库将边列表转换为图对象，并输出图中的节点和边的数量：

```py
#  create the networkx graph from the dict of edges
import networkx as nx
G = nx.Graph()
for e, w in edges.items():
    if w>0:
        e1, e2 = e.split('\t')
        G.add_edge(e1, e2, weight=w)

G.remove_edges_from(nx.selfloop_edges(G))

print('Number of nodes: ', G.number_of_nodes())
print('Number of edges: ', G.number_of_edges())
```

该代码块的输出：

![](../Images/0efcd63ba520a218c408ab4c57c3bcac.png)

该输出告诉我们，虽然我们从67个角色开始，其中16个角色最终没有与网络中的任何人建立连接，因此构建的图表中节点的数量较小。

## 可视化网络

一旦我们拥有网络，就可以将其可视化！首先，让我们使用 Matplotlib 和 NetworkX 内置工具创建一个简单的网络草图可视化。

```py
# take a very brief look at the network
import matplotlib.pyplot as plt
f, ax = plt.subplots(1,1,figsize=(15,15))
nx.draw(G, ax=ax, with_labels=True)
plt.savefig('test.png')
```

该单元的输出图像：

![](../Images/3b7a5aa717021a4b84c3d48a813ef25d.png)

虽然该网络已经提供了一些关于节目主要结构和最常见特点的线索，但我们可以使用开源网络可视化软件 Gephi 设计出更为详细的可视化。为此，我们首先需要将网络导出为 .gexf 图数据文件，如下所示。

```py
nx.write_gexf(G, 'arcane_network.gexf')
```

现在，关于如何使用 Gephi 可视化此网络的教程：

![](../Images/aae9b3347f0af50a8557d15dbb93153c.png)

YouTube 视频教程: [https://www.youtube.com/watch?v=utm91FhZalQ](https://www.youtube.com/watch?v=utm91FhZalQ)

## 附加内容

这是一个扩展部分，我在视频中提到过。导出包含网络社区指数的节点表后，我使用 Pandas 读取该表，并为每个社区分配了不同的颜色。我从 ChatGPT 那里得到了这些颜色（及其十六进制代码），并要求它们与节目主色调对齐。然后，这段代码将颜色导出——我再次在 Gephi 中使用它来给最终图形上色。

```py
import pandas as pd
nodes = pd.read_csv('nodes.csv')

pink = '#FF4081'
blue = '#00FFFF'
gold = '#FFD700'
silver = '#C0C0C0'
green = '#39FF14'

cmap = {0 : green, 
        1 : pink,
        2 : gold,
        3 : blue, 
    }

nodes['color'] = nodes.modularity_class.map(cmap)
nodes.set_index('Id')[['color']].to_csv('arcane_colors.csv')
```

# 总结

当我们根据发现的社区为网络着色时（社区指的是原始网络中的高度互联子图），我们揭示了四个主要群体，每个群体对应着故事情节中具体的一组角色。并不令人惊讶的是，算法将主角家庭与金克丝、维和范德（粉色）聚集在了一起。然后，我们还看到了扎恩地下人物（蓝色）的群体，如希尔科，而皮尔托弗的精英（蓝色）和军事执法（绿色）也被很好地分组在一起。

这种社区结构的美妙之处和作用在于，虽然这样的解释可以非常容易地将其置于背景中，但通常仅凭直觉很难得出类似的图示。尽管此处呈现的方法清楚地展示了我们如何利用网络科学提取虚拟（或真实）社交系统的隐藏联系，无论是律师事务所的合伙人、会计师事务所的同事，还是一家大型石油公司的HR部门。
