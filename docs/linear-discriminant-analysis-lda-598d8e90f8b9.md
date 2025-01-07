# 线性判别分析（LDA）

> 原文：[https://towardsdatascience.com/linear-discriminant-analysis-lda-598d8e90f8b9?source=collection_archive---------3-----------------------#2024-10-12](https://towardsdatascience.com/linear-discriminant-analysis-lda-598d8e90f8b9?source=collection_archive---------3-----------------------#2024-10-12)

## 发现LDA如何帮助识别关键数据特征

[](https://medium.com/@ingo.nowitzky?source=post_page---byline--598d8e90f8b9--------------------------------)[![Ingo Nowitzky](../Images/00d3560055109732b871c001d2b51ab5.png)](https://medium.com/@ingo.nowitzky?source=post_page---byline--598d8e90f8b9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--598d8e90f8b9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--598d8e90f8b9--------------------------------) [Ingo Nowitzky](https://medium.com/@ingo.nowitzky?source=post_page---byline--598d8e90f8b9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--598d8e90f8b9--------------------------------) ·阅读时间12分钟·2024年10月12日

--

![](../Images/5a54046c9d6695be140657cea1a5f7f7.png)

LDA在人工智能和机器学习方法中的分类 | 作者提供的图片

**本文旨在探讨线性判别分析（LDA），重点介绍其核心思想、在代码中的数学实现以及来自制造业的实际例子。

希望你能参与进来。让我们开始吧！**

**从事**工业数据**工作的实践者会熟悉这种情况：数据集通常包含许多特征，而且通常不清楚哪些特征重要，哪些特征较不重要。在此上下文中，“重要”是一个相对的概念。通常，目标是区分数据集之间的差异，即对其进行分类。一个非常典型的任务是区分好的零件和坏的零件，并找出导致零件失败的原因（即特征）。

一种常用的方法是广为人知的主成分分析（PCA）。虽然PCA属于**无**监督方法，但较少使用的LDA是一种有监督的方法，因此它从**标记数据**中学习。因此，LDA特别适用于解释来自大数据集的故障模式。

# 1\. LDA的目标和原理

LDA的目标是线性地组合数据的特征，使得数据集的标签能够彼此最佳分离，并且新特征的数量被减少到预定义的数量。在人工智能术语中，这通常被称为**投影到低维空间**。

![](../Images/dd8246e1f49c04fee04de426915216e6.png)

LDA原理 | 图片修改自[Raschka/Mirjalili, 2019](https://github.com/rasbt/python-machine-learning-book-3rd-edition/blob/master/ch05/images/05_06.png)

# 旁白：什么是维度，什么是维度降维？

![](../Images/0445b3df24a1a26401d95b2d0687883c.png)

维度与图形表示 | 图片由作者提供

维度指的是数据集中特征的数量。

仅凭一个测量（或特征），例如注塑机的工具温度，我们可以将其表示在数轴上。两个特征，比如温度和工具压力，仍然可以处理：我们可以轻松地在 x-y 图表上绘制数据。三个特征——温度、工具压力和注射压力——会变得更加复杂，但我们仍然可以在 3D x-y-z 图表上绘制数据。然而，当我们添加更多特征，例如粘度、电导率等时，复杂性会增加。

![](../Images/08dee7410ef3334e826510e464e4477e.png)

降维 | 图片由作者提供

在实际应用中，数据集通常包含数百甚至数千个特征。这带来了挑战，因为许多机器学习算法在数据集过大时表现较差。此外，所需的数据量随着维度的增加而指数增长，以达到统计显著性。这种现象被称为“维度灾难”。这些因素使得在数据科学过程中，提前确定哪些特征是相关的，并去除那些不重要的特征变得至关重要。

# 2\. LDA是如何工作的？

线性判别分析（LDA）的过程可以分为五个关键步骤。

**步骤 1：** 分别计算数据集中每个 *k* 类别的 *d* 维均值向量。

记住，LDA 是一种监督学习技术，这意味着我们可以利用已知的标签。在第一步中，我们计算所有属于特定类别 *c* 的样本的均值向量 *mean_c*。为此，我们按类别标签过滤特征矩阵，并计算每个 *d* 特征的均值。因此，我们得到 *k* 个均值向量（每个类别一个），每个均值向量的长度为 *d*（对应于 *d* 个特征）。

![](../Images/5ac7e6ee3fbe5483857a12a459e805e5.png)

标签向量 Y 和特征矩阵 X | 图片由作者提供

类别 c 的均值向量

**步骤 2：** 计算散度矩阵（类间散度矩阵和类内散度矩阵）。

类内散度矩阵衡量的是同一类别样本之间的变异性。为了找到具有最佳可分性的子空间，我们旨在最小化此矩阵中的值。相比之下，类间散度矩阵衡量的是不同类别之间的变异性。为了获得最佳可分性，我们旨在最大化此矩阵中的值。

直观地讲，类内散度关注的是每个类别的紧凑度，而类间散度则考察不同类别之间的距离。

![](../Images/fb10ec259899c64b81ce85f1100ba03a.png)

类内和类间散度矩阵 | 图片由作者提供

让我们从**类内散布**矩阵*S_W*开始。它是通过对每个类别的散布矩阵*S_c*求和得到的：

类内散布矩阵S_W

**类间散布**矩阵*S_B*是通过类别均值*mean_c*与整个数据集的总体均值之间的差异得出的：

类间散布矩阵S_B

其中*mean*指的是在所有样本上计算的均值向量，而不考虑它们的类别标签。

**步骤3：** 计算*S_W*和*S_B*比率的特征向量和特征值。

如前所述，为了实现最佳的类分离性，我们的目标是最大化*S_B*并最小化*S_W*。我们可以通过最大化比率*S_B/S_W*来实现这一目标。从线性代数的角度看，这个比率对应于散布矩阵*S_W⁻¹ S_B*，该矩阵在由具有最大特征值的特征向量张成的子空间中最大化。特征向量定义了这个子空间的方向，而特征值表示了变形的幅度。我们将选择与最大特征值相关联的*m*个特征向量。

特征向量和特征值的计算公式

![](../Images/43e9d50886343e62245ff02b8f47d566.png)

特征向量张成的子空间 | 图片来源：作者

**步骤4：** 按照特征值从大到小的顺序排序特征向量，并选择具有最大特征值的*m*个特征向量来构建*d × m-*维度的变换矩阵*W*。

记住，我们的目标不仅是将数据投影到一个能增强类分离性的子空间中，还要进行降维。特征向量将定义我们新特征子空间的坐标轴。为了决定丢弃哪些特征向量以形成低维子空间，我们需要检查它们相应的特征值。简而言之，具有最小特征值的特征向量对类分离贡献最小，我们希望丢弃这些特征向量。通常的方法是按特征值从大到小排列，选择前*m*个特征向量。*m*是一个可以自由选择的参数，*m*越大，变换过程中丢失的信息就越少。

在按特征值从大到小排序特征对并选择前*m*对之后，下一步是构建*d × m-*维度的变换矩阵*W*。通过将*m*个选定的特征向量水平堆叠，得到矩阵*W*：

变换矩阵W

*W*的第一列表示与最大特征值对应的特征向量，第二列表示与第二大特征值对应的特征向量，依此类推。

**步骤5：** 使用*W*将样本投影到新的子空间。

在最后一步，我们使用*d × m-*维度的变换矩阵*W*，该矩阵由前*m*个选定的特征向量组成，将样本投影到新的子空间：

变换后的特征矩阵Z

其中 *X* 是初始的 *n × d* 维特征矩阵，表示我们的样本，而 *Z* 是新子空间中经过转换后的 *n × m* 维特征矩阵。这意味着，所选的特征向量作为“配方”用于将原始特征转换为新特征（**线性判别量**）：具有最大特征值的特征向量提供了 **LD1** 的转换配方，具有第二大特征值的特征向量对应于 **LD2**，依此类推。

![](../Images/9ab651ba543314aeae2afbdab253c687.png)

将 X 投影到线性判别量 LD 上

# 3\. 从头开始实现线性判别分析（LDA）

为了展示理论和数学的实际应用，我们将从头开始使用 numpy 编写我们自己的 LDA。

```py
import numpy as np

class LDA_fs:
    """
    Performs a Linear Discriminant Analysis (LDA)

    Methods
    =======
    fit_transform():
        Fits the model to the data X and Y, derives the transformation matrix W
    and projects the feature matrix X onto the m LDA axes
    """

    def __init__(self, m):
        """
        Parameters
        ==========
        m : int
            Number of LDA axes onto which the data will be projected

        Returns
        =======
        None
        """
        self.m = m

    def fit_transform(self, X, Y):
        """
        Parameters
        ==========
        X : array(n_samples, n_features)
            Feature matrix of the dataset
        Y = array(n_samples)
            Label vector of the dataset

        Returns
        =======
        X_transform : New feature matrix projected onto the m LDA axes

        """

        # Get number of features (columns)
        self.n_features = X.shape[1]
        # Get unique class labels
        class_labels = np.unique(Y)
        # Get the overall mean vector (independent of the class labels)
        mean_overall = np.mean(X, axis=0)  # Mean of each feature
        # Initialize both scatter matrices with zeros
        SW = np.zeros((self.n_features, self.n_features))  # Within scatter matrix
        SB = np.zeros((self.n_features, self.n_features))  # Between scatter matrix

        # Iterate over all classes and select the corresponding data
        for c in class_labels:
            # Filter X for class c
            X_c = X[Y == c]
            # Calculate the mean vector for class c
            mean_c = np.mean(X_c, axis=0)
            # Calculate within-class scatter for class c
            SW += (X_c - mean_c).T.dot((X_c - mean_c))
            # Number of samples in class c
            n_c = X_c.shape[0]
            # Difference between the overall mean and the mean of class c --> between-class scatter
            mean_diff = (mean_c - mean_overall).reshape(self.n_features, 1)
            SB += n_c * (mean_diff).dot(mean_diff.T)

        # Determine SW^-1 * SB
        A = np.linalg.inv(SW).dot(SB)
        # Get the eigenvalues and eigenvectors of (SW^-1 * SB)
        eigenvalues, eigenvectors = np.linalg.eig(A)
        # Keep only the real parts of eigenvalues and eigenvectors
        eigenvalues = np.real(eigenvalues)
        eigenvectors = np.real(eigenvectors.T)

        # Sort the eigenvalues descending (high to low)
        idxs = np.argsort(np.abs(eigenvalues))[::-1]
        self.eigenvalues = np.abs(eigenvalues[idxs])
        self.eigenvectors = eigenvectors[idxs]
        # Store the first m eigenvectors as transformation matrix W
        self.W = self.eigenvectors[0:self.m]

        # Transform the feature matrix X onto LD axes
        return np.dot(X, self.W.T)
```

# 4\. 将 LDA 应用于工业数据集

为了看到 LDA 的实际应用，我们将其应用于生产环境中的典型任务。我们有来自一个简单制造流水线的数据，该流水线只有 7 个工作站。每个工作站发送一个数据点（是的，我知道，只有一个数据点是非常不现实的）。不幸的是，我们的生产线产生了大量的不良品，我们希望找出哪些工作站对这一现象负责。

首先，我们加载数据并初步查看。

```py
import pandas as pd

# URL to Github repository
url = "https://raw.githubusercontent.com/IngoNowitzky/LDA_Medium/main/production_line_data.csv"

# Read csv to DataFrame
data = pd.read_csv(url)

# Print first 5 lines
data.head()
```

![](../Images/c8c63823ac648330d6223f37f1ecc5be.png)

接下来，我们使用 Pandas 中的 `.describe()` 方法来研究数据的分布。

```py
# Show average, min and max of numerical values
data.describe()
```

![](../Images/3e772f56c494366b54ef78ece6386e3f.png)

我们看到有 20,000 个数据点，测量范围从 -5 到 +150。由此，我们需要记住，稍后需要对数据集进行归一化：不同数值的量级，否则会对 LDA 产生负面影响。

我们有多少个良品和多少个不良品？

```py
# Count the number of good and bad parts
label_counts = data['Label'].value_counts()

# Display the results
print("Number of Good and Bad Parts:")
print(label_counts)
```

![](../Images/ea0d6a2c9c981587a36f6beeb5ca4898.png)

我们有 19,031 个良品和 969 个不良品。数据集严重失衡是进一步分析的一个问题。因此，我们选择所有不良品和等数量的随机选择的良品进行后续处理。

```py
# Select all bad parts
bad_parts = data[data['Label'] == 'Bad']

# Randomly select an equal number of good parts
good_parts = data[data['Label'] == 'Good'].sample(n=len(bad_parts), random_state=42)

# Combine both subsets to create a balanced dataset
balanced_data = pd.concat([bad_parts, good_parts])

# Shuffle the combined dataset
balanced_data = balanced_data.sample(frac=1, random_state=42).reset_index(drop=True)

# Display the number of good and bad parts in the balanced dataset
print("Number of Good and Bad Parts in the balanced dataset:")
print(balanced_data['Label'].value_counts())
```

![](../Images/872c6b4b1dd3882783e57eb19d3c52a8.png)

现在，让我们将从头开始实现的 LDA 应用于平衡后的数据集。我们使用 `sklearn` 中的 `StandardScaler` 来对每个特征的测量值进行归一化，使其均值为 0，标准差为 1。我们选择一个线性判别轴 (*m=1*)，将数据投影到该轴上。这有助于我们清楚地看到哪些特征在区分良品和不良品方面最为相关，并通过直方图可视化投影后的数据。

```py
import matplotlib.pyplot as plt
from sklearn.preprocessing import StandardScaler

# Separate features and labels
X = balanced_data.drop(columns=['Label'])
y = balanced_data['Label']

# Normalize the features
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Perform LDA
lda = LDA_fs(m=1)  # Instanciate LDA object with 1 axis
X_lda = lda.fit_transform(X_scaled, y) # Fit the model and project the data

# Plot the LDA projection
plt.figure(figsize=(10, 6))
plt.hist(X_lda[y == 'Good'], bins=20, alpha=0.7, label='Good', color='green')
plt.hist(X_lda[y == 'Bad'], bins=20, alpha=0.7, label='Bad', color='red')
plt.title("LDA Projection of Good and Bad Parts")
plt.xlabel("LDA Component")
plt.ylabel("Frequency")
plt.legend()
plt.show()

# Examine feature contributions to the LDA component
feature_importance = pd.DataFrame({'Feature': X.columns, 'LDA Coefficient': lda.W[0]})
feature_importance = feature_importance.sort_values(by='LDA Coefficient', ascending=False)

# Display feature importance
print("Feature Contributions to LDA Component:")
print(feature_importance)
```

![](../Images/bc19ff379454908643a58e3ad24e81a8.png)

将特征矩阵投影到一个 LD（m=1）上

![](../Images/89ff843406588e3be91c2f2fb0098c46.png)

特征重要性 = 各站点在类分离中的贡献有多大？

直方图显示，我们能够很好地区分良品和不良品，只有少量重叠。这已经是一个积极的结果，表明我们的 LDA 成功了。

表格“LDA组件的特征贡献”中的“LDA系数”表示我们转换矩阵*W*的第一列（也是唯一一列，因为*m=1*）的特征向量。它们表示从各个工站的标准化测量值在LDA轴上的投影方向和大小。表中的数值按降序排列。我们需要同时从表格的顶部和底部读取，因为系数的绝对值表示每个工站在类别分离中的重要性，进而表示其对生产缺陷部件的贡献。符号表示较低或较高的测量值是否会增加缺陷部件的可能性。让我们仔细看看我们的例子：

最大的绝对值来自工站4，其系数为-0.672。 这意味着工站4对部件故障的影响最强。由于负号，较高的正测量值会投影到负的线性判别轴（LD）。直方图显示，负的LD与良好（绿色）部件相关。相反，**在该工站的低值和负值会增加部件故障的可能性**。

第二大绝对值来自工站2，其系数为0.557。 因此，这个工站是导致部件故障的第二大贡献者。正号表示较高的正测量值会投影到正的LD。从直方图中我们知道，较高的正LD值与较高的故障概率相关。换句话说，**工站2的高测量值会导致部件故障**。

第三高的系数来自工站7，其值为-0.486。 这使得工站7成为部件故障的第三大贡献者。负号再次表明，在该工站的较高正值会导致负LD（这对应于良好的部件）。相反，**在该工站的低值和负值会导致部件故障**。

所有其他LDA系数的数量级都比上述三者小得多，**因此，相关的工站对部件故障没有影响**。

我们的LDA分析结果正确吗？正如你可能已经猜到的，生产数据集是合成生成的。我将所有部件标记为有缺陷，其中工站2的测量值大于0.5，工站4的值小于-2.5，工站7的值小于3。**事实证明，LDA完全准确地命中了目标！**

```py
# Determine if a sample is a good or bad part based on the conditions
data['Label'] = np.where(
    (data['Station_2'] > 0.5) & (data['Station_4'] < -2.5) & (data['Station_7'] < 3),
    'Bad',
    'Good'
)
```

# 5. 结论

线性判别分析（LDA）不仅可以减少数据集的复杂性，还能突出驱动类别分离的关键特征，因此在识别生产系统中的故障原因时非常有效。它是一种直接而强大的方法，具有实际应用，并且在像`scikit-learn`这样的库中容易获取。

为了实现最佳效果，平衡数据集（确保每个类别中样本数量相似）并进行归一化（均值为0，标准差为1）是至关重要的。

**下次当你处理包含类别标签和多个特征的大型数据集时，为什么不尝试一下LDA呢？**
