# 离散化解释：为初学者提供的带代码示例的可视化指南

> 原文：[https://towardsdatascience.com/discretization-explained-a-visual-guide-with-code-examples-for-beginners-f056af9102fa?source=collection_archive---------5-----------------------#2024-10-22](https://towardsdatascience.com/discretization-explained-a-visual-guide-with-code-examples-for-beginners-f056af9102fa?source=collection_archive---------5-----------------------#2024-10-22)

## 数据预处理

## 6种有趣的方法将数字分类到区间中！

[](https://medium.com/@samybaladram?source=post_page---byline--f056af9102fa--------------------------------)[![Samy Baladram](../Images/715cb7af97c57601966c5d2f9edd0066.png)](https://medium.com/@samybaladram?source=post_page---byline--f056af9102fa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f056af9102fa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f056af9102fa--------------------------------) [Samy Baladram](https://medium.com/@samybaladram?source=post_page---byline--f056af9102fa--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f056af9102fa--------------------------------) ·阅读时长10分钟·2024年10月22日

--

![](../Images/6561ef28778421d0abf32c496d9cc832.png)

`⛳️ 更多 [数据预处理](https://medium.com/@samybaladram/list/data-preprocessing-17a2c49b44e4) 解释：· [缺失值插补](/missing-value-imputation-explained-a-visual-guide-with-code-examples-for-beginners-93e0726284eb) · [分类编码](/encoding-categorical-data-explained-a-visual-guide-with-code-example-for-beginners-b169ac4193ae) · [数据缩放](/scaling-numerical-data-explained-a-visual-guide-with-code-examples-for-beginners-11676cdb45cb) ▶ [离散化](/discretization-explained-a-visual-guide-with-code-examples-for-beginners-f056af9102fa?gi=c1bf25229f86) · [过采样与欠采样](/oversampling-and-undersampling-explained-a-visual-guide-with-mini-2d-dataset-1155577d3091) · [数据泄露与预处理](/data-leakage-in-preprocessing-explained-a-visual-guide-with-code-examples-33cbf07507b7)`

大多数机器学习模型要求数据为数值型——所有的对象或分类数据都必须首先转化为数值格式。但实际上，有时分类数据也非常有用（对我们人类来说，它往往比对机器更有用）。离散化（或分箱）正是实现这一点——将数值数据转换为分类数据！

根据你的目标，有很多方法可以将数据分类。在这里，我们将使用一个简单的数据集，通过六种不同的分箱方法来展示。从等宽分箱到基于聚类的方法，我们将把这些数值值划分到一些分类区间中！

![](../Images/637e519d208064455494fb6145230db2.png)

所有视觉效果：作者使用 Canva Pro 创建。针对移动设备进行了优化；在桌面设备上可能会显得过大。

# 什么是离散化？

离散化，也称为分箱，是将连续数值变量转化为离散类别特征的过程。它通过将连续变量的范围划分为若干区间（箱），并根据数据点的值将其分配到相应的箱中。

# 为什么我们需要分箱？

1.  **处理异常值**：分箱可以减少异常值的影响，而不需要删除数据点。

1.  **提高模型性能**：某些算法在处理类别输入时表现更好（例如[伯努利朴素贝叶斯](https://medium.com/towards-data-science/bernoulli-naive-bayes-explained-a-visual-guide-with-code-examples-for-beginners-aec39771ddd6)）。

1.  **简化可视化**：分箱后的数据通常更容易可视化和解读。

1.  **减少过拟合**：它可以防止模型在高精度数据中拟合噪声。

# 哪些数据需要分箱？

## 常常受益于分箱的数据：

1.  **范围较广的连续变量**：具有较大值区间的变量通常可以通过分组受益。

1.  **偏态分布**：分箱可以帮助规范化高度偏斜的数据。

1.  **含有异常值的变量**：分箱可以处理极端值的影响。

1.  **高基数数值数据**：具有许多独特值的变量可以通过分箱进行简化。

## 通常不需要分箱的数据：

1.  **已经是类别数据**：已经是离散类别的变量不需要进一步分箱。

1.  **唯一值较少的离散数值数据**：如果一个变量只有少量可能的值，分箱可能不会提供额外的好处。

1.  **数字ID或代码**：这些是用于唯一标识符的，而非分析用途。

1.  **时间序列数据**：虽然可以对时间序列数据进行分箱，但通常需要专门的技术和谨慎的考虑，总体上不太常见。

![](../Images/aee9adcaa9403a05e391d55f4a08f509.png)

# 数据集

为了演示这些分箱技术，我们将使用这个人工数据集。假设这是某高尔夫球场在15天内收集的天气情况。

![](../Images/0d331773e1d05bd2af57d2579c62304b.png)

**紫外线指数**（0–11的范围）、**湿度**（以%为单位）、**风速**（以英里每小时为单位）、**降水量**（以毫米为单位）、**温度**（以华氏度为单位）、**拥挤度**（0（空）到1（满））

```py
import pandas as pd
import numpy as np

# Create the dataset as a dictionary
data = {
    'UVIndex': [2, 10, 1, 7, 3, 9, 5, 11, 1, 8, 3, 9, 11, 5, 7],
    'Humidity': [15, 95, 10, 98, 18, 90, 25, 80, 95, 40, 20, 30, 85, 92, 12], 
    'WindSpeed': [2, 90, 1, 30, 3, 10, 40, 5, 60, 15, 20, 45, 25, 35, 50],
    'RainfallAmount': [5,2,7,3,18,3,0,1,25,0,9,0,18,7,0],    
    'Temperature': [68, 60, 63, 55, 50, 56, 57, 65, 66, 68, 71, 72, 79, 83, 81],  
    'Crowdedness': [0.15, 0.98, 0.1, 0.85, 0.2, 0.9, 0.92, 0.25, 0.12, 0.99, 0.2, 0.8, 0.05, 0.3, 0.95]
}

# Create a DataFrame from the dictionary
df = pd.DataFrame(data)
```

使用这个数据集，让我们看看如何将各种分箱技术应用于我们的列！

# 方法 1：等宽分箱

等宽分箱将变量的范围划分为指定数量的区间，每个区间具有相同的宽度。

**常见数据类型**：这种方法适用于数据大致呈均匀分布，且最小值和最大值有意义的情况。

**在我们的案例中**：让我们对紫外线指数变量应用等宽分箱法。我们将创建四个箱子：低、适中、高和非常高。我们选择这种方法是因为它能清晰直观地划分指数范围，这对于理解不同指数范围如何影响高尔夫决策非常有用。

![](../Images/d852fd9590a7de9d5af5d3b3aa020b08.png)

```py
# 1\. Equal-Width Binning for UVIndex
df['UVIndexBinned'] = pd.cut(df['UVIndex'], bins=4, 
                             labels=['Low', 'Moderate', 'High', 'Very High'])
```

# 方法 2：等频分箱法（分位数分箱）

等频分箱法创建的箱子包含大致相同数量的观察值。

**常见数据类型**：这种方法对于偏斜数据或当你想确保各类别之间的平衡表示时非常有用。

**在我们的案例中**：让我们对湿度变量应用等频分箱法，创建三个箱子：低、中和高。我们选择这种方法是因为它确保每个类别中的观察值数量相等，这在湿度值在其范围内分布不均时非常有用。

![](../Images/49950ea927ed58b57b5cfcf244bd9d46.png)

```py
# 2\. Equal-Frequency Binning for Humidity
df['HumidityBinned'] = pd.qcut(df['Humidity'], q=3, 
                               labels=['Low', 'Medium', 'High'])
```

# 方法 3：自定义分箱

自定义分箱法允许你根据领域知识或特定要求定义自己的箱子边界。

**常见数据类型**：这种方法在你拥有领域中有意义的特定阈值，或当你想专注于特定值范围时非常理想。

**在我们的案例中**：让我们对降水量应用自定义分箱法。我们选择这种方法是因为雨水有标准化的分类（例如在[这个网站](https://www.researchgate.net/figure/Classification-of-rainfall-events-based-on-daily-rainfall-amount_tbl1_289849694)中描述的），这些分类比任意的划分更有意义。

![](../Images/c211f20ccf1f74df9d51d93058fbdf6c.png)

```py
# 3\. Custom Binning for RainfallAmount
df['RainfallAmountBinned'] = pd.cut(df['RainfallAmount'], bins=[-np.inf, 2, 4, 12, np.inf], 
                                    labels=['No Rain', 'Drizzle', 'Rain', 'Heavy Rain'])
```

# 方法 4：对数分箱法

对数分箱法创建的箱子在大小上呈指数增长。该方法基本上首先应用对数转换，然后执行等宽分箱。

**常见数据类型**：这种方法对于跨越多个数量级或遵循幂律分布的数据尤其有用。

**在我们的案例中**：让我们对风速变量应用对数分箱法。我们选择这种方法是因为风对高尔夫球轨迹的影响可能不是线性的。从0到5英里每小时的变化可能比从20到25英里每小时的变化更为显著。

![](../Images/447e1400db2cdde81998ef29e1c3bb00.png)

```py
# 4\. Logarithmic Binning for WindSpeed
df['WindSpeedBinned'] = pd.cut(np.log1p(df['WindSpeed']), bins=3, 
                               labels=['Light', 'Moderate', 'Strong'])
```

# 方法 5：基于标准差的分箱法

基于标准差的分箱法是根据与均值的标准差距离来创建箱子的。这种方法在处理正态分布数据时很有用，或者当你想根据数据值与集中趋势的偏离程度来分箱时也很有效。

**变化**：用于分箱的标准差数量可以根据分析的具体需求进行调整。分箱的数量通常为奇数（以便有一个中央箱）。某些实现可能使用不等宽度的箱子，靠近均值的箱子较窄，尾部的箱子较宽。

**常见数据类型**：该方法非常适合正态分布的数据，或者当你想识别异常值并理解数据分布时。对于高度偏斜的分布可能不太适用。

**在我们的案例中**：让我们将这种分箱方法应用到我们的“温度”变量上。我们选择这种方法是因为它可以根据温度偏离平均值的程度对温度进行分类，这在理解天气模式或气候趋势时特别有用。

![](../Images/7c740e47c406b24440f075ca4a5884dd.png)

```py
# 5\. Standard Deviation-Based Binning for Temperature
mean_temp, std_dev = df['Temperature'].mean(), df['Temperature'].std()
bin_edges = [
    float('-inf'),  # Ensure all values are captured
    mean_temp - 2.5 * std_dev,
    mean_temp - 1.5 * std_dev,
    mean_temp - 0.5 * std_dev,
    mean_temp + 0.5 * std_dev,
    mean_temp + 1.5 * std_dev,
    mean_temp + 2.5 * std_dev,
    float('inf')   # Ensure all values are captured
]
df['TemperatureBinned'] = pd.cut(df['Temperature'], bins=bin_edges, 
                                 labels=['Very Low', 'Low', 'Below Avg', 'Average','Above Avg', 'High', 'Very High'])
```

# 方法6：K均值分箱

K均值分箱使用K均值聚类算法来创建分箱。它根据数据点之间的相似性将数据点分成不同的簇，每个簇成为一个分箱。

**常见数据类型**：该方法非常适合发现数据中可能不显而易见的分组。它对具有一个或多个峰值的数据效果良好，并且能够根据数据的组织方式进行调整。

**在我们的案例中**：让我们将K均值分箱应用到我们的“拥挤度”变量上。我们选择这种方法是因为它可能揭示出高尔夫球场繁忙程度的自然分组，这些分组可能受许多因素的影响，而这些因素并未被简单的阈值分箱所捕捉。

![](../Images/fd0981955f858214f737ef76dc4980ef.png)

```py
# 6\. K-Means Binning for Crowdedness
kmeans = KMeans(n_clusters=3, random_state=42).fit(df[['Crowdedness']])
df['CrowdednessBinned'] = pd.Categorical.from_codes(kmeans.labels_, categories=['Low', 'Medium', 'High'])
```

# 结论

我们尝试了六种不同的方法来“离散化”我们高尔夫数据中的数字。所以，最终的数据集现在看起来是这样的：

![](../Images/0c8083b55c6b1d171f7f5adc0523d5c6.png)

```py
# Print only the binned columns
binned_columns = [col for col in df.columns if col.endswith('Binned')]
print(df[binned_columns])
```

让我们回顾一下每种分箱技术如何转变了我们的天气数据：

1.  **等宽分箱（紫外线指数）**：将紫外线指数刻度分为四个相等的范围，按“低”到“非常高”分类。这提供了紫外线强度的直观解释。

1.  **等频分箱（湿度）**：将湿度读数分为“低”、“中”和“高”三个类别，每个类别包含相同数量的数据点。这种方法确保了湿度水平的均衡表示。

1.  **对数分箱（风速）**：应用于我们的风速数据，这种方法考虑了风速对天气条件的非线性影响，将风速分为“轻微”、“适中”或“强烈”。

1.  **自定义分箱（降水量）**：利用领域知识将降水量分类为有意义的类别，从“无雨”到“暴雨”。这种方法将测量结果直接转化为实际的天气描述。

1.  **基于标准差的分箱（温度）**：根据温度数据的分布进行分段，范围从“非常低”到“非常高”。这种方法突出了温度偏离平均值的程度。

1.  **K均值分箱（拥挤度）**：在我们的拥挤度数据中显示了自然的分组，可能揭示了某些模式。

在应用分箱技术时，避免毫无思考的使用。每个变量的性质和你的分析目标始终是多样化的，选择分箱方法时要牢记这一点。在许多情况下，尝试多种技术并比较它们的结果能够为你的数据提供最多的见解！

# ⚠️ 分箱的风险

尽管执行分箱听起来很简单，但它也有其自身的风险：

1.  **信息丢失**：当你对数据进行分箱时，本质上是将细节平滑化。这对于发现趋势非常有用，但你可能会错过在箱子内的细微模式或关系。

1.  **任意边界**：分箱边界的选择有时可能更多是艺术而非科学。边界的轻微调整可能导致对数据的不同解释。

1.  **模型影响**：某些模型，特别是基于树的模型，如[决策树](https://medium.com/towards-data-science/decision-tree-classifier-explained-a-visual-guide-with-code-examples-for-beginners-7c863f06a71e)，在使用分箱数据时可能表现更差。它们在找到自己的“箱子”方面非常擅长。

1.  **虚假的安全感**：分箱可以让你的数据看起来更整洁、更易管理，但潜在的复杂性依然存在，只是被隐藏了。

1.  **解释困难**：虽然分箱可以简化分析，但它也可能使解释效果的大小变得更加困难。“高”温度在不同的上下文中可能意味着完全不同的事情。

那么，数据科学家该怎么做呢？以下是我的建议：

+   始终保留未分箱数据的副本。你可能需要重新查看它。

+   尝试不同的分箱策略并比较结果。不要满足于你尝试的第一个方法。

+   查找数据集领域是否已经有标准的方式来对数据进行分类（就像我们上面的“降水量”示例）。

# 🌟 离散化总结

```py
import pandas as pd
import numpy as np
from sklearn.cluster import KMeans

# Create the dataset
data = {
    'UVIndex': [2, 10, 1, 7, 3, 9, 5, 11, 1, 8, 3, 9, 11, 5, 7],
    'Humidity': [15, 95, 10, 98, 18, 90, 25, 80, 95, 40, 20, 30, 85, 92, 12], 
    'WindSpeed': [2, 90, 1, 30, 3, 10, 40, 5, 60, 15, 20, 45, 25, 35, 50],
    'RainfallAmount': [5,2,7,3,18,3,0,1,25,0,9,0,18,7,0],    
    'Temperature': [68, 60, 63, 55, 50, 56, 57, 65, 66, 68, 71, 72, 79, 83, 81],  
    'Crowdedness': [0.15, 0.98, 0.1, 0.85, 0.2, 0.9, 0.92, 0.25, 0.12, 0.99, 0.2, 0.8, 0.05, 0.3, 0.95]
}

# Create a DataFrame from the dictionary
df = pd.DataFrame(data)

# 1\. Equal-Width Binning for UVIndex
df['UVIndexBinned'] = pd.cut(df['UVIndex'], bins=4, 
                             labels=['Low', 'Moderate', 'High', 'Very High'])

# 2\. Equal-Frequency Binning for Humidity
df['HumidityBinned'] = pd.qcut(df['Humidity'], q=3, 
                               labels=['Low', 'Medium', 'High'])

# 3\. Custom Binning for RainfallAmount
df['RainfallAmountBinned'] = pd.cut(df['RainfallAmount'], bins=[-np.inf, 2, 4, 12, np.inf], 
                                    labels=['No Rain', 'Drizzle', 'Rain', 'Heavy Rain'])

# 4\. Logarithmic Binning for WindSpeed
df['WindSpeedBinned'] = pd.cut(np.log1p(df['WindSpeed']), bins=3, 
                               labels=['Light', 'Moderate', 'Strong'])

# 5\. Standard Deviation-Based Binning for Temperature
mean_temp, std_dev = df['Temperature'].mean(), df['Temperature'].std()
bin_edges = [
    float('-inf'),  # Ensure all values are captured
    mean_temp - 2.5 * std_dev,
    mean_temp - 1.5 * std_dev,
    mean_temp - 0.5 * std_dev,
    mean_temp + 0.5 * std_dev,
    mean_temp + 1.5 * std_dev,
    mean_temp + 2.5 * std_dev,
    float('inf')   # Ensure all values are captured
]
df['TemperatureBinned'] = pd.cut(df['Temperature'], bins=bin_edges, 
                                 labels=['Very Low', 'Low', 'Below Avg', 'Average','Above Avg', 'High', 'Very High'])

# 6\. KMeans Binning for Crowdedness
kmeans = KMeans(n_clusters=3, random_state=42).fit(df[['Crowdedness']])
df['CrowdednessBinned'] = pd.Categorical.from_codes(kmeans.labels_, categories=['Low', 'Medium', 'High'])

# Print only the binned columns
binned_columns = [col for col in df.columns if col.endswith('Binned')]
print(df[binned_columns])
```

## 技术环境

本文使用Python 3.7和scikit-learn 1.5版本。虽然讨论的概念普遍适用，但不同版本的具体代码实现可能会略有不同。

## 关于插图

除非另有说明，所有插图均由作者创建，结合了Canva Pro的许可设计元素。

𝙎𝙚𝙚 𝙢𝙤𝙧𝙚 𝘿𝙖𝙩𝙖 𝙋𝙧𝙚𝙥𝙧𝙤𝙘𝙚𝙨𝙨𝙞𝙣𝙜 𝙢𝙚𝙩𝙝𝙤𝙙𝙨 𝙝𝙚𝙧𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----f056af9102fa--------------------------------)

## 数据预处理

[查看列表](https://medium.com/@samybaladram/list/data-preprocessing-17a2c49b44e4?source=post_page-----f056af9102fa--------------------------------)6篇故事![](../Images/f7ead0fb9a8dc2823d7a43d67a1c6932.png)![两个人物相互拥抱，周围浮动着字母‘A’，‘B’，‘C’和数字‘1’，‘2’，‘3’。一个粉色的爱心漂浮在上方，象征着情感。背景是蓝绿色方块的像素化图案，代表数据或编码。这个图像比喻地描绘了分类数据编码的概念，其中类别（ABC）被转化为数字表示（123）。](../Images/72bb3a287a9ca4c5e7a3871e234bcc4b.png)![一幅卡通插图，表示机器学习中的数据缩放。一个高个女人（代表具有大范围的数值特征）正在变小，缩成一个孩子（代表相同特征经过缩放后变成较小范围的样子）。一支红色箭头表示缩小过程，黄色的闪光围绕着孩子，象征着缩放的正面影响。](../Images/d261b2c52a3cafe266d1962d4dbabdbd.png)

𝙔𝙤𝙪 𝙢𝙞𝙜𝙝𝙩 𝙖𝙡𝙨𝙤 𝙡𝙞𝙠𝙚:

![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----f056af9102fa--------------------------------)

## 分类算法

[查看列表](https://medium.com/@samybaladram/list/classification-algorithms-b3586f0a772c?source=post_page-----f056af9102fa--------------------------------)8篇故事![](../Images/f95c1a80b88fe6220b18cd3b2a83a30d.png)![](../Images/6ea70d9d2d9456e0c221388dbb253be8.png)![](../Images/7221f0777228e7bcf08c1adb44a8eb76.png)![Samy Baladram](../Images/835013c69e08fec04ad9ca465c2adf6c.png)

[Samy Baladram](https://medium.com/@samybaladram?source=post_page-----f056af9102fa--------------------------------)

## 回归算法

[查看列表](https://medium.com/@samybaladram/list/regression-algorithms-b0b6959f1b39?source=post_page-----f056af9102fa--------------------------------)5篇故事![一个卡通娃娃，扎着双马尾，戴着粉色帽子。这个“傀儡”娃娃，穿着基本设计的衣服并装饰有心形图案，直观地表现了机器学习中的傀儡回归器概念。正如这个玩具般的形象是一个简化的、静态的人的表现，傀儡回归器也是一种基本模型，作为更复杂分析的基准。](../Images/aa7eeaa18e4bb093f5ce4ab9b93a8a27.png)![](../Images/44e6d84e61c895757ff31e27943ee597.png)![](../Images/7f3e5f3e2aca2feec035ca92e1bc440a.png)
