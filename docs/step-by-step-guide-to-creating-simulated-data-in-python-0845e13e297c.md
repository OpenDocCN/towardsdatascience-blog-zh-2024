# Python中创建模拟数据的分步指南

> 原文：[https://towardsdatascience.com/step-by-step-guide-to-creating-simulated-data-in-python-0845e13e297c?source=collection_archive---------2-----------------------#2024-07-10](https://towardsdatascience.com/step-by-step-guide-to-creating-simulated-data-in-python-0845e13e297c?source=collection_archive---------2-----------------------#2024-07-10)

## 一份适合初学者的教程，教你生成自己的数据以便进行分析和测试

[](https://marcusmvls-vinicius.medium.com/?source=post_page---byline--0845e13e297c--------------------------------)[![Marcus Sena](../Images/ff594ec7029e6259f0be6dc031d8a6cd.png)](https://marcusmvls-vinicius.medium.com/?source=post_page---byline--0845e13e297c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0845e13e297c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0845e13e297c--------------------------------) [Marcus Sena](https://marcusmvls-vinicius.medium.com/?source=post_page---byline--0845e13e297c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0845e13e297c--------------------------------) ·7分钟阅读·2024年7月10日

--

![](../Images/6bcf22a841c978218fcf4b88da3132db.png)

图片来源：[Alexandru-Bogdan Ghita](https://unsplash.com/@bogzilla?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

想象一下，你刚刚编写了一个机器学习模型，需要在特定场景下进行测试，或者你正在发布一篇关于自定义数据科学解决方案的学术论文，但现有数据集存在版权限制。另一方面，你可能正处于机器学习项目的调试和故障排除阶段，需要数据来识别和解决问题。

所有这些情况，以及更多情境，都可以从使用模拟数据中获益。通常，真实世界的数据并不容易获得，价格昂贵，或者具有隐私限制。因此，创建合成数据是数据科学从业者和专业人士的一项有用技能。

在本文中，我介绍了几种创建模拟数据、玩具数据集和“虚拟”值的方法和技巧，这些都是使用Python从零开始创建的。一些解决方案使用了Python库中的方法，另一些则是使用Python内置函数的技术。

接下来的各个方法在我进行研究任务、学术论文撰写、模型训练或测试时都曾对我非常有帮助。希望读者能在文章结尾探索这个笔记本，并将其作为指南，或者作为未来项目的参考。

> 目录
> 
> [1\. 使用NumPy](#0751)
> 
> [2\. 使用Scikit-learn](#d80b)
> 
> [3\. 使用SciPy](#5cfb)
> 
> [4\. 使用Faker](#855f)
> 
> [5\. 使用合成数据库 (SDV)](#1937)
> 
> [结论与下一步](#29f3)

# 1\. 使用NumPy

最著名的Python库之一，专注于线性代数和数值计算的SciPy，同样也有助于数据生成。

+   **线性数据生成**

在这个示例中，我将展示如何创建一个带噪声且与目标值具有线性关系的数据集。这对于测试线性回归模型非常有用。

```py
# importing modules
from matplotlib import pyplot as plt
import numpy as np

def create_data(N, w):
  """
  Creates a dataset with noise having a linear relationship with the target values.
  N: number of samples
  w: target values
  """
  # Feature matrix with random data
  X = np.random.rand(N, 1) * 10
  # target values with noise normally distributed
  y = w[0] * X + w[1] + np.random.randn(N, 1)
  return X, y

# Visualize the data
X, y = create_data(200, [2, 1])

plt.figure(figsize=(10, 6))
plt.title('Simulated Linear Data')
plt.xlabel('X')
plt.ylabel('y')
plt.scatter(X, y)
plt.show()
```

![](../Images/95b874c7e5687dcb196b28ba4312da16.png)

模拟线性数据（图片由作者提供）。

+   **时间序列数据**

在这个示例中，我将使用NumPy生成具有线性趋势和季节性成分的合成时间序列数据。这个示例对金融建模和股市预测非常有用。

```py
def create_time_series(N, w):
  """
  Creates a time series data with a linear trend and a seasonal component.
  N: number of samples
  w: target values
  """
  # time values
  time = np.arange(0,N)
  # linear trend
  trend = time * w[0]
  # seasonal component
  seasonal = np.sin(time * w[1])
  # noise
  noise = np.random.randn(N)
  # target values
  y = trend + seasonal + noise
  return time, y

# Visualize the data
time, y = create_time_series(100, [0.25, 0.2])

plt.figure(figsize=(10, 6))
plt.title('Simulated Time Series Data')
plt.xlabel('Time')
plt.ylabel('y')

plt.plot(time, y)
plt.show()
```

![](../Images/71c35febf35269780dbb75f742d81401.png)

+   **自定义数据**

有时需要具有特定特征的数据。例如，你可能需要一个高维数据集，其中只有少数几个维度是有意义的，适合用于降维任务。在这种情况下，下面的示例展示了生成此类数据集的合适方法。

```py
# create simulated data for analysis
np.random.seed(42)
# Generate a low-dimensional signal
low_dim_data = np.random.randn(100, 3)

# Create a random projection matrix to project into higher dimensions
projection_matrix = np.random.randn(3, 6)

# Project the low-dimensional data to higher dimensions
high_dim_data = np.dot(low_dim_data, projection_matrix)

# Add some noise to the high-dimensional data
noise = np.random.normal(loc=0, scale=0.5, size=(100, 6))
data_with_noise = high_dim_data + noise

X = data_with_noise
```

上面的代码片段创建了一个包含100个观察值和6个特征的数据集，基于一个仅有3个维度的低维数组。

# 2\. 使用Scikit-learn

除了机器学习模型，Scikit-learn还有一些数据生成器，用于构建具有可控大小和复杂度的人工数据集。

+   **制作分类数据集**

*make_classification* 方法可以用来创建一个随机的多类别数据集。该方法允许根据选择的观察值、特征和类别数来创建数据集。

它对于测试和调试分类模型（如支持向量机、决策树和朴素贝叶斯）也非常有用。

```py
X, y = make_classification(n_samples=1000, n_features=5, n_classes=2)

#Visualize the first rows of the synthetic dataset
import pandas as pd
df = pd.DataFrame(X, columns=['feature1', 'feature2', 'feature3', 'feature4', 'feature5'])
df['target'] = y
df.head()
```

![](../Images/98d2c98069f8ddb2bca5fb1e96c1fe54.png)

数据集的前几行（图片由作者提供）。

+   **制作回归数据**

类似地，*make_regression* 方法对于创建回归分析数据集非常有用。它允许设置观察值的数量、特征数量、偏差以及生成数据集的噪声。

```py
from sklearn.datasets import make_regression

X,y, coef = make_regression(n_samples=100, # number of observations
                            n_features=1,  # number of features
                            bias=10, # bias term
                            noise=50, # noise level
                            n_targets=1, # number of target values
                            random_state=0, # random seed
                            coef=True # return coefficients
                            )
```

![](../Images/5a975196aa5fb709579f692b45283083.png)

使用*make_regression*生成的模拟数据（图片由作者提供）。

+   **制作簇数据**

*make_blobs* 方法允许创建人工的“簇”数据，这些数据可用于聚类任务。它允许设置数据集中的总点数、簇的数量以及簇内的标准差。

```py
from sklearn.datasets import make_blobs

X,y = make_blobs(n_samples=300, # number of observations
                n_features=2, # number of features
                centers=3, # number of clusters
                cluster_std=0.5, # standard deviation of the clusters
                random_state=0)
```

![](../Images/5df7a4743c1a21b323178106b0755e4e.png)

聚类中的模拟数据（图片由作者提供）。

# 3\. 使用SciPy

SciPy（科学Python）库与NumPy一起，是处理数值计算、优化、统计分析和许多其他数学任务的最佳库之一。SciPy的stats模型可以根据多种统计分布（如正态分布、二项分布和指数分布）创建模拟数据。

```py
from scipy.stats import norm, binom, expon
```

```py
# Normal Distribution
norm_data = norm.rvs(size=1000)
```

![](../Images/752f226fbcac411c23456b8dbed63f3e.png)

图片由作者提供。

```py
# Binomial distribution
binom_data = binom.rvs(n=50, p=0.8, size=1000)
```

![](../Images/08bb072cfa22c15fcc2a1b3fbebefc1d.png)

图片由作者提供。

```py
# Exponential distribution
exp_data = expon.rvs(scale=.2, size=10000)
```

![](../Images/0454620f63b59f62107d16ea92a5e27d.png)

图片由作者提供。

# 4\. 使用Faker

那么，非数值数据呢？我们通常需要对非数值或用户数据（如姓名、地址和电子邮件）进行训练。一个创建与用户信息相似的现实数据的解决方案是使用Faker Python库。

Faker库可以生成逼真的数据，用于测试应用程序和机器学习分类器。在下面的示例中，我展示了如何创建一个包含姓名、地址、电话号码和电子邮件信息的假数据集。

```py
from faker import Faker

def create_fake_data(N):
  """
  Creates a dataset with fake data.
  N: number of samples
  """
  fake = Faker()
  names = [fake.name() for _ in range(N)]
  addresses = [fake.address() for _ in range(N)]
  emails = [fake.email() for _ in range(N)]
  phone_numbers = [fake.phone_number() for _ in range(N)]
  fake_df = pd.DataFrame({'Name': names, 'Address': addresses, 'Email': emails, 'Phone Number': phone_numbers})
  return fake_df

fake_users = create_fake_data(100)
fake_users.head()
```

![](../Images/333c512f49172854f7cae1b36f3f3c46.png)

使用Faker生成的假用户数据（作者提供的图片）。

# 5\. 使用Synthetic Data Vault（SDV）

如果你有一个数据集，其中的观测数据不足，或者你需要更多与现有数据集相似的数据来补充机器学习模型的训练步骤，该怎么办？Synthetic Data Vault（SDV）是一个Python库，它通过统计模型创建合成数据集。

在下面的示例中，我们将使用SDV扩展一个演示数据集：

```py
from sdv.datasets.demo import download_demo

# Load the 'adult' dataset
adult_data, metadata = download_demo(dataset_name='adult', modality='single_table')
adult_data.head()
```

![](../Images/929606b4f0f2e46c3484fbee6aedcd8c.png)

成人演示数据集。

```py
from sdv.single_table import GaussianCopulaSynthesizer
# Use GaussianCopulaSynthesizer to train on the data
model = GaussianCopulaSynthesizer(metadata)
model.fit(adult_data)

# Generate Synthetic data
simulated_data = model.sample(100)
simulated_data.head()
```

![](../Images/2716d3930d21bcb642c7b2721b84b1e5.png)

模拟样本（作者提供的图片）。

请观察这些数据与原始数据集非常相似，但它们是合成数据。

# 结论与下一步

文章展示了创建模拟和合成数据集的5种方法，这些数据集可以用于机器学习项目、统计建模以及其他涉及数据的任务。文中展示的示例易于跟随，因此我建议你深入探索代码，阅读相关文档，并开发出更适合每个需求的数据生成方法。

如前所述，数据科学家、机器学习专家和开发人员可以通过使用合成数据集来提高模型性能，降低生产和应用测试的成本。

查看本文中探讨的所有方法的笔记本：

[](https://github.com/Marcussena/Synthetic-data-generation?source=post_page-----0845e13e297c--------------------------------) [## GitHub - Marcussena/Synthetic-data-generation: 数据科学和机器学习的模拟数据生成

### 数据科学和机器学习的模拟数据生成 - Marcussena/Synthetic-data-generation

github.com](https://github.com/Marcussena/Synthetic-data-generation?source=post_page-----0845e13e297c--------------------------------)

# 参考资料

[1] DataCamp. “使用Python和Faker创建合成数据。” DataCamp, [https://www.datacamp.com/tutorial/creating-synthetic-data-with-python-faker-tutorial](https://www.datacamp.com/tutorial/creating-synthetic-data-with-python-faker-tutorial). 访问日期：2024年7月4日。

[2] Scikit-learn. “生成的数据集。”Scikit-learn, [https://scikit-learn.org/stable/datasets/sample_generators.html#sample-generators](https://scikit-learn.org/stable/datasets/sample_generators.html#sample-generators). 访问日期：2024年7月4日。

[3] SDV 用户指南。“高斯 Copula 用户指南。” SDV, [https://sdv.dev/SDV/user_guides/single_table/gaussian_copula.html](https://sdv.dev/SDV/user_guides/single_table/gaussian_copula.html)。访问时间：2024年7月4日。

[4] SciPy 用户指南。“SciPy 教程。” SciPy 文档, [https://docs.scipy.org/doc/scipy/tutorial/index.html](https://docs.scipy.org/doc/scipy/tutorial/index.html)。访问时间：2024年7月4日。
