# 使用 NetworkX 浏览网络：Python 中图形的简短指南

> 原文：[https://towardsdatascience.com/navigating-networks-with-networkx-a-short-guide-to-graphs-in-python-c16cbafe8063?source=collection_archive---------1-----------------------#2024-11-18](https://towardsdatascience.com/navigating-networks-with-networkx-a-short-guide-to-graphs-in-python-c16cbafe8063?source=collection_archive---------1-----------------------#2024-11-18)

![](../Images/e8bb442e9e37d2c3417e291b25f19312.png)

由 [Alina Grubnyak](https://unsplash.com/@alinnnaaaa?utm_source=medium&utm_medium=referral) 提供的照片，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 探索 NetworkX，构建、分析和可视化 Python 中的图形。发现关联数据中的洞察。

[](https://medium.com/@technologger?source=post_page---byline--c16cbafe8063--------------------------------)[![Diego Penilla](../Images/aa69a740a3b915e5548219fe762dd2ae.png)](https://medium.com/@technologger?source=post_page---byline--c16cbafe8063--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c16cbafe8063--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c16cbafe8063--------------------------------) [Diego Penilla](https://medium.com/@technologger?source=post_page---byline--c16cbafe8063--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c16cbafe8063--------------------------------) ·14 分钟阅读·2024年11月18日

--

在一个充满连接的世界中——从社交媒体上的朋友关系到复杂的交通网络——理解关系和模式是理解周围系统的关键。试想一下，如何将一个社交网络可视化，其中每个人都是一个点（“节点”），通过线（或“边”）与朋友相连。或者想象映射一个城市的地铁系统，其中每个车站是一个节点，每条路线是连接它们的边。

这就是 [NetworkX](https://networkx.org/) 的亮点，它提供了一种强大的方式来构建、分析和可视化这些错综复杂的关系网络。

NetworkX 允许我们以传统表格方式难以实现的形式来表示数据，而图形格式使得这种表示变得既简便又自然。那些在电子表格中需要许多行和列才能定义的关系，可以通过直观的可视化方式捕捉，从而帮助我们理解和解读复杂数据。

![](../Images/1485ea18d9293697705cd993ec0707fe.png)

由 [Terry Vlisidis](https://unsplash.com/@vlisidis?utm_source=medium&utm_medium=referral) 提供的照片，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

该库允许我们将各种方法和算法应用于这些图形，每次通过新的方法重新构架数据时，都能提供新的洞察。

# `NetworkX`

让我们首先分解一下什么是图。在网络分析中，图由**节点**（或顶点）和**边**（或链接）组成。

+   可以把节点看作是主要的实体，比如人或网页，边则是它们之间的连接——就像社交网络中的友谊或网站之间的超链接。

+   一些边甚至带有权重，表示两个节点之间连接的强度、距离或成本。这个附加的信息层帮助我们分析两个节点不仅是否连接，还能知道它们的连接强度或紧密度。

*这些图形可以用来建模各种系统，从社交网络到分子和交通网格。*

让我们先看看如何使用`networkx`创建图。如果你还没有安装，可以先运行：

```py
$ pip install networkx
```

## 创建图

要构建一个网络，我们将：

1.  初始化网络：通过创建一个图`G = nx.Graph()`来初始化网络。

1.  添加带属性的节点：使用 G.add_node() 来添加节点，每个节点可以存储自定义属性，如标签或年龄。

1.  添加边：使用 G`.add_edge()` 来连接节点，每条边可以包含一个权重属性，表示连接的强度或成本。

1.  可视化图形：使用 Matplotlib 函数，如`nx.draw()`和`nx.draw_networkx_edge_labels()`来显示图形，展示节点标签和边权重，方便解释。

这是实现这一目标的代码：

```py
import networkx as nx
import matplotlib.pyplot as plt

# Create a simple graph
G = nx.Graph()

# Add nodes with attributes (e.g., 'label' and 'age')
G.add_node(1, label="A", age=25)
G.add_node(2, label="B", age=30)
G.add_node(3, label="C", age=22)
G.add_node(4, label="D", age=28)

# Add weighted edges (node1, node2, weight)
G.add_edge(1, 2, weight=4)
G.add_edge(1, 3, weight=3)
G.add_edge(2, 4, weight=5)

# Retrieve and print node attributes
node_attributes = nx.get_node_attributes(G, 'age')  # Get 'age' attribute for all nodes
print("Node Attributes (Age):", node_attributes)

# Retrieve and print edge attributes
edge_weights = nx.get_edge_attributes(G, 'weight')  # Get 'weight' attribute for all edges
print("Edge Attributes (Weight):", edge_weights)

# Draw the graph with node and edge attributes
pos = nx.spring_layout(G)  # Layout for node positions
node_labels = nx.get_node_attributes(G, 'label')  # Get node labels for visualization
edge_labels = nx.get_edge_attributes(G, 'weight')  # Get edge weights for visualization

plt.figure(figsize=(6, 6))
nx.draw(G, pos, with_labels=True, node_color='skyblue', font_size=15, font_weight='bold', node_size=500)

# Draw the edge weights and node labels
nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels)

plt.title("NetworkX Graph with Node and Edge Attributes")
plt.show()
```

![](../Images/31f6d8dffd659af64706b7862af9d16f.png)

图 1：一个带权重的图，节点为 1 到 4。图片来源：作者。

在这个例子中，我们初始化图形，然后创建：

+   **4 个节点**（1、2、3、4）通过调用`G.add_node(node, label, attr)`

+   **3 条带权重的边**连接这些节点：(1, 2)、(1, 3) 和 (2, 4)，通过调用`G.add_edge(node1, node2, attr)`

在 NetworkX 中，节点和边都可以保存额外的属性，使得图形更加丰富和信息量更大。

+   节点属性（通过`nx.get_node_attributes(G, 'attribute')`访问）允许每个节点存储数据，比如社交网络中某人的职业。

+   边属性（通过`nx.get_edge_attributes(G, 'attribute')`访问）存储每个连接的信息，比如交通网络中的距离或旅行时间。这些属性增加了上下文和深度，使得网络的分析更加详细。

然后，我们使用 NetworkX 的弹簧布局`pos = nx.spring_layout(G)`来为节点定位，以便自然地将它们排布在图中。最后，`nx.draw()` 和 `nx.draw_networkx_edge_labels()` 显示带有节点标签和边权重的图，清晰地呈现网络的结构和连接。

虽然这是一个相对简单的网络，但它展示了处理网络的基本方法：为了操作图形，我们需要处理节点及其连接，以及它们可能具有的任何属性。

## 空手道俱乐部网络

网络科学中最著名的例子之一是[*扎卡里空手道俱乐部*](https://en.wikipedia.org/wiki/Zachary's_karate_club)，它常常用于说明社交网络分析和社区检测。该数据集是公有领域的，并且默认包含在networkx中。您可以按以下方式访问：

```py
# Load the  Karate Club
G = nx.karate_club_graph()

# Draw the graph
plt.figure(figsize=(8, 8))
pos = nx.spring_layout(G)  # Layout for nodes -> treats nodes as repelling objects
nx.draw(G, pos, with_labels=True, node_color='skyblue', font_size=12, font_weight='bold', node_size=500)
plt.title("Zachary's Karate Club Network")
plt.show()
```

![](../Images/37559d2c05050146006e7e491f775bf6.png)

图2：扎卡里空手道俱乐部网络。图片来自作者。

该网络代表了空手道俱乐部34名成员之间的友谊，它因两个派别之间的分裂而闻名，每个派别都有一个中心人物——`Mr. Hi`和`Officer`。

让我们来看看节点数据中包含的属性：

```py
# looping over nodes
for node in G.nodes():
    print(f"Node: {node}, Node Attributes: {G.nodes[node]}")
```

```py
Node: 0, Node Attributes: {'club': 'Mr. Hi'}
Node: 1, Node Attributes: {'club': 'Mr. Hi'}
Node: 2, Node Attributes: {'club': 'Mr. Hi'}
Node: 3, Node Attributes: {'club': 'Mr. Hi'}
Node: 4, Node Attributes: {'club': 'Mr. Hi'}
Node: 5, Node Attributes: {'club': 'Mr. Hi'}
Node: 6, Node Attributes: {'club': 'Mr. Hi'}
Node: 7, Node Attributes: {'club': 'Mr. Hi'}
Node: 8, Node Attributes: {'club': 'Mr. Hi'}
Node: 9, Node Attributes: {'club': 'Officer'}
Node: 10, Node Attributes: {'club': 'Mr. Hi'}
Node: 11, Node Attributes: {'club': 'Mr. Hi'}
Node: 12, Node Attributes: {'club': 'Mr. Hi'}
Node: 13, Node Attributes: {'club': 'Mr. Hi'}
Node: 14, Node Attributes: {'club': 'Officer'}
Node: 15, Node Attributes: {'club': 'Officer'}
Node: 16, Node Attributes: {'club': 'Mr. Hi'}
Node: 17, Node Attributes: {'club': 'Mr. Hi'}
Node: 18, Node Attributes: {'club': 'Officer'}
Node: 19, Node Attributes: {'club': 'Mr. Hi'}
Node: 20, Node Attributes: {'club': 'Officer'}
Node: 21, Node Attributes: {'club': 'Mr. Hi'}
Node: 22, Node Attributes: {'club': 'Officer'}
Node: 23, Node Attributes: {'club': 'Officer'}
Node: 24, Node Attributes: {'club': 'Officer'}
Node: 25, Node Attributes: {'club': 'Officer'}
Node: 26, Node Attributes: {'club': 'Officer'}
Node: 27, Node Attributes: {'club': 'Officer'}
Node: 28, Node Attributes: {'club': 'Officer'}
Node: 29, Node Attributes: {'club': 'Officer'}
Node: 30, Node Attributes: {'club': 'Officer'}
Node: 31, Node Attributes: {'club': 'Officer'}
Node: 32, Node Attributes: {'club': 'Officer'}
Node: 33, Node Attributes: {'club': 'Officer'}
```

节点属性`club`指的是每个节点所属的社区，例如“`Officer`”或“`Mr. Hi`”。我们将使用这些信息为图中的节点着色。

为此，我们将蓝色分配给标签为“`Mr. Hi`”的节点，将红色分配给标签为“`Officer`”的节点，并将它们存储在一个`color_map`列表中，然后我们可以使用`nx.draw`来可视化网络。

```py
# Load the Karate Club 
G: nx.Graph = nx.karate_club_graph()

# Get the node labels
labels = nx.get_node_attributes(G, 'club')

# Map community labels to colors
color_map = []
for node in G.nodes():
    if labels[node] == 'Mr. Hi':
        # Assign blue color for 'Mr. Hi'
        color_map.append('blue')  
    else:
        # Assign red color for 'Officer'
        color_map.append('red')  

# Visualize the graph
plt.figure(figsize=(8, 8))
pos = nx.spring_layout(G)  

nx.draw(G, pos, with_labels=True, node_color=color_map, font_size=12, font_weight='bold', node_size=500, cmap=plt.cm.rainbow)
plt.title("Zachary's Karate Club Network with Ground Truth Communities")
plt.show()
```

![](../Images/b3706bc713c845290a772d81cf771a9e.png)

图3：空手道俱乐部网络中的“`Mr. Hi`”与“`Officer`”社区。图片来自作者。

图例说明，俱乐部的教练“`Mr. Hi`”与俱乐部的管理员“`Officer`”之间发生了冲突。这场分裂最终导致俱乐部分成了两个不同的团体，每个团体的中心人物分别是这两位领导者。

通过将这些关系表示为网络，我们可以直观地捕捉到这种分裂，并揭示数据中的模式和聚类——这些洞察在传统的表格格式中可能难以看到。

## 中心性

为了理解网络的结构和动态，识别最具影响力或战略性节点是至关重要的。这就是**中心性**度量的作用，它是网络科学中的一个关键概念。

它通过节点连接类型来衡量节点的位置，根据某些标准识别关键节点。常见的度量包括：

+   [*度数中心性*](https://www.sciencedirect.com/topics/computer-science/degree-centrality)（仅指每个节点拥有的连接数）

+   [*紧密中心性*](https://www.sciencedirect.com/topics/computer-science/closeness-centrality)（一个节点能够多快地访问网络中的所有其他节点）。

+   以及[*介数中心性*](https://www.sciencedirect.com/topics/computer-science/betweenness-centrality)（一个节点在其他节点之间的最短路径中出现的频率）

这些度量有助于揭示网络中的关键节点或瓶颈，从而提供对网络结构和动态的洞察。

```py
import networkx as nx
import matplotlib.pyplot as plt

# Load the Karate Club 
G = nx.karate_club_graph()

# Compute centrality measures
degree_centrality = nx.degree_centrality(G)
betweenness_centrality = nx.betweenness_centrality(G)
closeness_centrality = nx.closeness_centrality(G)

# top 5 nodes by centrality for each measure
top_degree_nodes = sorted(degree_centrality, key=degree_centrality.get, reverse=True)[:5]
top_betweenness_nodes = sorted(betweenness_centrality, key=betweenness_centrality.get, reverse=True)[:5]
top_closeness_nodes = sorted(closeness_centrality, key=closeness_centrality.get, reverse=True)[:5]

# top 5 nodes for each centrality measure
print("Top 5 nodes by Degree Centrality:", top_degree_nodes)
print("Top 5 nodes by Betweenness Centrality:", top_betweenness_nodes)
print("Top 5 nodes by Closeness Centrality:", top_closeness_nodes)

# top 5 nodes for Degree Centrality
plt.figure(figsize=(8, 8))
pos = nx.spring_layout(G)  # Positioning of nodes
node_color = ['red' if node in top_degree_nodes else 'skyblue' for node in G.nodes()]

# draw top 5 nodes by degree centrality
nx.draw(G, pos, with_labels=True, node_color=node_color, font_size=15, font_weight='bold', node_size=500)
plt.title("Karate Club Network with Top 5 Degree Central Nodes")
plt.show()
```

```py
Top 5 nodes by Degree Centrality: [33, 0, 32, 2, 1]
Top 5 nodes by Betweenness Centrality: [0, 33, 32, 2, 31]
Top 5 nodes by Closeness Centrality: [0, 2, 33, 31, 8]
```

![](../Images/c2f6342dc1490a55efdca0cfa0684d04.png)

图4：空手道俱乐部网络中中心性最高的节点。图片来自作者。

对于节点`0`和`33`，我们看到这些节点在网络中最为中心，具有较高的度数、介数和紧密中心性。

它们在网络中的核心作用表明它们是高度连接的枢纽，通常充当其他成员之间的桥梁，并能够快速触及网络中的其他节点。这一定位使它们成为关键角色，在网络的流动性和结构中具有重要意义。

# [使用Girvan-Newman算法进行社区检测](https://www.google.com/url?q=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FGirvan%25E2%2580%2593Newman_algorithm)

*一个社区C是一个节点集合（例如，社交网络中的个体、通过超链接连接的网页等），这些节点之间的连接比与网络中其他部分的连接更强。*

在考虑中心性的可视化表示时，让我们将Girvan-Newman算法应用于此图。

+   该算法通过逐步移除具有最高介数中心性的边缘，生成一系列社区划分。

+   当算法第一次运行时，它识别出最重要的社区划分。

```py
from networkx.algorithms.community import girvan_newman

# Load the Karate Club graph
G = nx.karate_club_graph()

# Apply Girvan-Newman community detection
comp = girvan_newman(G)
first_level_communities = next(comp)

# Visualize the first level of communities
pos = nx.spring_layout(G)
plt.figure(figsize=(8, 8))

# Color nodes by their community
node_colors = ['skyblue' if node in first_level_communities[0] else 'orange' for node in G.nodes()]
nx.draw(G, pos, with_labels=True, node_color=node_colors, font_size=12, node_size=500)

plt.title("Karate Club Network with Girvan-Newman Communities")
plt.show()

print("Detected Communities:", first_level_communities)
```

![](../Images/bdb890e21aaf06f875e267e1260c4b0c.png)

图5：Girvan-Newman算法在空手道俱乐部网络中的第一次划分。图像来自作者。

+   由于`girvan_newman(G)`返回一个迭代器`comp`，调用`next(comp)`可以让你检索到第一个划分，即将网络分成两个社区的第一次划分。

让我们将检测到的社区与实际的节点标签`club`进行比较。

```py
 print("Detected Communities:", first_level_communities)
# Print the actual communities (ground truth)
print("\nActual Communities (Ground Truth):")
mr_hi_nodes = [node for node, label in labels.items() if label == 'Mr. Hi']
officer_nodes = [node for node, label in labels.items() if label == 'Officer']

print(f"Mr. Hi's Community: {mr_hi_nodes}")
print(f"Officer's Community: {officer_nodes}")
```

```py
Detected Communities: (
{0, 1, 3, 4, 5, 6, 7, 10, 11, 12, 13, 16, 17, 19, 21}, 
{2, 8, 9, 14, 15, 18, 20, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33}
)

Actual Communities (Ground Truth):
Mr. Hi's Community: [0, 1, 2, 3, 4, 5, 6, 7, 8, 10, 11, 12, 13, 16, 17, 19, 21]
Officer's Community: [9, 14, 15, 18, 20, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33]
```

Girvan-Newman算法检测到的社区*与实际的Hi先生和警官社区相似，但并不完全匹配*。这是因为Girvan-Newman算法仅基于边缘的介数中心性来划分网络，而不依赖任何预定义的社区标签。

这种方法在没有标签的非结构化数据集中尤其有用，因为它揭示了基于网络结构特性的有意义的分组。这突出了社区检测中的一个关键问题：什么构成一个社区并没有严格的定义。

结果是，没有单一的“正确”方式来划分一个网络。不同的方法，由不同的度量驱动，可以得出不同的结果，每种结果根据具体的上下文提供有价值的见解。

![](../Images/22467af082bfdb6ed871e214c41385c4.png)

[来源](https://images.unsplash.com/photo-1639322537228-f710d846310a?q=80&w=1932&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D)

## 团体

在网络中，一个有用的概念是团体。在网络科学中，团体是指图中一个节点子集，其中每个节点都与该子集中的其他每个节点相连接。这意味着团体中的所有成员之间都有直接的关系，形成了一个紧密连接的群体。团体在研究复杂网络的结构时特别有用，因为它们通常代表了更大系统中高度连接或凝聚的群体。

例如在：

+   在社交网络中：团体可以代表彼此认识的群体，如紧密联系的朋友圈或职业同事。

+   在协作网络中：在一个协作网络中（例如，研究合作），团体可以揭示那些在相同主题或项目上共同合作的研究团队。

+   在生物网络中：在生物网络中，团体可以表示在生物过程中紧密相互作用的功能性蛋白质或基因群体。

让我们找到空手道网络中的最大团体。我们将找到具有所有互联链接的最大人群。

```py
import networkx as nx
import matplotlib.pyplot as plt

# Load the Karate Club graph
G = nx.karate_club_graph()

# Find all cliques in the Karate Club network
cliques = list(nx.find_cliques(G))

# Find the largest clique (the one with the most nodes)
largest_clique = max(cliques, key=len)

# Print the largest clique
print("Largest Clique:", largest_clique)

# Visualize the graph with the largest clique highlighted
plt.figure(figsize=(8, 8))
pos = nx.spring_layout(G)  # Layout for node positions
nx.draw(G, pos, with_labels=True, node_color='skyblue', font_size=12, node_size=500)

# Highlight the nodes in the largest clique
nx.draw_networkx_nodes(G, pos, nodelist=largest_clique, node_color='orange', node_size=500)

plt.title("Karate Club Network with Largest Clique Highlighted")
plt.show()
```

![](../Images/6ed6565bd4b26fac324ca4c6d6de509f.png)

图6：空手道俱乐部网络中的最大团体，节点0、1、2、3和13相互连接。图片由作者提供。

尽管在网络科学中定义“社区”存在挑战，但团体提供了一个具体且明确的概念，用于识别那些完全互联的群体，从而为结构化和非结构化网络提供有意义的见解。

## 最短路径

网络科学中的另一个有趣概念是最短路径。在图中的两个节点之间的最短路径指的是连接这两个节点的边的序列，同时最小化总距离或成本，这可以根据应用的不同有多种解释。这个概念在路由算法、网络设计、运输规划甚至社交网络分析等领域发挥着至关重要的作用。

NetworkX 提供了几种算法来计算最短路径，例如用于加权图的 Dijkstra 算法和用于无权图的广度优先搜索（BFS）。

![](../Images/51cc5e26a2bcbc16ddfb596244abe583.png)

图片来源：[Ed 259](https://unsplash.com/@ed259?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

让我们来看一个例子，我们将创建一个合成数据集，其中节点代表车站，边表示车站之间的连接。

+   我们还将添加加权边时间，表示从一个站点到下一个站点所需的时间。

```py
import pandas as pd
import networkx as nx
import matplotlib.pyplot as plt

# Simulate loading a CSV file (real example would load an actual CSV file)
# Define a more extensive set of stations and travel times between them
data = {
    'station_id': ['A', 'A', 'B', 'B', 'C', 'C', 'D', 'D', 'E', 'E', 'F', 'F', 'G', 'G', 'H'],
    'connected_station': ['B', 'C', 'A', 'C', 'A', 'D', 'C', 'E', 'B', 'F', 'D', 'G', 'E', 'H', 'F'],
    'time': [10, 20, 10, 15, 20, 10, 5, 15, 10, 25, 10, 5, 15, 10, 30]  # Travel times in minutes
}

# Create a DataFrame
df = pd.DataFrame(data)

# Create a graph from the DataFrame
G = nx.Graph()

# Add edges to the graph (station connections with weights as travel times)
for index, row in df.iterrows():
    G.add_edge(row['station_id'], row['connected_station'], weight=row['time'])

# Draw the graph
plt.figure(figsize=(8, 8))
pos = nx.spring_layout(G)  # Layout for node positions
nx.draw(G, pos, with_labels=True, node_size=500, node_color='skyblue', font_size=12, font_weight='bold')

# Draw edge weights (travel times)
edge_labels = nx.get_edge_attributes(G, 'weight')
nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels)

plt.title("Expanded Transportation Network with Travel Times")
plt.show()
```

![](../Images/8f261cc230e141c399330c326b939259.png)

图7：示例运输网络，其中节点代表车站，边代表时间或长度。图片由作者提供。

在这个例子中，我们使用 Dijkstra 算法来计算从车站A到车站H的最短路径，其中边的权重代表旅行时间。最短路径及其总旅行时间会被打印出来，路径在图中以红色突出显示，边的权重显示以指示车站之间的旅行时间。

```py
# Compute the shortest path using Dijkstra's algorithm (considering the travel time as weight)
source = 'A'
target = 'H'

shortest_path = nx.shortest_path(G, source=source, target=target, weight='weight')
path_length = nx.shortest_path_length(G, source=source, target=target, weight='weight')

# Print the shortest path and its length
print(f"Shortest path from {source} to {target}: {shortest_path}")
print(f"Total travel time from {source} to {target}: {path_length} minutes")

# Visualize the shortest path on the graph
plt.figure(figsize=(8, 8))
nx.draw(G, pos, with_labels=True, node_size=500, node_color='skyblue', font_size=12, font_weight='bold')

# Highlight the shortest path in red
edges_in_path = [(shortest_path[i], shortest_path[i + 1]) for i in range(len(shortest_path) - 1)]
nx.draw_networkx_edges(G, pos, edgelist=edges_in_path, edge_color='red', width=2)

# Draw edge weights (travel times)
nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels)

plt.title(f"Shortest Path from {source} to {target} with Travel Time {path_length} minutes")
plt.show()
```

```py
Shortest path from A to H: ['A', 'B', 'E', 'G', 'H']
Total travel time from A to H: 45 minutes
```

![](../Images/8ce1d37688551b9219a4ecc9dc8ad390.png)

图8：给定图中输入节点A和H之间的最短路径—45分钟。图片由作者提供。

该算法计算了最短路径及其总旅行时间，然后将其显示出来。从A到H的最短路径在图中以红色高亮显示，边的权重表示每个连接站点之间的时间，总和为45。

尽管这只是一个简单的计算，但最短路径算法有着广泛的应用。在交通运输中，它们优化路线并减少旅行时间；在数字通信中，它们有效地路由数据。它们在物流中至关重要，用于降低成本，在供应链中确保及时交货，在社交网络中衡量个体之间的亲密度。理解最短路径使得各领域可以基于数据做出决策——从城市规划到网络基础设施——使其成为高效导航复杂系统的关键工具。

## 感谢阅读

我们已经使用NetworkX探索了网络科学中的几个基本概念，如最短路径算法、社区检测以及图论在建模和分析复杂系统中的强大能力。

如果你想继续学习，下面我放了一些链接供你参考 :) 如果你想深入了解社区检测算法，可以查看CDLib库。

1.  [Networkx教程](https://networkx.org/documentation/stable/tutorial.html)

1.  [CDLib,](https://cdlib.readthedocs.io/en/latest/) 一个用于社区检测的库

**注意**：在图上计算高级指标和度量常常可能是模糊的，甚至会导致误导。由于有这么多可用的潜在指标，很容易生成一些没有实际意义或可能会误表示网络真实结构的数字。选择正确的指标需要谨慎考虑，因为并非所有度量都能为每种类型的网络分析提供相关见解。如果你对此有所共鸣，可以点击这里查看更多信息：[统计推断将数据与理论联系在一起，应用于网络科学](https://www.nature.com/articles/s41467-022-34267-9)

## 参考文献

+   *空手道俱乐部网络：* [*Zachary, Wayne W. “An information flow model for conflict and fission in small groups.” Journal of Anthropological Research, 33(4), 452–473, 1977.*](https://web.archive.org/web/20171021192632/http://networkdata.ics.uci.edu:80/data.php?id=105)
