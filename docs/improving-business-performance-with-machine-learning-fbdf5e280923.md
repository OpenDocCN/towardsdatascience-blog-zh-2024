# 使用机器学习提升商业表现

> 原文：[https://towardsdatascience.com/improving-business-performance-with-machine-learning-fbdf5e280923?source=collection_archive---------3-----------------------#2024-06-13](https://towardsdatascience.com/improving-business-performance-with-machine-learning-fbdf5e280923?source=collection_archive---------3-----------------------#2024-06-13)

## 无论你是数据科学家、分析师，还是业务分析师，你的目标都是交付能够提升商业表现的项目。

[](https://medium.com/@juanjosemunozp?source=post_page---byline--fbdf5e280923--------------------------------)[![Juan Jose Munoz](../Images/b42d72e9e2a2eaf11da5465e9b041d53.png)](https://medium.com/@juanjosemunozp?source=post_page---byline--fbdf5e280923--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fbdf5e280923--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fbdf5e280923--------------------------------) [Juan Jose Munoz](https://medium.com/@juanjosemunozp?source=post_page---byline--fbdf5e280923--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fbdf5e280923--------------------------------) ·阅读时间15分钟·2024年6月13日

--

![](../Images/d28fb9d54d5dd553dc3c6563016f0125.png)

图片来源：[Daria Nepriakhina 🇺🇦](https://unsplash.com/@epicantus?utm_source=medium&utm_medium=referral) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

可能会有诱惑专注于最新的机器学习发展或解决大问题。然而，通过使用简单的机器学习算法解决一些容易实现的低价值问题，通常也能带来很大的价值。

基准测试就是其中一个低价值问题。它是将业务KPI与类似组织进行对比的过程。它使企业能够从最优秀的企业中学习，并不断提高业绩。

基准测试有两种类型：

> **1\. 内部**：与公司内的单位/产品对比衡量KPI
> 
> **2\. 外部**：与竞争对手对比衡量KPI

在我从事酒店行业的日常工作中，我们常常依赖于收集酒店数据的第三方公司来进行外部基准测试。然而，我们从他们那里获得的数据是有限的。另一方面，我们管理着超过500家酒店，拥有大量潜在的基准测试数据。

这是我们最近设定来解决的低价值问题。

无论你正在进行哪种类型的基准测试，第一步都是选择一组与目标酒店相似的酒店。在酒店行业，我们通常依赖于位置指标、品牌等级、房间数量、价格范围和市场需求。对于一两家酒店，我们通常是手动进行这样的操作，但对500家酒店进行手动操作是不可行的。

一旦确定了需要解决的问题，下一步就是选择使用的工具。机器学习提供了许多工具。然而，这个问题可以通过一个简单的算法族来解决：最近邻算法。

# **最近邻算法族**

最近邻算法族是一种优化问题，旨在找到给定数据集中与指定点最接近或最相似的点。

这些算法在解决许多分类和回归问题方面非常成功。因此，Scikit Learn API 提供了一个出色的最近邻模块。

[](https://scikit-learn.org/stable/modules/classes.html?source=post_page-----fbdf5e280923--------------------------------#module-sklearn.neighbors) [## API 参考

### 这是 Scikit-learn 的类和函数参考。有关更多详细信息，请参考完整的用户指南，因为……

[scikit-learn.org](https://scikit-learn.org/stable/modules/classes.html?source=post_page-----fbdf5e280923--------------------------------#module-sklearn.neighbors)

# 选择合适的算法

大多数人都熟悉 K 最近邻（KNN）；然而，Scikit Learn 提供了多种不同的最近邻算法，涵盖了监督学习和无监督学习任务。

对于我们的这个问题，我们没有任何标签。因此，我们需要寻找一个无监督算法。

如果你浏览 Scikit Learn 文档，你会找到 `NearestNeighbors`。这个算法执行无监督学习，用于实现邻居搜索。

这似乎涵盖了我们解决问题所需的内容。让我们开始准备数据并运行基准模型。

# 基准模型

# 1\. 加载数据

酒店的表现通常取决于位置、品牌和规模。为了我们的分析，我们使用了两个数据集：

酒店数据：以下使用的酒店数据是基于原始数据集人工生成的，用于本次分析。

+   `品牌`: 定义酒店的服务水平：豪华型、高档型、经济型

+   `房间数`: 可供出售的房间数量

+   `市场`: 酒店所在城市的名称

+   `国家`: 国家名称

+   `纬度`: 酒店的纬度位置

+   `经度`: 酒店的经度位置

+   `机场代码`: 最近国际机场的3字母代码

+   `市场层级`: 定义市场的发展水平。

+   `HCLASS`: 表示酒店是城市酒店还是度假酒店

+   `需求`: 表示酒店的年度入住率

+   `价格区间`: 表示酒店的平均价格

我们还知道，酒店的表现可能会受到可达性的影响。为了衡量可达性，我们可以测量酒店距离主要国际机场的远近。机场数据来自世界银行：[https://datacatalog.worldbank.org/search/dataset/0038117](https://datacatalog.worldbank.org/search/dataset/0038117)

+   `Orig`: 3 个字母的机场代码

+   `名称`: 机场名称

+   `总座位数`: 年度乘客量

+   `国家名称`: 机场所在国家的名称

+   `Airpot1Latitude`: 机场的纬度

+   `Airport1Longitude`：机场经度

**全球机场数据集根据知识共享署名 4.0 许可协议授权使用**

让我们导入数据。

```py
import pandas as pd
import numpy as np

data = pd.read_excel("mock_data.xlsx")
airport_data = pd.read_csv("airport_volume_airport_locations.csv")
```

![](../Images/5a02a04415f60ae25407698a38693530.png)

酒店数据样本。图片由作者提供

![](../Images/1d2b250de192743886e633b4d2eb9f60.png)

机场数据样本。图片由作者提供

如前所述，酒店的表现高度依赖于位置。在我们的数据集中，我们有许多关于位置的度量，例如市场国家等。然而，这些定义通常较为宽泛，并不总是理想的。为了缩小相似位置的范围，我们需要创建一个`可达性`度量，定义为到最近国际机场的距离。

为了计算酒店到机场的距离，我们使用哈弗辛公式。哈弗辛公式用于计算球面上两点之间的距离，给定它们的纬度和经度。

```py
# Below code is taken from geeksforgeeks
from math import radians, cos, sin, asin, sqrt

def distance_to_airport(lat, airport_lat, lon, airport_lon):

    #  Convert latitude and longitude values from decimal degrees to radians
    lon = radians(lon)
    airport_lon = radians(airport_lon)
    lat = radians(lat)
    airport_lat = radians(airport_lat)

    # Haversine formula
    dlon = airport_lon - lon
    dlat = airport_lat - lat
    a = sin(dlat / 2)**2 + cos(lat) * cos(airport_lat) * sin(dlon / 2)**2

    c = 2 * asin(sqrt(a))

    # Radius of earth in kilometers.
    r = 6371

    # return distance in KM
    return(c * r)

#Apply the distance_to_airport functions to each hotel
data["distance_to_airport"] = data.apply(lambda row: distance_to_airport(row["Latitude"],row["Airport1Latitude"],row["Longitude"],row["Airport1Longitude"]),axis=1)
data.head()
```

![](../Images/f42916249a4d86d75892a53743b9a4f2.png)

包含机场距离特征的结果数据框。图片由作者提供

下一步是移除我们不需要的任何列。

```py
# Drop Columns that we dont need
# For the purpose of benchmarking we will keep the hotel feautures, and distance to airport
col_to_drop = ["Latitude","Longitude","Airport Code","Orig","Name","TotalSeats","Country Name","Airport1Latitude","Airport1Longitude"]

data_clean = data.drop(col_to_drop,axis=1)
data_clean.head()
```

接下来，我们对所有非数值变量进行编码，以便可以将它们输入到模型中。此时，重要的是要记住，我们需要原始标签来向团队呈现我们建议的分组，并便于验证。为此，我们将把编码信息存储在一个字典中。

```py
from sklearn.preprocessing import LabelEncoder

# Create a LabelEncoder object for each object column
brand_encoder = LabelEncoder()
market_encoder = LabelEncoder()
country_encoder = LabelEncoder()
market_tier_encoder = LabelEncoder()
hclass_encoder = LabelEncoder()

# Fit each LabelEncoder on the unique values of the corresponding column
data_clean['BRAND'] = brand_encoder.fit_transform(data_clean['BRAND'])
data_clean['Market'] = market_encoder.fit_transform(data_clean['Market'])
data_clean['Country'] = country_encoder.fit_transform(data_clean['Country'])
data_clean['Market Tier'] = market_tier_encoder.fit_transform(data_clean['Market Tier'])
data_clean['HCLASS']= hclass_encoder.fit_transform(data_clean['HCLASS'])

# create a dictionnary with all the encoders for reverse encoding
encoders ={"BRAND" : brand_encoder,
           "Market": market_encoder,
           "Country": country_encoder,
           "Market Tier": market_tier_encoder,
           "HCLASS": hclass_encoder
}

data_clean.head()
```

![](../Images/765d8ac866bab658217e85f9dde837cb.png)

编码数据。图片由作者提供

我们的数据现在是数值型的，但正如您所看到的，每列中的值的范围差异非常大。为了避免任何特征的范围不成比例地影响我们的模型，我们需要对数据进行重新缩放。

```py
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
data_scaled = scaler.fit_transform(data_clean)
data_scaled
```

![](../Images/a885e446568eb13a0c3a01d119cf4258.png)

标准化数据。图片由作者提供

此时，我们已准备好生成基准模型。

```py
from sklearn.neighbors import NearestNeighbors

nns = NearestNeighbors()
nns.fit(data_scaled)
nns_results_model_0 = nns.kneighbors(data_scaled)[1]

nns_results_model_0
```

![](../Images/c1e08da417e6a83c4a372f5b5c1436df.png)

模型输出。图片由作者提供

模型的输出是一个索引列表，其中第一个索引是目标酒店，其他索引表示最靠近的酒店。

为了验证模型，我们可以通过直观检查结果。我们可以创建一个函数，该函数接收索引列表并解码值。

```py
def clean_results(nns_results: np.ndarray,
                  encoders: dict,
                  data: pd.DataFrame):
  """
  Returns a dataframe with a list of labels for each Nearest Neighobor group
  """
  result = pd.DataFrame()

  # 1\. Get a list of Nearest Hotels based on our model
  for i in range(len(nns_results)):

    results = {} #empty dictionary to append each rows values

    # Each row in nns_results contains the indexs of the selected nearest neighbors
    # We use those index to get the Hotel names in our main data set
    results["Hotels"] = list(data.iloc[nns_results[i]].index)

    # 2\. Get the values for each features for all Nearest Neighbors groups
    for item in  data_clean.columns:
      results[item] = list(data.iloc[nns_results[i]][item])

    # 3\. Create a row for each Nearest Neighbor group and append to main DataFrame
    df = pd.DataFrame([results])
    result = pd.concat([result,df],axis=0)

  # 4\. Decode the labels to the encoded columns
  for key, val in encoders.items():
    result[key] = result[key].apply(lambda x : list(val.inverse_transform(x)))

  result.reset_index(drop=True,inplace=True) # Reset the index for clarity
  return result

results_model_0 = clean_results(nns_results=nns_results_model_0,
                                encoders=encoders,
                                data=data_clean)
results_model_0.head()
```

![](../Images/4d295e60f17cf12d9ffb4270c94a3250.png)

初步基准分组。图片由作者提供

因为我们使用的是无监督学习算法，所以没有广泛使用的准确度衡量标准。然而，我们可以使用领域知识来验证我们的分组。

通过直观检查这些分组，我们可以看到一些基准分组包含了经济型酒店和奢华型酒店的混合，这在商业上没有意义，因为酒店的需求本质上是不同的。

**我们可以滚动查看数据并注意到一些差异，但我们能否提出自己的准确度衡量标准？**

我们希望创建一个函数来衡量推荐的基准集在每个特征上的一致性。一种方法是通过计算每个集的每个特征的方差来实现。对于每个聚类，我们可以计算每个特征方差的平均值，然后可以将每个酒店聚类的方差取平均，得到一个总的模型得分。

根据我们的领域知识，我们知道，为了设置一个可比较的基准集，我们需要优先考虑相同品牌、可能相同市场和相同国家的酒店，如果使用不同的市场或国家，那么市场层级应该相同。

鉴于此，我们希望我们的度量能够对这些特征的方差给予更高的惩罚。为此，我们将使用加权平均来计算每个基准集的方差。我们还将分别打印关键特征和次要特征的方差。

总结一下，创建准确度量时，我们需要：

1.  **计算分类变量的方差**：一种常见的方法是使用“基于熵的”度量，其中类别的多样性越高，熵（方差）越高。

1.  **计算数值变量的方差**：我们可以计算标准差或范围（最大值与最小值之间的差异）。这衡量了每个聚类内数值数据的分布情况。

1.  **规范化数据**：在应用权重之前，先规范化每个类别的方差得分，以确保没有单一特征因尺度差异而主导加权平均数。

1.  **为不同的度量应用权重**：根据方差类型对其在聚类逻辑中的重要性进行加权。

1.  **计算加权平均数**：计算每个聚类的这些方差得分的加权平均数。

1.  **聚合聚类的得分**：总得分是所有聚类或行的加权方差得分的平均值。较低的平均得分表明我们的模型有效地将相似的酒店分组在一起，最小化了聚类内的方差。

```py
from scipy.stats import entropy
from sklearn.preprocessing import MinMaxScaler
from collections import Counter

def categorical_variance(data):
    """
    Calculate entropy for a categorical variable from a list.
    A higher entropy value indicates datas with diverse classes.
    A lower entropy value indicates a more homogeneous subset of data.
    """
    # Count frequency of each unique value
    value_counts = Counter(data)
    total_count = sum(value_counts.values())
    probabilities = [count / total_count for count in value_counts.values()]
    return entropy(probabilities)

#set scoring weights giving higher weights to the most important features
scoring_weights = {"BRAND": 0.3,
           "Room_count": 0.025,
           "Market": 0.25,
           "Country": 0.15,
           "Market Tier": 0.15,
           "HCLASS": 0.05,
           "Demand": 0.025,
           "Price range": 0.025,
           "distance_to_airport": 0.025}

def calculate_weighted_variance(df, weights):
    """
    Calculate the weighted variance score for clusters in the dataset
    """
    # Initialize a DataFrame to store the variances
    variance_df = pd.DataFrame()

    # 1\. Calculate variances for numerical features
    numerical_features = ['Room_count', 'Demand', 'Price range', 'distance_to_airport']
    for feature in numerical_features:
        variance_df[f'{feature}'] = df[feature].apply(np.var)

    # 2\. Calculate entropy for categorical features
    categorical_features = ['BRAND', 'Market','Country','Market Tier','HCLASS']
    for feature in categorical_features:
        variance_df[f'{feature}'] = df[feature].apply(categorical_variance)

    # 3\. Normalize the variance and entropy values
    scaler = MinMaxScaler()
    normalized_variances = pd.DataFrame(scaler.fit_transform(variance_df),
                                        columns=variance_df.columns,
                                        index=variance_df.index)

    # 4\. Compute weighted average

    cat_weights = {f'{feature}': weights[f'{feature}'] for feature in categorical_features}
    num_weights = {f'{feature}': weights[f'{feature}'] for feature in numerical_features}

    cat_weighted_scores = normalized_variances[categorical_features].mul(cat_weights)
    df['cat_weighted_variance_score'] = cat_weighted_scores.sum(axis=1)

    num_weighted_scores = normalized_variances[numerical_features].mul(num_weights)
    df['num_weighted_variance_score'] = num_weighted_scores.sum(axis=1)

    return df['cat_weighted_variance_score'].mean(), df['num_weighted_variance_score'].mean()
```

为了保持代码简洁并跟踪我们的实验，让我们还定义一个函数来存储我们的实验结果。

```py
# define a function to store the results of our experiments
def model_score(data: pd.DataFrame,
                weights: dict = scoring_weights,
                model_name: str ="model_0"):
  cat_score,num_score = calculate_weighted_variance(data,weights)
  results ={"Model": model_name,
            "Primary features score": cat_score,
            "Secondary features score": num_score}
  return results

model_0_score= model_score(results_model_0,scoring_weights)
model_0_score
```

![](../Images/07f338459cb382b9bf734562b11c454b.png)

基准模型结果。

现在我们已经有了一个基准，接下来看看我们是否能改进我们的模型。

## 通过实验改进我们的模型

到目前为止，当我们运行这段代码时，我们并不需要知道其背后的具体实现：

```py
nns = NearestNeighbors()
nns.fit(data_scaled)
nns_results_model_0 = nns.kneighbors(data_scaled)[1]
```

为了改进我们的模型，我们需要理解模型参数，并了解如何与它们交互以获得更好的基准集。

让我们从查看 Scikit Learn 文档和源代码开始：

```py
# the below is taken directly from scikit learn source

from sklearn.neighbors._base import KNeighborsMixin, NeighborsBase, RadiusNeighborsMixin

class NearestNeighbors_(KNeighborsMixin, RadiusNeighborsMixin, NeighborsBase):
    """Unsupervised learner for implementing neighbor searches.
    Parameters
    ----------
    n_neighbors : int, default=5
        Number of neighbors to use by default for :meth:`kneighbors` queries.

    radius : float, default=1.0
        Range of parameter space to use by default for :meth:`radius_neighbors`
        queries.

    algorithm : {'auto', 'ball_tree', 'kd_tree', 'brute'}, default='auto'
        Algorithm used to compute the nearest neighbors:

        - 'ball_tree' will use :class:`BallTree`
        - 'kd_tree' will use :class:`KDTree`
        - 'brute' will use a brute-force search.
        - 'auto' will attempt to decide the most appropriate algorithm
          based on the values passed to :meth:`fit` method.

        Note: fitting on sparse input will override the setting of
        this parameter, using brute force.

    leaf_size : int, default=30
        Leaf size passed to BallTree or KDTree.  This can affect the
        speed of the construction and query, as well as the memory
        required to store the tree.  The optimal value depends on the
        nature of the problem.

    metric : str or callable, default='minkowski'
        Metric to use for distance computation. Default is "minkowski", which
        results in the standard Euclidean distance when p = 2\. See the
        documentation of `scipy.spatial.distance
        <https://docs.scipy.org/doc/scipy/reference/spatial.distance.html>`_ and
        the metrics listed in
        :class:`~sklearn.metrics.pairwise.distance_metrics` for valid metric
        values.

    p : float (positive), default=2
        Parameter for the Minkowski metric from
        sklearn.metrics.pairwise.pairwise_distances. When p = 1, this is
        equivalent to using manhattan_distance (l1), and euclidean_distance
        (l2) for p = 2\. For arbitrary p, minkowski_distance (l_p) is used.

    metric_params : dict, default=None
        Additional keyword arguments for the metric function.
  """

    def __init__(
        self,
        *,
        n_neighbors=5,
        radius=1.0,
        algorithm="auto",
        leaf_size=30,
        metric="minkowski",
        p=2,
        metric_params=None,
        n_jobs=None,
    ):
        super().__init__(
            n_neighbors=n_neighbors,
            radius=radius,
            algorithm=algorithm,
            leaf_size=leaf_size,
            metric=metric,
            p=p,
            metric_params=metric_params,
            n_jobs=n_jobs,
        )
```

这里有很多事情在发生。

`Nearestneighbor` 类继承自 `NeighborsBase`，后者是最近邻估计器的基类。此类处理最近邻搜索所需的常见功能，如：

+   n_neighbors（使用的邻居数量）

+   radius（基于半径的邻居搜索的半径）

+   algorithm（用于计算最近邻的算法，如 ‘ball_tree’，‘kd_tree’ 或 ‘brute’）

+   metric（用于计算的距离度量）

+   metric_params（距离函数的额外关键字参数）

`Nearestneighbor` 类还继承自 `KNeighborsMixin` 和 `RadiusNeighborsMixin` 类。这些 Mixin 类为 `Nearestneighbor` 添加了特定的邻居搜索功能。

+   `KNeighborsMixin` 提供了一个功能，用于查找离某一点最近的固定数量 k 个邻居。它通过计算邻居之间的距离及其索引，并基于每个点的 k 最近邻来构建一个点之间的连接图。

+   `RadiusNeighborsMixin` 基于半径邻居算法，寻找给定半径内的所有邻居。这种方法在关注捕捉所有位于有意义距离阈值内的点时非常有用，而不是固定数量的点。

根据我们的场景，KNeighborsMixin 提供了我们所需的功能。

在我们能改进模型之前，需要理解一个关键参数，那就是距离度量。

# 简短介绍距离

文档提到，NearestNeighbor 算法默认使用 “Minkowski” 距离，并为我们提供了 SciPy API 的参考。

在 `scipy.spatial.distance` 中，我们可以看到“Minkowski” 距离的两种数学表示：

> *∥u−v∥ p​=( i ∑​∣u i​−v i​∣ p ) 1/p*

这个公式计算的是所有元素差异的幂和的 p 次根。

“Minkowski” 距离的第二种数学表示是：

> *∥u−v∥ p​=( i ∑​w i​(∣u i​−v i​∣ p )) 1/p*

这个公式与第一个类似，但它引入了权重 `wi`，使得某些维度的差异被强调或减弱。在某些特征比其他特征更为重要的情况下，这非常有用。默认情况下，该设置为 None，这意味着所有特征的权重均为 1.0。

**这是改进我们模型的一个很好的选项，因为它允许我们将领域知识传递给模型，并强调对用户最相关的相似性。**

如果我们查看公式，会看到参数 `p`。该参数影响算法计算距离时采取的“路径”。**默认情况下，p=2，这代表欧几里得距离。**

你可以将欧几里得距离理解为通过在两个点之间画一条直线来计算距离。这通常是最短的距离，但在高维空间中，这并不总是计算距离的最理想方式。如需了解为什么是这样，可以参考这篇很棒的在线论文：[https://bib.dbvis.de/uploadedFiles/155.pdf](https://www.google.com/url?q=https%3A%2F%2Fbib.dbvis.de%2FuploadedFiles%2F155.pdf)

**另一个常见的 p 值是 1\. 这代表曼哈顿距离。** 可以将其理解为在类似网格的路径上测量两个点之间的距离。

**另一方面，如果我们将 p 增加到无穷大，我们将得到切比雪夫距离，它被定义为向量中任何对应元素之间的最大绝对差异**。它本质上衡量的是最坏情况下的差异，因此在你希望确保没有单一特征变化过大的情况下非常有用。

通过阅读并熟悉文档，我们发现了一些可以改善模型的可能选项。

# 实验 1：基准模型，n_neighbors = 4

默认情况下，n_neighbors 为 5，但对于我们的基准集，我们希望将每个酒店与 3 个最相似的酒店进行比较。为此，我们需要将 n_neighbors 设置为 4（目标酒店 + 3 个同类酒店）。

```py
nns_1= NearestNeighbors(n_neighbors=4)
nns_1.fit(data_scaled)
nns_1_results_model_1 = nns_1.kneighbors(data_scaled)[1]
results_model_1 = clean_results(nns_results=nns_1_results_model_1,
                                encoders=encoders,
                                data=data_clean)
model_1_score= model_score(results_model_1,scoring_weights,model_name="baseline_k_4")
model_1_score
```

![](../Images/573845ae760e04d4c0d1b3a414bd3e40.png)

我们的主要特征有所改善。图片由作者提供

# 实验 2：添加权重

根据文档，我们可以通过传递权重来强调某些特征之间的关系。根据我们的领域知识，我们已经确定了需要强调的特征，在这种情况下是品牌、市场、国家和市场层级。

```py
# set up weights for distance calculation
weights_dict =  {"BRAND": 5,
           "Room_count": 2,
           "Market": 4,
           "Country": 3,
           "Market Tier": 3,
           "HCLASS": 1.5,
           "Demand": 1,
           "Price range": 1,
           "distance_to_airport": 1}
# Transform the wieghts dictionnary into a list by keeping the scaled data column order
weights = [ weights_dict[idx] for idx in list(scaler.get_feature_names_out())]

nns_2= NearestNeighbors(n_neighbors=4,metric_params={ 'w': weights})
nns_2.fit(data_scaled)
nns_2_results_model_2 = nns_2.kneighbors(data_scaled)[1]
results_model_2 = clean_results(nns_results=nns_2_results_model_2,
                                encoders=encoders,
                                data=data_clean)
model_2_score= model_score(results_model_2,scoring_weights,model_name="baseline_with_weights")
model_2_score
```

![](../Images/a0c93e3306379ea2efef56846f0f095f.png)

主要特征得分持续提高。图片由作者提供

通过权重将领域知识传递给模型显著提高了得分。接下来，让我们测试距离度量的影响。

# 实验 3：使用曼哈顿距离

到目前为止，我们一直在使用欧几里得距离。让我们看看如果改用曼哈顿距离会发生什么。

```py
nns_3= NearestNeighbors(n_neighbors=4,p=1,metric_params={ 'w': weights})
nns_3.fit(data_scaled)
nns_3_results_model_3 = nns_3.kneighbors(data_scaled)[1]
results_model_3 = clean_results(nns_results=nns_3_results_model_3,
                                encoders=encoders,
                                data=data_clean)
model_3_score= model_score(results_model_3,scoring_weights,model_name="Manhattan_with_weights")
model_3_score
```

![](../Images/2139801c701f67fa1332a9ac59be5543.png)

主要得分显著下降。图片由作者提供

# 实验 4：使用切比雪夫距离

将 p 降到 1 后，取得了一些良好的改进。让我们看看当 p 接近无穷大时会发生什么。

要使用切比雪夫距离，我们将把度量参数更改为 `Chebyshev`。默认的 sklearn 切比雪夫度量没有权重参数。为了解决这个问题，我们将定义一个自定义的 `weighted_chebyshev` 度量。

```py
#  Define the custom weighted Chebyshev distance function
def weighted_chebyshev(u, v, w):
    """Calculate the weighted Chebyshev distance between two points."""
    return np.max(w * np.abs(u - v))

nns_4 = NearestNeighbors(n_neighbors=4,metric=weighted_chebyshev,metric_params={ 'w': weights})
nns_4.fit(data_scaled)
nns_4_results_model_4 = nns_4.kneighbors(data_scaled)[1]
results_model_4 = clean_results(nns_results=nns_4_results_model_4,
                                encoders=encoders,
                                data=data_clean)
model_4_score= model_score(results_model_4,scoring_weights,model_name="Chebyshev_with_weights")
model_4_score
```

![](../Images/17c6cda0b99cf5ebd7ac39902c0cf08f.png)

比基准模型要好，但比之前的实验高。图片由作者提供

通过实验，我们成功地降低了主要特征的方差得分。

让我们可视化结果。

```py
results_df = pd.DataFrame([model_0_score,model_1_score,model_2_score,model_3_score,model_4_score]).set_index("Model")
results_df.plot(kind='barh')
```

![](../Images/e5deb0e34f1ce91bf97eb1f80d7ec4cd.png)

实验结果。图片由作者提供

根据我们的需求，使用带权重的曼哈顿距离似乎能提供最准确的基准集。

在实现基准集之前的最后一步是检查得分最高的主要特征集，并确定需要采取哪些步骤。

```py
# Histogram of Primary features score
results_model_3["cat_weighted_variance_score"].plot(kind="hist")
```

![](../Images/9fe38e3b7fcdc89974b7cebd25e6e230.png)

得分分布。图片由作者提供

```py
exceptions = results_model_3[results_model_3["cat_weighted_variance_score"]>=0.4]

print(f" There are {exceptions.shape[0]} benchmark sets with significant variance across the primary features")
```

![](../Images/1c862f02f1fa83283cbd36ef588e4292.png)

图片由作者提供

这 18 个案例需要复审，以确保基准集是相关的。

正如你所见，凭借几行代码和对最近邻搜索的一些理解，我们成功地设置了内部基准集。我们现在可以分发这些基准集，并开始衡量酒店的KPI与其基准集的对比。

你不一定总是需要专注于最前沿的机器学习方法来提供价值。很多时候，简单的机器学习也能带来巨大价值。

在你的业务中，有哪些容易通过机器学习解决的低挂果？

# 参考文献

世界银行. “世界发展指标.” 访问日期：2024年6月11日，来源：[https://datacatalog.worldbank.org/search/dataset/0038117](https://datacatalog.worldbank.org/search/dataset/0038117)

Aggarwal, C. C., Hinneburg, A., & Keim, D. A. (n.d.). 高维空间中距离度量的惊人行为. IBM T. J. Watson 研究中心和哈雷大学计算机科学研究所. 来源：[https://bib.dbvis.de/uploadedFiles/155.pdf](https://bib.dbvis.de/uploadedFiles/155.pdf)

SciPy v1.10.1 手册. `scipy.spatial.distance.minkowski`. 访问日期：2024年6月11日，来源：[https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.distance.minkowski.html](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.distance.minkowski.html)

GeeksforGeeks. 哈弗辛公式计算球面上两点之间的距离. 访问日期：2024年6月11日，来源：[https://www.geeksforgeeks.org/haversine-formula-to-find-distance-between-two-points-on-a-sphere/](https://www.geeksforgeeks.org/haversine-formula-to-find-distance-between-two-points-on-a-sphere/)

scikit-learn. 邻居模块. 访问日期：2024年6月11日，来源：[https://scikit-learn.org/stable/modules/classes.html#module-sklearn.neighbors](https://scikit-learn.org/stable/modules/classes.html#module-sklearn.neighbors)
