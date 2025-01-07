# 在Python中使用PCA和K-Means进行交通数据分析

> 原文：[https://towardsdatascience.com/pca-k-means-for-traffic-data-in-python-a0ec66ab4789?source=collection_archive---------2-----------------------#2024-05-07](https://towardsdatascience.com/pca-k-means-for-traffic-data-in-python-a0ec66ab4789?source=collection_archive---------2-----------------------#2024-05-07)

## 基于每小时交通数据，减少维度并对台北捷运车站进行聚类

[](https://medium.com/@beth-ouyang?source=post_page---byline--a0ec66ab4789--------------------------------)[![Beth Ou Yang](../Images/d182a3e58759953dcf90039c064390ac.png)](https://medium.com/@beth-ouyang?source=post_page---byline--a0ec66ab4789--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a0ec66ab4789--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a0ec66ab4789--------------------------------) [Beth Ou Yang](https://medium.com/@beth-ouyang?source=post_page---byline--a0ec66ab4789--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a0ec66ab4789--------------------------------) ·阅读时间8分钟·2024年5月7日

--

![](../Images/9e36326467465bce72fda2a126249d5e.png)

台北铁路地图（实际上基于罗马化标准引入）包括高铁、台铁、台北捷运及其他线路。图片来源：[Taiwan J](https://commons.wikimedia.org/wiki/User:Taiwan_Junior)。

主成分分析（PCA）已经在交通数据中用于检测异常，但它也可以用于捕捉交通站点历史数据的模式，类似于它在顾客购买数据上的应用。

## **在本文中，我们将讨论：**

1.  PCA做了什么技巧

1.  应用PCA后我们可以做什么

1.  **玩得开心！**

    看看我们的数据集：

    [台北捷运系统每小时交通数据](https://www.kaggle.com/datasets/bethouyang/taipei-mrt-hourly-traffic-data)

    [完整代码](https://www.kaggle.com/code/bethouyang/pca-k-means-for-traffic-data-in-python)也包含在上述的Kaggle数据集中。

1.  在每小时交通数据上使用PCA

1.  对PCA结果进行聚类

1.  关于台北捷运交通的洞察

1.  主要收获

## 1\. PCA做了什么技巧

简而言之，PCA通过找到特征的线性组合来总结数据，这可以理解为拍摄一个三维物体的几张照片，并且它会在交给你之前自然地按最具代表性的照片到最不具代表性的照片进行排序。

输入是我们的原始数据，PCA会输出2个有用的结果：**Z** 和 **W**。通过将它们相乘，我们可以得到重构数据，即原始数据，但有一些可以容忍的信息丢失（因为我们已经减少了维度）。

我们将在下面的实践中解释这2个输出矩阵与我们的数据的关系。

## 2\. 应用PCA后我们可以做什么

在将PCA应用于我们的数据以降维后，我们可以将其用于其他机器学习任务，如聚类、分类和回归。

在本文稍后的台北捷运案例中，我们将在较低维度的数据上进行聚类，其中几个维度可以解释为一天中不同时间段的乘客比例，例如早晨、午间和傍晚。那些白天具有相似乘客比例的车站会被认为在同一簇中（它们的模式相似！）。

## 3. 看看**我们的交通数据集！**

我们这里使用的数据是[台北捷运系统每小时交通数据](https://www.kaggle.com/datasets/bethouyang/taipei-mrt-hourly-traffic-data)，包含列：`date, hour, origin, destination, passenger_count`。

在我们的案例中，我只保留工作日的数据，因为在工作日不同车站之间有更多有趣的模式。例如，位于住宅区的车站白天可能有更多的通勤乘客进入，而在晚上，位于商业区的车站可能有更多人进站。

![](../Images/533227a1535cfdcb7b77e75a8a2557e8.png)

位于住宅区的车站白天可能有更多的通勤乘客进入。

上面的图是4个不同车站的每小时交通趋势（乘客进入车站的数量）。红色的两条线是新埔和永安市场，这两个车站实际上位于新北市的超拥挤区域。另一方面，蓝色的两条线是台北市政府和忠孝复兴站，这里是大多数公司所在地，也是商业活动发生的地方。

这些趋势反映了这些区域和车站的特点，我们可以注意到，比较它们在通勤时间（早上7点到9点，以及下午5点到7点）期间的趋势时，差异最为明显。

## 4. 使用PCA处理每小时交通数据

为什么在进行进一步的机器学习任务之前要进行降维？

有两个主要原因：

1.  **随着维度的增加**，所有数据点在许多方面看起来都变得稀疏且不相似，这就是所谓的“维度灾难”。

1.  由于交通数据的**高维**特性，它**难以可视化**和**解释**。

通过应用PCA，我们可以识别出不同车站的交通趋势最为明显和具有代表性的时间段。直观地，通过之前显示的图，我们可以假设早上8点和下午6点左右的时间段可能足够具有代表性，能够对车站进行聚类。

记得我们在[上一节](#1f50)提到过PCA的有用输出矩阵Z和W吗？在这里，我们将用MRT案例来解释它们。

***原始数据，X***

![](../Images/8f069365eec76c988e00eee62ec8d0bb.png)

+   索引：车站

+   列：小时

+   数值：特定小时进入的乘客比例（对于每个车站：#乘客 / #总乘客数）

通过这样的X，我们可以通过以下代码应用PCA：

```py
from sklearn.decomposition import PCA

n_components = 3
pca = PCA(n_components=n_components)

X_tran = StandardScaler().fit_transform(X)

pca.fit(X_tran)
```

在这里，我们指定参数**n_components**为3，这意味着PCA将为我们提取最重要的三个主成分。

请注意，这就像是“*拍摄多个三维物体的照片，并按最具代表性的顺序排序，我们选择前三张照片*”，因此如果我们将**n_components**设为5，我们将得到两张额外的照片，但我们的前三张照片仍然保持不变！

***PCA输出，W矩阵***

**W**可以被视为每个特征（即小时数）相对于我们的“图片”的权重，或者更具体地说，**主成分**。

```py
pd.set_option('precision', 2)

W = pca.components_
W_df = pd.DataFrame(W, columns=hour_mapper.keys(), index=[f'PC_{i}' for i in range(1, n_components+1)])
W_df.round(2).style.background_gradient(cmap='Blues')
```

![](../Images/b6650ff1b3d09c6532026a9c8c47e445.png)

对于我们的三个主成分，我们可以看到PC_1在夜间时段的权重大，而PC_2在中午时段的权重大，PC_3则与早晨时间相关。

***PCA输出，Z矩阵***

我们可以将**Z**矩阵解读为车站的表现。

```py
Z = pca.fit_transform(X)

# Name the PCs according to the insights on W matrix
Z_df = pd.DataFrame(Z, index=origin_mapper.keys(), columns=['Night', 'Noon', 'Morning'])

# Look at the stations we demonstrated earlier
Z_df = Z_df.loc[['Zhongxiao_Fuxing', 'Taipei_City_Hall', 'Xinpu', 'Yongan_Market'], :]
Z_df.style.background_gradient(cmap='Blues', axis=1)
```

![](../Images/7149bc2c7b25cf5e4b0ed63493a3cdc3.png)

在我们的案例中，既然我们已经解读了W矩阵并理解了每个成分的潜在含义，我们可以为这些主成分命名。

这四个车站的Z矩阵表明，前两个车站的夜间时段占比更大，而另外两个车站则更多是在早晨时段。这一分布也支持我们在EDA中的发现（回想一下早期部分这四个车站的折线图）。

## 5. 使用K均值聚类对PCA结果进行聚类

在得到PCA结果后，我们将进一步根据车站的交通模式（由3个主成分表示）对车站进行聚类。

在最后一部分中，Z矩阵表示了各个车站在夜间、中午和早晨的表现。

我们将根据这些表示对车站进行聚类，使得同一组中的车站在这三个时间段的客流分布相似。

聚类方法有很多种，比如K均值、DBSCAN、层次聚类等等。由于这里的主要话题是展示PCA的便捷性，我们将跳过实验哪些方法更合适的过程，直接使用**K均值**。

```py
from sklearn.cluster import KMeans

# Fit Z matrix to K-Means model 
kmeans = KMeans(n_clusters=3)
kmeans.fit(Z)
```

在拟合K均值模型后，让我们通过[plotly](https://plotly.com/python/3d-scatter-plots/)绘制三维散点图来可视化这些聚类结果。

```py
import plotly.express as px

cluster_df = pd.DataFrame(Z, columns=['PC1', 'PC2', 'PC3']).reset_index()

# Turn the labels from integers to strings, 
# such that it can be treated as discrete numbers in the plot.
cluster_df['label'] = kmeans.labels_
cluster_df['label'] = cluster_df['label'].astype(str)

fig = px.scatter_3d(cluster_df, x='PC1', y='PC2', z='PC3', 
                       color='label', 
                       hover_data={"origin": (pca_df['index'])},
                       labels={
                          "PC1": "Night",
                          "PC2": "Noon",
                          "PC3": "Morning",
                          },
                      opacity=0.7,
                      size_max=1,
                      width = 800, height = 500
                    ).update_layout(margin=dict(l=0, r=0, b=0, t=0)
                    ).update_traces(marker_size = 5) 
```

![](../Images/7d09ac0d02ba0ba94479078c60e23685.png)

## 6. 台北捷运交通洞察——聚类结果

![](../Images/061d07d462968e4e9f17f73d5838ab51.png)

+   **Cluster 0**：白天有更多的乘客，因此它可能是“居住区”组。

+   **Cluster 2**：傍晚时段有更多的乘客，因此它可能是“商业区”组。

+   **Cluster 1**：白天和夜间都有大量的乘客进入车站，解释这些车站的特性更加复杂，因为不同车站可能有不同的原因。接下来，我们将深入分析该聚类中的两个极端案例。

![](../Images/7609ef3f6ef55d89c339bd98e2def7ff.png)

例如，在**集群1**中，拥有最多乘客的台北车站——台北车站，是台北的一个重要交通枢纽，通勤者可以在这里从公交和铁路系统换乘到地铁。因此，早晚高峰时段的高流量模式是显而易见的。

相反，台北动物园站也位于集群1中，但“白天和晚上的人流都很大”并不适用。实际上，在这两个时段，那里的人不多，因为周围没有多少居民，而且大多数市民平日很少去台北动物园。

这两座车站的模式并不相似，尽管它们在同一集群中。这意味着集群1可能包含了许多实际上并不相似的车站。因此，未来我们需要对K-Means的超参数进行微调，例如集群的数量，像**轮廓系数**和**肘部法则**这样的方式会很有帮助。

# 结论

总结来说，

1.  在交通数据上应用PCA来降低维度可以通过提取3个重要时段（早晨、中午、晚间）来实现，这些时段来自21个工作小时**。**

1.  PCA的输出是W和Z矩阵，其中Z可以视为车站在主成分（时间段）上的表示，而W可以视为主成分（时间段）在原始特征（小时）上的表示。

1.  考虑到W矩阵有助于我们理解每个主成分的潜在含义。

1.  聚类方法可以应用于PCA输出的Z矩阵。

> 请注意，为了专注于本文的主题，我们在这里跳过了EDA和超参数调优部分，但它们实际上非常重要。

感谢你读到这里！

希望你在台北的线上之旅愉快 🫶

## 进一步阅读

+   [KMeans 超参数解释与示例](/kmeans-hyper-parameters-explained-with-examples-c93505820cd3)，Sujeewa Kumaratunga 博士

+   [如何在Python中结合PCA与K-means聚类？](https://365datascience.com/tutorials/python-tutorials/pca-k-means/)，Elitsa Kaloyanova

## 参考文献

+   DSCI 563 讲义，UBC 数据科学硕士课程，Varada Kolhatkar

+   [高维数据上的K均值聚类](https://medium.com/swlh/k-means-clustering-on-high-dimensional-data-d2151e1a4240)，shivangi singh

+   [维度灾难 — 机器学习的“诅咒”](/curse-of-dimensionality-a-curse-to-machine-learning-c122ee33bfeb)，Shashmi Karanam

*除非另有说明，所有图片均来自作者。*
