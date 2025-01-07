# DBSCAN，5分钟讲解

> 原文：[https://towardsdatascience.com/dbscan-explained-in-5-minutes-133f6a9766e4?source=collection_archive---------4-----------------------#2024-08-23](https://towardsdatascience.com/dbscan-explained-in-5-minutes-133f6a9766e4?source=collection_archive---------4-----------------------#2024-08-23)

## Python中最快的实现🐍

[](https://medium.com/@alexroz?source=post_page---byline--133f6a9766e4--------------------------------)[![Aleksei Rozanov](../Images/748b69bfaccf39c9aa568a9e6f41eec3.png)](https://medium.com/@alexroz?source=post_page---byline--133f6a9766e4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--133f6a9766e4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--133f6a9766e4--------------------------------) [Aleksei Rozanov](https://medium.com/@alexroz?source=post_page---byline--133f6a9766e4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--133f6a9766e4--------------------------------) ·阅读时间5分钟·2024年8月23日

--

![](../Images/e034fb66930bd44b034406ac41f56803.png)

图片由[作者](https://medium.com/@alexroz)提供。

什么是**DBSCAN** [1]？如何用Python构建它？有很多文章介绍了这个话题，但我认为这个算法本身如此简单直观，以至于可以在仅仅*5分钟*内解释清楚它的基本思想，所以让我们试着这样做。

> DBSCAN = 基于密度的空间聚类算法，带噪声

## 这是什么意思？

1.  该算法基于**对象之间的空间距离**在数据中搜索聚类。

1.  该算法可以**识别异常值**（噪声）。

## 为什么你根本需要DBSCAN呢???

+   **提取新特征。** 如果你处理的数据集很大，找出数据中明显的聚类并分别处理每个聚类（为不同的聚类训练不同的模型）可能会很有帮助。

+   **压缩数据。** 我们经常需要处理数百万行数据，这在计算上是昂贵且耗时的。通过聚类数据，然后从每个聚类中仅保留X%的数据，*可能会拯救你宝贵的数据科学灵魂*。因此，你将**保持数据集内的平衡**，但**减少其大小**。

+   **新颖性检测。** 前面提到过，DBSCAN可以检测噪声，但噪声可能是数据集中的一个之前未知的特征，你可以保留它并在建模中使用。

然后你可能会说：但是有一个超可靠且有效的**k均值**算法。

[](/semantic-segmentation-of-remote-sensing-imagery-using-k-means-e4c165d9218e?source=post_page-----133f6a9766e4--------------------------------) [## 使用k均值进行遥感影像的语义分割

### 从零开始用Python实现🐍

towardsdatascience.com](/semantic-segmentation-of-remote-sensing-imagery-using-k-means-e4c165d9218e?source=post_page-----133f6a9766e4--------------------------------)

是的，但 DBSCAN 最棒的地方是它克服了 k-means 的缺点，而且你不需要指定聚类的数量。**DBSCAN 为你检测聚类！**

DBSCAN 有两个由用户定义的组件：邻域或半径（**𝜀**）和邻居数（**N**）。

对于一个由若干对象组成的数据集，该算法基于以下思想：

1.  **核心对象**。如果一个对象在距离**𝜀**范围内至少有**N**个其他对象，那么它被称为核心对象。

1.  一个位于核心点**𝜀**邻域内的非核心对象称为**边界对象**。

1.  一个核心对象与所有位于**𝜀**邻域内的核心和边界对象形成一个**聚类**。

1.  如果一个对象既不是核心对象也不是边界对象，它被称为**噪声（离群点）**。它不属于任何聚类。

为了实现 DBSCAN，必须创建一个距离函数。在本文中，我们将使用欧几里得距离：

![](../Images/2fd5387f00392750e94306d81375c782.png)

图片由 [作者](https://medium.com/@alexroz) 提供。

我们算法的伪代码如下：

![](../Images/511de6d35f92eb58fad5b01d3ea2ee7e.png)

图片由 [2] 提供。

> **和往常一样，本文的代码可以在我的** [**GitHub**](https://github.com/alexxxroz/Medium/blob/main/DBSCAN.ipynb)** 上找到。**

让我们从距离函数开始：

```py
def distances(object, data):
  euclidean = []
  for row in data: #iterating through all the objects in the dataset
    d = 0
    for i in range(data.shape[1]): #calculating sum of squared residuals for all the coords
      d+=(row[i]-object[i])**2
    euclidean.append(d**0.5) #taking a sqaure root
  return np.array(euclidean)
```

现在让我们构建算法的主体：

```py
def DBSCAN(data, epsilon=0.5, N=3):
  visited, noise = [], [] #lists to collect visited points and outliers
  clusters = [] #list to collect clusters
  for i in range(data.shape[0]): #iterating through all the points
    if i not in visited: #getting in if the point's not visited
      visited.append(i)
      d = distances(data[i], data) #getting distances to all the other points
      neighbors = list(np.where((d<=epsilon)&(d!=0))[0]) #getting the list of neighbors in the epsilon vicinity and removing distance = 0 (it's the point itself)
      if len(neighbors)<N: #if the number of object is less than N, it's an outlier
        noise.append(i)
      else:
        cluster = [i] #otherwise it forms a new cluster
        for neighbor in neighbors: #iterating trough all the neighbors of the point i
          if neighbor not in visited: #if neighbor isn't visited
            visited.append(neighbor)
            d = distances(data[neighbor], data) #get the distances to other objects from the neighbor
            neighbors_idx = list(np.where((d<=epsilon)&(d!=0))[0]) #getting neighbors of the neighbor
            if len(neighbors_idx)>=N: #if the neighbor has N or more neighbors, than it's a core point
              neighbors += neighbors_idx #add neighbors of the neighbor to the neighbors of the ith object
          if not any(neighbor in cluster for cluster in clusters):
            cluster.append(neighbor) #if neighbor is not in clusters, add it there
        clusters.append(cluster) #put the cluster into clusters list

  return clusters, noise
```

完成！

让我们检查一下我们实现的正确性，并与**sklearn**进行比较。

让我们生成一些合成数据：

```py
X1 = [[x,y] for x, y in zip(np.random.normal(6,1, 2000), np.random.normal(0,0.5, 2000))]
X2 = [[x,y] for x, y in zip(np.random.normal(10,2, 2000), np.random.normal(6,1, 2000))]
X3 = [[x,y] for x, y in zip(np.random.normal(-2,1, 2000), np.random.normal(4,2.5, 2000))]

fig, ax = plt.subplots()
ax.scatter([x[0] for x in X1], [y[1] for y in X1], s=40, c='#00b8ff', edgecolors='#133e7c', linewidth=0.5, alpha=0.8)
ax.scatter([x[0] for x in X2], [y[1] for y in X2], s=40, c='#00ff9f', edgecolors='#0abdc6', linewidth=0.5, alpha=0.8)
ax.scatter([x[0] for x in X3], [y[1] for y in X3], s=40, c='#d600ff', edgecolors='#ea00d9', linewidth=0.5, alpha=0.8)
ax.spines[['right', 'top', 'bottom', 'left']].set_visible(False)
ax.set_xticks([])
ax.set_yticks([])
ax.set_facecolor('black')
ax.patch.set_alpha(0.7)
```

![](../Images/f27953128084bd76852f0b2e754604f9.png)

图片由 [作者](https://medium.com/@alexroz) 提供。

让我们应用我们的实现并可视化结果：

![](../Images/f10fec3abef911cd001c387b1dee55a2.png)

图片由 [作者](https://medium.com/@alexroz) 提供。

对于 sklearn 的实现，我们得到了相同的聚类：

![](../Images/727430787a45f9542b63737f659ca610.png)

图片由 [作者](https://medium.com/@alexroz) 提供。

就这样，它们是相同的。5分钟就搞定了！当你自己尝试 DBSCAN 时，别忘了调整 epsilon 和邻居数，因为它们会显著影响最终结果。

===========================================

**参考文献：**

[1] Ester, M., Kriegel, H. P., Sander, J., & Xu, X. (1996, August). Density-based spatial clustering of applications with noise. In *Int. Conf. knowledge discovery and data mining* (Vol. 240, №6).

[2] Yang, Yang, et al. “An efficient DBSCAN optimized by arithmetic optimization algorithm with opposition-based learning.” *The journal of supercomputing* 78.18 (2022): 19566–19604.

===========================================

*我在 Medium 上的所有出版物都是免费的开放访问，因此如果你在这里关注我，我将不胜感激！*

P.s. 我对（地理）数据科学、机器学习/人工智能以及气候变化充满极大热情。如果你想在某个项目上合作，请通过[LinkedIn](https://www.linkedin.com/in/alexxxroz/)联系我。

🛰️关注以获取更多信息🛰️
