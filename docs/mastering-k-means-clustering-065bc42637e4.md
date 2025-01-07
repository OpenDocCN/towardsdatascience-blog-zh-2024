# 精通 K-Means 聚类

> 原文：[https://towardsdatascience.com/mastering-k-means-clustering-065bc42637e4?source=collection_archive---------0-----------------------#2024-05-22](https://towardsdatascience.com/mastering-k-means-clustering-065bc42637e4?source=collection_archive---------0-----------------------#2024-05-22)

## 通过这个逐步 Python 教程，从头开始实现 K-Means 算法

[](https://marcusmvls-vinicius.medium.com/?source=post_page---byline--065bc42637e4--------------------------------)[![Marcus Sena](../Images/ff594ec7029e6259f0be6dc031d8a6cd.png)](https://marcusmvls-vinicius.medium.com/?source=post_page---byline--065bc42637e4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--065bc42637e4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--065bc42637e4--------------------------------) [Marcus Sena](https://marcusmvls-vinicius.medium.com/?source=post_page---byline--065bc42637e4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--065bc42637e4--------------------------------) ·阅读时间 8 分钟·2024年5月22日

--

![](../Images/5c7adde7e4e88ee31689443db5e31e44.png)

图片由作者使用 DALL-E 创建。

在本文中，我将展示如果今天开始学习 K-Means 算法，我会如何学习。我们将从基础概念开始，并实现一个使用 Numpy 包执行聚类任务的 Python 类。

无论你是一个试图建立坚实概念理解的机器学习初学者，还是一个有兴趣创建自定义机器学习应用并需要了解算法底层实现的从业者，本文都适合你。

> 目录
> 
> [1\. 介绍](#4970)
> 
> [2\. K-Means 算法做什么？](#7e98)
> 
> [3\. Python 实现](#60a5)
> 
> [4\. 评估与解释](#2e89)
> 
> [5\. 结论与下一步](#655b)

# 1\. 介绍

大多数广泛使用的机器学习算法，如线性回归、逻辑回归、决策树等，都是用来从标记数据中进行预测的，即每个输入包含特征值并与一个标签值相关联。这就是所谓的**监督学习**。

然而，通常我们需要处理没有标签的大数据集。想象一下，一个企业需要根据购买行为、人口统计学、地址等信息了解不同的客户群体，从而能够提供更好的服务、产品和促销活动。

这类问题可以通过使用 **无监督学习** 技术来解决。K-Means 算法是机器学习中广泛使用的无监督学习算法。它简洁而优雅的方法使得将数据集分成 K 个不同的簇成为可能，从而可以从未标记的数据中学习模式。

# 2\. K-Means 算法做什么？

如前所述，K-Means 算法旨在将数据点划分为给定数量的簇。每个簇中的点是相似的，而不同簇中的点有显著的差异。

话虽如此，一个问题随之而来：我们如何定义相似性或差异？在 K-Means 聚类中，欧几里得距离是最常用的度量相似性的指标。

在下图中，我们可以清楚地看到 3 个不同的组。因此，我们可以确定每个组的中心，并且每个点将与最接近的中心关联。

![](../Images/0ec2067871637ccf83430558df44cfbf.png)

带有 200 个观测值的模拟数据集（图片由作者提供）。

通过这样做，从数学角度来说，目标是最小化 *簇内方差*，即每个点与其最接近的中心之间的相似性度量。

执行上述示例中的任务很简单，因为数据是二维的且各组之间差异明显。然而，随着维度的增加和考虑不同的 K 值，我们需要一种算法来处理复杂性。

## 第 1 步：选择初始中心（随机选择）

我们需要为算法提供初始中心向量，这些向量可以从数据中随机选择，或者生成与原始数据相同维度的随机向量。请参见下图中的白色菱形。

![](../Images/33dd2f443181dc5507406b8ec38c9077.png)

初始中心是随机选择的（图片由作者提供）。

## 第 2 步：找到每个点到中心的距离

现在，我们将计算每个数据点到 K 个中心的距离。然后将每个点与离它最近的中心关联。

给定一个包含 *N* 条数据和 *M* 个特征的数据集，可以通过以下公式计算到中心 *c* 的距离：

![](../Images/01b720e1312e5eea301af0f6dfa65c36.png)

欧几里得距离（图像通过 codecogs.com 生成）。

其中：

*k* 从 1 到 *K* 变化；

*D* 是点 n 到 *k* 中心的距离；

*x* 是点向量；

*c* 是中心向量。

因此，对于每个数据点 *n*，我们将得到 K 个距离，然后我们需要将该向量标记为与最小距离的中心关联：

![](../Images/6c1fb0e5d6b3deeba9e459fef5ca022c.png)

（图像通过 codecogs.com 生成）

其中 *D* 是一个包含 *K* 个距离的向量。

## 第 3 步：找到 *K* 个质心并进行迭代

对于每个 *K* 个簇，重新计算质心。新的质心是分配给该簇的所有数据点的均值。然后更新质心的位置，使用新计算出的质心。

检查质心是否发生了显著变化。这可以通过将当前迭代中的质心位置与上一迭代中的质心位置进行比较来完成。

如果质心发生了显著变化，请返回第2步。如果没有，算法已收敛，过程停止。见下图。

![](../Images/0c157b01ce3cb851db9481f3b53f5786.png)

质心的收敛（作者提供的图片）。

# 3\. 在Python中的实现

现在我们已经了解了K-Means算法的基本概念，是时候实现一个Python类了。所使用的包包括Numpy进行数学计算、Matplotlib进行可视化，以及Sklearn中的Make_blobs包用于生成模拟数据。

```py
# import required packages
import numpy as np
import matplotlib.pyplot as plt
from sklearn.datasets import make_blobs
```

该类将具有以下方法：

+   **初始化方法**

一个构造方法，用于初始化算法的基本参数：聚类的值*k*，最大迭代次数*max_iter*，以及当没有显著改善时中止优化的容忍度*tol*。

+   **辅助函数**

这些方法旨在协助训练过程中的优化，如计算欧几里得距离、随机选择初始质心、将每个点分配给最近的质心、更新质心的值以及验证优化是否收敛。

+   **拟合与预测方法**

如前所述，K-Means算法是一种无监督学习技术，这意味着它在训练过程中不需要标签数据。这样，需要一种单一的方法来拟合数据并预测每个数据点所属的聚类。

+   **总误差方法**

评估优化质量的方法，通过计算优化的*总平方误差*。这将在下一节中详细探讨。

以下是完整的代码：

```py
# helper function for calculating Euclidean distance
def euclidean_distance(a,b):
    d = np.sqrt(np.sum((a - b)**2))
    return d

class Kmeans:

    # construct method for hyperparameter initialization
    def __init__(self, k=3, max_iter=100, tol=1e-06):
        self.k = k
        self.max_iter = max_iter
        self.tol = tol

    # randomly picks the initial centroids from the input data
    def pick_centers(self, X):
        centers_idxs = np.random.choice(self.n_samples, self.k)
        return X[centers_idxs]

    # finds the closest centroid for each data point
    def get_closest_centroid(self, x, centroids):
        distances = [euclidean_distance(x, centroid) for centroid in centroids]
        return np.argmin(distances)

    # creates a list with lists containing the idxs of each cluster
    def create_clusters(self, centroids, X):
        clusters = [[] for _ in range(self.k)]
        labels = np.empty(self.n_samples)
        for i, x in enumerate(X):
            centroid_idx = self.get_closest_centroid(x, centroids)
            clusters[centroid_idx].append(i)
            labels[i] = centroid_idx

        return clusters, labels

    # calculates the centroids for each cluster using the mean value 
    def compute_centroids(self, clusters, X):
        centroids = np.empty((self.k, self.n_features))
        for i, cluster in enumerate(clusters):
            centroids[i] = np.mean(X[cluster], axis=0)

        return centroids

    # helper function to verify if the centroids changed significantly
    def is_converged(self, old_centroids, new_centroids):
        distances = [euclidean_distance(old_centroids[i], new_centroids[i]) for i in range(self.k)]
        return (sum(distances) < self.tol)

    # method to train the data, find the optimized centroids and label each data point according to its cluster
    def fit_predict(self, X):
        self.n_samples, self.n_features = X.shape
        self.centroids = self.pick_centers(X)

        for i in range(self.max_iter):
            self.clusters, self.labels = self.create_clusters(self.centroids, X)
            new_centroids = self.compute_centroids(self.clusters, X)
            if self.is_converged(self.centroids, new_centroids):
                break
            self.centroids = new_centroids

    # method for evaluating the intracluster variance of the optimization
    def clustering_errors(self, X):
        cluster_values = [X[cluster] for cluster in self.clusters]
        squared_distances = []
        # calculation of total squared Euclidean distance
        for i, cluster_array in enumerate(cluster_values):
            squared_distances.append(np.sum((cluster_array - self.centroids[i])**2))

        total_error = np.sum(squared_distances)
        return total_error
```

# 4\. 评估与解释

现在我们将使用K-Means类对模拟数据进行聚类。为此，将使用Sklearn库中的make_blobs包。数据由500个二维点组成，具有4个固定的中心。

```py
# create simulated data for examples
X, _ = make_blobs(n_samples=500, n_features=2, centers=4, 
                  shuffle=False, random_state=0)
```

![](../Images/0c6ff01bc9de6ffa2c0a4b553b15d86e.png)

模拟数据（作者提供的图片）。

在使用四个聚类进行训练后，我们得到了以下结果。

```py
model = Kmeans(k=4)
model.fit_predict(X)
labels = model.labels
centroids =model.centroids
plot_clusters(X, labels, centroids)
```

![](../Images/2beea78e39fb0e4730512b9c829df0ad.png)

k=4的聚类（作者提供的图片）。

在这种情况下，算法成功地通过18次迭代计算出了聚类。然而，我们必须记住，模拟数据已经知道了最优的聚类数。在实际应用中，我们通常不知道这个值。

如前所述，K-Means算法的目标是尽量减小*类内方差*。用于计算该方差的度量是*总平方欧几里得距离*，计算公式为：

![](../Images/72661e266df6e85c3b4301407fe58c8c.png)

总平方欧几里得距离公式（作者通过codecogs.com提供的图片）。

其中：

p 是一个簇中的数据点数量；

c_i 是一个簇的质心向量；

K 是簇的数量。

换句话说，上面的公式将数据点到最近质心的距离相加。随着K值的增加，误差会减小。

> 在极端情况下，当K = N时，你会为每个数据点创建一个簇，此时误差将为零。
> 
> Willmott, Paul (2019)。

如果我们将误差与簇的数量绘制出来，并观察图形“弯曲”的位置，我们就能找到最佳簇数。

![](../Images/cb9e6cb88d7dfb252a6602ef6d826078.png)

屏幕图（作者提供的图像）。

如我们所见，图形呈现“肘部形状”，并在K = 4时弯曲，这意味着对于更大的K值，误差的减少将不再显著。

# 5\. 结论和下一步

在本文中，我们介绍了K-Means算法背后的基本概念、用途和应用。此外，通过这些概念，我们能够从零开始实现一个Python类，执行模拟数据的聚类，并展示如何通过屏幕图找到K的最佳值。

然而，由于我们使用的是无监督技术，还有一个额外的步骤。算法可以成功地为簇分配标签，但每个标签的含义是数据科学家或机器学习工程师通过分析每个簇的数据来完成的任务。

此外，我还将留下一些供进一步探索的要点：

+   我们的模拟数据使用了二维点。尝试将该算法应用于其他数据集，并找到K的最佳值。

+   还有其他广泛使用的无监督学习算法，如*层次聚类*。

+   根据问题领域，可能需要使用其他误差度量，如曼哈顿距离和余弦相似度。尝试研究它们。

完整代码可在[此处](https://github.com/Marcussena/ML-and-Ai-from-scratch/tree/main/K-Means)获取：

[](https://github.com/Marcussena/ML-and-Ai-from-scratch/tree/main/K-Means?source=post_page-----065bc42637e4--------------------------------) [## ML-and-Ai-from-scratch/K-Means 在 main · Marcussena/ML-and-Ai-from-scratch

### 从零开始实现的机器学习和人工智能算法 - ML-and-Ai-from-scratch/K-Means 在 main ·…

github.com](https://github.com/Marcussena/ML-and-Ai-from-scratch/tree/main/K-Means?source=post_page-----065bc42637e4--------------------------------)

请随时使用和改进代码、评论、提出建议，并通过[LinkedIn](https://www.linkedin.com/in/marcus-sena-660198150/)、[X](https://twitter.com/MarcusMVLS)和[Github](https://github.com/Marcussena/ML-and-Ai-from-scratch)与我联系。

# 参考文献

[1] Sebastian Raschka (2015), 《Python机器学习》。

[2] Willmott, Paul. (2019). *机器学习：应用数学导论*。Panda Ohana 出版社。

[3] Géron, A. (2017). *动手学机器学习*。O’Reilly Media Inc.

[4] Grus, Joel. (2015). *从零开始学数据科学*. O'Reilly Media Inc.
