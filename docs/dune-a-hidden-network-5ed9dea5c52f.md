# 沙丘——一个隐藏的网络

> 原文：[https://towardsdatascience.com/dune-a-hidden-network-5ed9dea5c52f?source=collection_archive---------2-----------------------#2024-03-19](https://towardsdatascience.com/dune-a-hidden-network-5ed9dea5c52f?source=collection_archive---------2-----------------------#2024-03-19)

![](../Images/15da4170bfe982fa87481d66e4312b6e.png)[](https://medium.com/@janosovm?source=post_page---byline--5ed9dea5c52f--------------------------------)[![Milan Janosov](../Images/b7ede67b165cdd368d96f13f46c68ccb.png)](https://medium.com/@janosovm?source=post_page---byline--5ed9dea5c52f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5ed9dea5c52f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5ed9dea5c52f--------------------------------) [Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--5ed9dea5c52f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ed9dea5c52f--------------------------------) ·9分钟阅读·2024年3月19日

--

# 在本文中，我们与[Patrik Szigeti](https://medium.com/u/6f5de121c728?source=post_page---user_mention--5ed9dea5c52f--------------------------------)合作，设计了一种数据和网络方法，并通过图形可视化来概述原始《沙丘》三部曲背后的复杂社交网络。

随着2021年《沙丘》在票房和评论界的成功，2024年《沙丘：第二部》成为最受期待的电影之一，并没有让人失望。根据本文写作时的情况，它的票房收入不断攀升，并且在烂番茄和IMDb上的评分都超过了前作。凭借其不断变化的政治格局，《沙丘》是一个通过网络科学深入挖掘的完美系列。在这篇简短的文章中，我们旨在基于弗兰克·赫伯特的前三本书——《沙丘》（1965）、《沙丘救世主》（1969）和《沙丘之子》（1976），探索帝国不同家族和人物之间的联系。

在本文的第一部分，我们介绍了一种基于Python的方法，用于从[Dune Wiki](https://dune.fandom.com/wiki/Dune_Wiki)收集角色档案数据，并将这些档案转化为一个引人注目的网络图。然后，在第二部分——这一部分包含大量剧透——我们深入探讨了网络的深度，提取了与《沙丘》第一部三部曲相关的所有故事。

*所有图片均由作者制作。*

# 1 构建网络

首先，我们使用 Python 收集了沙丘角色的完整列表。然后，我们从每个角色的粉丝维基站点下载他们的个人简介，并统计每个角色的故事中提到其他角色故事的次数，假设这些提及编码了任意两个角色之间的互动。接着，我们将使用网络科学将这些关系转化为复杂的图形。

**1.1 收集角色列表**

首先，我们从《沙丘》粉丝维基网站收集了所有相关角色的列表。具体来说，我们使用 urllib 和 bs4 提取了每个被提及且拥有自己维基页面的角色的名称和粉丝维基 ID。我们为前三本书做了这些：**沙丘**、**沙丘救世主** 和 **沙丘之子**。这三本书涵盖了阿特雷ides家族的崛起。

来源：

+   [https://dune.fandom.com/wiki/Dune_(novel)](https://dune.fandom.com/wiki/Dune_(novel))

+   [https://dune.fandom.com/wiki/Dune_Messiah](https://dune.fandom.com/wiki/Dune_Messiah)

+   [https://dune.fandom.com/wiki/Children_of_Dune_(novel)](https://dune.fandom.com/wiki/Children_of_Dune_(novel))

首先，下载角色列表站点的 HTML 文件：

```py
dune_meta = {
    'Dune': {'url': 'https://dune.fandom.com/wiki/Dune_(novel)'},
    'Dune Messiah': {'url': 'https://dune.fandom.com/wiki/Dune_Messiah'},
    'Children of Dune': {'url': 'https://dune.fandom.com/wiki/Children_of_Dune_(novel)'}
}

for book, url in dune_meta.items():
    sauce = urlopen(url['url']).read()
    soup  = bs.BeautifulSoup(sauce,'lxml')
    dune_meta[book]['chars'] = soup.find_all('li')
```

稍微手动调整角色名称和 ID：

```py
dune_meta['Dune']['char_start'] = 'Abulurd'
dune_meta['Dune']['char_end'] = 'Arrakis'
dune_meta['Dune Messiah']['char_start'] = 'Abumojandis'
dune_meta['Dune Messiah']['char_end'] = 'Arrakis'
dune_meta['Children of Dune']['char_start'] = '2018 Edition'
dune_meta['Children of Dune']['char_end'] = 'Categories'
```

然后，我们提取了所有可能相关的名称和对应的个人资料 URL。在这里，我们手动检查了角色名称开始的标签块（例如，与角色列表站点的大纲不同）。此外，我们决定放弃标记为‘XD’和‘DE’的角色，它们属于扩展系列，以及那些在某本书中仅“提及”的角色：

```py
for k, v in dune_meta.items():
    names_urls = {}
    keep_row = False
    print(f'----- {k} -----')
    for char in v['chars']:
        if v['char_start'] in char.text.strip():
            keep_row = True
        if v['char_end'] in char.text.strip():
            keep_row = False
        if keep_row and 'Video' not in char.text:
            try:
                url = 'https://dune.fandom.com' + str(char).split('href="')[1].split('" title')[0]
                name = char.text.strip()
                if 'wiki' in url and 'XD' not in name and 'DE' not in name and '(Mentioned only)' not in name:
                    names_urls[name] = url
                    print(name)
            except:
                pass
    dune_meta[k]['names_urls'] = names_urls
```

这个代码块将输出角色列表，如：

![](../Images/7e6b5e9b0d8ed11ae35d2bd9eb9cd2ec.png)

提取名称的示例。

最后，我们检查收集到的角色数量，并保存它们的个人资料 URL 和标识符，以便下一个小节使用。

```py
dune_names_urls = {}
for k, v in dune_meta.items():
    dune_names_urls.update(dune_meta[k]['names_urls'])

names_ids  = {n : u.split('/')[-1] for n, u in dune_names_urls.items()}

print(len(dune_names_urls))
```

该单元输出的结果，显示了119个角色及其个人资料 URL：

![](../Images/7e61799e86e6600f8898effb400031cc.png)

**1.2 下载角色资料**

我们的目标是绘制沙丘角色的社交网络——这意味着我们需要弄清楚谁和谁互动。在前一小节中，我们获得了所有“谁”的列表，现在我们将获取有关他们个人故事的信息。我们将通过再次使用简单的网页抓取技术来获取这些故事，然后将每个角色个人站点的来源保存在本地的单独文件中：

```py
# output folder for the profile htmls
folderout = 'fandom_profiles'
if not os.path.exists(folderout):
    os.makedirs(folderout)

# crawl and save the profile htmls
for ind, (name, url) in enumerate(dune_names_urls.items()):
    if not os.path.exists(folderout + '/' + name + '.html'):
        try:
            fout = open(folderout + '/' + name + '.html', "w")
            fout.write(str(urlopen(url).read()))
        except:
            pass
```

运行此代码的结果将是我们本地目录中的一个文件夹，里面包含每个选定角色的粉丝维基站点资料。

## 1.3 构建网络

为了构建角色之间的网络，我们通过以下逻辑计算每个角色的维基页面源中引用其他角色维基标识符的次数。在这里，我们构建了边列表——一个包含连接的源节点和目标节点（角色），以及它们之间的权重（共同引用频率）的列表。

```py
# extract the name mentions from the html sources
# and build the list of edges in a dictionary
edges = {}

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

一旦运行此代码块，我们将得到连接119个《沙丘》角色的边数，结果为307。

接下来，我们使用NetworkX图分析库将边列表转换为图对象，并输出图中节点和边的数量：

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

该代码块的结果：

![](../Images/aa07ed552c575246b9ba952c33094bbc.png)

节点数量仅为72，这意味着有47个角色在他们的——可能相当简短——维基个人资料中没有与任何中心成员相连。此外，我们还看到边的数量减少了四个，因为移除了一些自环。

让我们使用内置的Matplotlib绘图工具简要查看该网络：

```py
# take a very brief look at the network
import matplotlib.pyplot as plt
f, ax = plt.subplots(1,1,figsize=(15,15))
nx.draw(G, ax=ax, with_labels=True)
```

这个单元格的输出：

![](../Images/9e39fce207d89c086a0d9021a20011bb.png)

《沙丘》角色的初始网络可视化。

虽然这个可视化已经显示了一些网络结构，但我们通过以下代码行将图导出为Gephi文件，并设计了附图中的网络（关于这种网络可视化的教程将是即将发布的文章主题）：

```py
nx.write_gexf(G, 'dune_network.gexf')
```

完整的《沙丘》网络：

![](../Images/ecc0ef482242cf4d3dda675e0ed11865.png)

《沙丘》角色的完整网络。网络对应于网络社区（密集互联的子图），经过一些小的手动调整，而节点的大小根据它们的连接数来确定。

# 2 阅读网络

> 警告：以下段落包含《沙丘》系列前三本书的剧透。两部电影（《沙丘》和《沙丘：第二部》）基于第一本书。

我们发现保罗·阿特雷德（也被称为利桑·阿尔-盖布、穆阿德·迪布、乌苏尔、克维萨茨·哈德拉赫等）位于网络的中心也就不足为奇了。他是第一本书（和电影）的主角，是一个中心人物，最终成为帝国的皇帝。在第二本书《沙丘救世主》中，我们遇到了一个不同的保罗，一个在多年的战斗和被预知之力所诅咒后，作为一个盲眼的弗雷曼走进沙漠，献身给沙赫·胡鲁德的人。然后，他在《沙丘之子》中以传教士的身份出现，一个在沙漠中活动和传教的神秘人物，最终迎来自己的结局。在这段旅程中，他与许多其他角色交织在一起。这通过他的所谓“自我网络”完美地体现出来——即包含他所有连接以及这些连接之间的子图——这个网络包含了大约一半的节点和64%的边。下面的图示展示了这一点。

![](../Images/9ab29b6bb8fda8e979e04b9846d23b69.png)

保罗的自我网络。

当我们继续阅读网络时，我们可以看到阿特雷ides家族位于其中，当然，围绕着保罗的是他的家人。他的父母，杰西卡夫人，莱托·阿特雷ides一世的妾，以及本·杰瑟里特教会的女祭司。杰西卡是哈科宁家族弗拉基米尔·哈科宁的女儿，这为我们提供了黄色和浅蓝色节点群体之间的第一次联系。我们可以看到保罗与他的弗雷曼妾侍查尼之间有着强烈的联系，进一步连接到他们的孩子——莱托二世和加尼玛。保罗还与他的导师和好友邓肯·爱达荷和格尔尼·哈雷克有着密切的关系，以及女祭司盖尤斯·海伦·莫希姆，她在各本书中不断出现，进一步巩固了本·杰瑟里特的事业。

即使这个网络显然是以保罗为中心，我们也能看到阿科里诺家族（棕色）、哈科宁家族（浅蓝色）和弗雷曼家族（蓝色）的明显分组，但真正有趣的是，这个基于维基百科文章创建的简单网络告诉我们，在这三本书的情节展开过程中，有多少内容。

我们看到了利耶特·凯恩斯，他是弗雷曼的事实领导人和植物学家，他的梦想是看到荒芜的阿拉基斯星球变得富饶，拥有绿草如茵的牧场和充足的水源。他的女儿查尼通过斯蒂尔加与保罗的生活中重要人物、一个宗教信徒联系在一起，通过他与所有弗雷曼建立了联系。然而，在他们之间有一个人——斯卡特尔，他在穆阿迪布的圣战后通过海特（邓肯·爱达荷的高拉——一个由死者复生的人工人类）密谋摧毁王室。对于那些只看过电影的人来说，邓肯是我们网络中如此核心人物可能令人惊讶，但在担任阿特雷ides家族的剑术大师，并在阿拉基斯的沙漠战争中阵亡后，他以高拉的身份回归，并扮演了重要角色——娶了杰西卡夫人的女儿、保罗的妹妹阿丽亚·阿特雷ides。

电影观众可能会对图费尔·哈瓦特作为哈科宁家族一员的设定感到惊讶。他是一个负责阿特雷ides家族安全的门塔特（Mentat），但在哈科宁家族取代阿特雷ides家族成为阿拉基斯的统治者后，他被迫为他们服务，并策划了反对他们的阴谋，尽管他的真正目标始终是复仇他心爱的公爵之死，他认为是杰西卡夫人幕后策划了这一袭击。他后来通过拒绝杀死保罗而自杀来获得救赎。

然而，这个网络最引人入胜的部分是，无论一个角色的节点看起来多么微小，这并不意味着他们在情节中没有扮演重要角色。他们可能对错误的观众说了正确的话（例如伊克斯星的布朗索声称保罗失去了他人性中的某些重要部分，才成为穆阿迪布），成为阿丽亚·阿特雷迪的情人（贾维德），或密谋杀害阿特雷迪双胞胎，莱托和甘妮玛（泰耶卡尼克）。我们可以一直列举下去，这些只是弗兰克·赫伯特的《沙丘》中错综复杂的政治景观的一些迷人例子。

# 3 结论

本文的目标是搭建数据科学爱好者与《沙丘》爱好者之间的桥梁——并可能娱乐已经存在的这两个社区的交集部分。首先，我们展示了一个相对通用的 Python 框架，使我们能够绘制出任何我们遇到的粉丝维基网站的社交网络。其次，我们还详细解释了这些网络可视化如何展现整个故事的发展——一幅图胜过千言万语，甚至更多——一部三部曲。
