# 聚类算法指南

> 原文：[https://towardsdatascience.com/a-guide-to-clustering-algorithms-e28af85da0b7?source=collection_archive---------4-----------------------#2024-09-06](https://towardsdatascience.com/a-guide-to-clustering-algorithms-e28af85da0b7?source=collection_archive---------4-----------------------#2024-09-06)

[](https://medium.com/@adavis08?source=post_page---byline--e28af85da0b7--------------------------------)[![Alex Davis](../Images/f773cce9438a68856cb8ba486ac8b051.png)](https://medium.com/@adavis08?source=post_page---byline--e28af85da0b7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e28af85da0b7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e28af85da0b7--------------------------------) [Alex Davis](https://medium.com/@adavis08?source=post_page---byline--e28af85da0b7--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e28af85da0b7--------------------------------) ·阅读时间：6分钟·2024年9月6日

--

*聚类是任何数据科学家必备的技能，因为它在解决实际问题时的实用性和灵活性。本文概述了聚类及不同类型的聚类算法。*

# 什么是聚类？

聚类是一种流行的无监督学习技术，旨在根据对象或观测值之间的相似性将它们分组。聚类有许多有用的应用，如市场细分、推荐系统、探索性分析等。

![](../Images/3aa1508082c9b47231972b9af15633a8.png)

图片由作者提供

虽然聚类是数据科学领域广为人知且被广泛使用的技术，但一些人可能不了解不同类型的聚类算法。尽管聚类算法种类不多，但理解这些算法及其工作原理对于根据你的使用场景获得最佳结果至关重要。

# 基于中心点的聚类

基于中心点的聚类是大多数人提到聚类时的典型方式。它是通过使用预定义数量的中心点（中心）来根据数据点与每个中心点的距离将数据分组的“传统”聚类方法。最终，中心点成为其分配数据点的均值。尽管基于中心点的聚类非常强大，但它对离群值的鲁棒性较差，因为离群值仍需要被分配到某个聚类中。

## K-Means

K-Means 是最广泛使用的聚类算法，它可能是你作为数据科学家学习的第一个算法。如上所述，目标是最小化数据点与聚类中心之间的距离之和，从而确定每个数据点应该属于的正确组别。其工作原理如下：

1.  一个预定义数量的中心点被随机放置到无标签数据的向量空间中（初始化）。

1.  每个数据点会与每个质心进行自我比较（通常使用欧几里得距离），并将自己分配给最近的一个质心。

1.  质心会移动到它们所分配的数据点的均值位置。

1.  步骤2-3会反复执行，直到生成“最佳”聚类。

![](../Images/228afd72b5c9d40421a55f06880992fe.png)

图片由作者提供

```py
from sklearn.cluster import KMeans
import numpy as np

#sample data
X = np.array([[1, 2], [1, 4], [1, 0],
              [10, 2], [10, 4], [10, 0]])

#create k-means model
kmeans = KMeans(n_clusters = 2, random_state = 0, n_init = "auto").fit(X)

#print the results, use to predict, and print centers
kmeans.labels_
kmeans.predict([[0, 0], [12, 3]])
kmeans.cluster_centers_
```

## K-Means ++

K-Means ++是K-Means初始化步骤的改进版。由于质心是随机选择的，因此可能会有多个质心初始化到同一个聚类中，导致结果不理想。

然而，K-Means ++通过随机分配第一个质心来解决这个问题，最终找到最大的聚类。然后，其他质心会被放置在与初始聚类一定距离的位置。K-Means ++的目标是使质心之间的距离尽可能远。这种方式可以得到高质量的聚类，且这些聚类是独立且明确定义的。

```py
from sklearn.cluster import KMeans
import numpy as np

#sample data
X = np.array([[1, 2], [1, 4], [1, 0],
              [10, 2], [10, 4], [10, 0]])

#create k-means model
kmeans = KMeans(n_clusters = 2, random_state = 0, n_init = "k-means++").fit(X)

#print the results, use to predict, and print centers
kmeans.labels_
kmeans.predict([[0, 0], [12, 3]])
kmeans.cluster_centers_
```

# 基于密度的聚类

基于密度的算法也是一种流行的聚类形式。然而，它们并不是通过从随机位置的质心进行度量，而是通过识别数据中的高密度区域来创建聚类。基于密度的算法不需要预先定义聚类的数量，因此优化起来工作量较小。

虽然基于质心的算法在处理球形聚类时表现更好，但基于密度的算法可以处理任意形状的聚类，并且更具灵活性。它们还不会将离群点包括在聚类中，因此具有较强的鲁棒性。然而，这些算法在面对具有不同密度和高维度的数据时可能会遇到困难。

![](../Images/900c97181437cb771ab90dc1c2a7240b.png)

图片由作者提供

## DBSCAN

DBSCAN是最流行的基于密度的算法。DBSCAN的工作原理如下：

1.  DBSCAN随机选择一个数据点，并检查它是否在指定的半径内有足够的邻居。

1.  如果某个点有足够的邻居，它将被标记为聚类的一部分。

1.  DBSCAN会递归检查邻居是否也在半径内有足够的邻居，直到聚类中的所有点都被访问过。

1.  重复步骤1-3，直到剩余的数据点在半径内没有足够的邻居。

1.  剩余的数据点被标记为离群点。

```py
from sklearn.cluster import DBSCAN
import numpy as np

#sample data
X = np.array([[1, 2], [2, 2], [2, 3],
              [8, 7], [8, 8], [25, 80]])

#create model
clustering = DBSCAN(eps=3, min_samples=2).fit(X)

#print results
clustering.labels_
```

# 层次聚类

接下来，我们来讲解层次聚类。这种方法首先通过从原始数据计算一个距离矩阵开始。这个距离矩阵最好且通常通过树状图（见下文）进行可视化。数据点通过找到最近的邻居，一一连接，最终形成一个巨大的聚类。因此，可以通过设置一个截断点，停止所有数据点的连接，从而识别出不同的聚类。

![](../Images/e180fc95c23f18fec38023cc80147e63.png)

图片由作者提供

通过使用这种方法，数据科学家可以通过定义离群点并将其排除在其他聚类之外，构建一个鲁棒的模型。这种方法在处理层次结构数据（如分类法）时效果极佳。聚类的数量取决于深度参数，范围可以是1到n。

```py
from scipy.cluster.hierarchy import dendrogram, linkage
from sklearn.cluster import AgglomerativeClustering
from scipy.cluster.hierarchy import fcluster

#create distance matrix
linkage_data = linkage(data, method = 'ward', metric = 'euclidean', optimal_ordering = True)

#view dendrogram
dendrogram(linkage_data)
plt.title('Hierarchical Clustering Dendrogram') 
plt.xlabel('Data point') 
plt.ylabel('Distance') 
plt.show()

#assign depth and clusters
clusters = fcluster(linkage_data, 2.5, criterion = 'inconsistent', depth = 5)
```

# 基于分布的聚类

最后，基于分布的聚类考虑了除距离和密度之外的度量标准，即概率。基于分布的聚类假设数据由概率分布组成，例如正态分布。该算法创建了“带状”区域，表示置信区间。数据点距离聚类中心越远，我们就越不确定该数据点是否属于该聚类。

![](../Images/b9c66c47eb6422b7014a43d6bd01a57b.png)

作者提供的图片

基于分布的聚类由于其假设条件，实施起来非常困难。通常不推荐使用，除非经过严格分析确认其结果。例如，可以使用它来识别市场数据集中的客户群体，并确认这些群体符合某种分布。这个方法也非常适合探索性分析，不仅能了解聚类中心的组成，还能分析聚类的边缘和离群点。

# 结论

聚类是一种无监督机器学习技术，在许多领域的应用日益增多。它可以用于支持数据分析、细分项目、推荐系统等多个场景。上述内容中，我们探讨了它们的工作原理、优缺点、代码示例，甚至一些使用案例。我认为，数据科学家必须具备使用聚类算法的经验，因为它们具有广泛的实用性和灵活性。

*希望你喜欢我的文章！请随时发表评论、提问或请求其他话题。*
