# 一种非传统的训练-测试-验证集划分方法

> 原文：[`towardsdatascience.com/an-off-beat-approach-to-train-test-validation-split-your-dataset-esp-small-ones-650492b735fb?source=collection_archive---------2-----------------------#2024-07-07`](https://towardsdatascience.com/an-off-beat-approach-to-train-test-validation-split-your-dataset-esp-small-ones-650492b735fb?source=collection_archive---------2-----------------------#2024-07-07)

## 确保在小数据集划分中的分布一致性

[](https://medium.com/@soniamarpreet17?source=post_page---byline--650492b735fb--------------------------------)![Amarpreet Singh](https://medium.com/@soniamarpreet17?source=post_page---byline--650492b735fb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--650492b735fb--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--650492b735fb--------------------------------) [Amarpreet Singh](https://medium.com/@soniamarpreet17?source=post_page---byline--650492b735fb--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--650492b735fb--------------------------------) ·阅读时长 8 分钟·2024 年 7 月 7 日

--

![](img/fa894c871e506a99304ada5e4808197d.png)

使用 Microsoft Designer 生成

我们都需要对总体进行抽样，以进行**统计分析**并获得洞见。当我们这样做时，目标是确保我们的样本分布尽可能接近总体分布。

为此，我们有多种方法：**简单随机采样**（每个样本在总体中被选中的概率相等），**分层采样**（将总体划分为子组，并从每个子组中进行采样），**聚类采样**（将总体分为若干簇，并随机选取整簇），**系统采样**（每隔 n 个样本选择一个样本），等等。每种方法都有其优点，并根据研究的具体需求和特征进行选择。

在本文中，我们不会专注于采样方法本身，而是会介绍如何利用这些概念将用于机器学习方法的数据集划分为**训练-测试-验证**集。这些方法适用于所有类型的**表格数据**。我们将在这里使用 Python 进行演示。

以下是您可能已经了解的一些方法：

# 1\. 简单训练-测试-验证集划分

该方法使用**随机采样**方法。

示例代码：

```py
from sklearn.model_selection import train_test_split
# Assuming X is your feature set and y is your target variable
X_train, X_temp, y_train, y_temp = train_test_split(X, y, test_size=0.4, random_state=42)
X_val, X_test, y_val, y_test = train_test_split(X_temp, y_temp, test_size=0.5, random_state=42)
```

# 2\. 分层训练-测试-验证集划分

该方法确保划分保持与原始数据集**相同的类别比例**（当然还是采用随机采样），这对于类别不平衡的数据集非常有用。当目标变量**不是连续变量**时，该方法有效。

```py
from sklearn.model_selection import train_test_split

# Stratified split to maintain class distribution
X_train, X_temp, y_train, y_temp = train_test_split(X, y, test_size=0.4, stratify=y, random_state=42)
X_val, X_test, y_val, y_test = train_test_split(X_temp, y_temp, test_size=0.5, stratify=y_temp, random_state=42)
```

# 3\. K 折交叉验证

在 K 折交叉验证中，数据集被切分为`k`个子集（折）。模型在`k-1`个折上训练，在剩余的一个折上测试。这个过程会重复`k`次。

```py
from sklearn.model_selection import KFold, train_test_split

kf = KFold(n_splits=5, shuffle=True, random_state=42)

for train_index, test_index in kf.split(X):
    X_train, X_test = X[train_index], X[test_index]
    y_train, y_test = y[train_index], y[test_index]

    # Further split X_train and y_train into train and validation sets
    X_train, X_val, y_train, y_val = train_test_split(X_train, y_train, test_size=0.2, random_state=42)

    # Now you have X_train, X_val, X_test, y_train, y_val, y_test for each fold
    # You can now train and evaluate your model using these sets
```

# 4\. 分层 K 折交叉验证

如名称所示，这是分层抽样和 K 折交叉验证的结合。

```py
from sklearn.model_selection import StratifiedKFold, train_test_split

skf = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

for train_index, test_index in skf.split(X, y):
    X_train, X_test = X[train_index], X[test_index]
    y_train, y_test = y[train_index], y[test_index]

    # Further split X_train and y_train into train and validation sets
    X_train, X_val, y_train, y_val = train_test_split(X_train, y_train, test_size=0.2, stratify=y_train, random_state=42)

    # Now you have X_train, X_val, X_test, y_train, y_val, y_test for each fold
    # You can now train and evaluate your model using these sets
```

完整示例使用：

```py
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score

# Initialize lists to store the scores for each fold
accuracy_scores = []
precision_scores = []
recall_scores = []
f1_scores = []

skf = StratifiedKFold(n_splits=5, shuffle=True, random_state=42)

for train_index, test_index in skf.split(X, y): #y is a categorical target variable
    X_train, X_test = X[train_index], X[test_index]
    y_train, y_test = y[train_index], y[test_index]

    # Further split X_train and y_train into train and validation sets
    X_train, X_val, y_train, y_val = train_test_split(X_train, y_train, test_size=0.2, stratify=y_train, random_state=42)

    # Train the model
    model = LogisticRegression(random_state=42)
    model.fit(X_train, y_train)

    # Validate the model
    y_val_pred = model.predict(X_val)
    val_accuracy = accuracy_score(y_val, y_val_pred)
    val_precision = precision_score(y_val, y_val_pred, average='weighted')
    val_recall = recall_score(y_val, y_val_pred, average='weighted')
    val_f1 = f1_score(y_val, y_val_pred, average='weighted')

    print(f"Validation Scores - Accuracy: {val_accuracy}, Precision: {val_precision}, Recall: {val_recall}, F1 Score: {val_f1}")

    # Test the model
    y_test_pred = model.predict(X_test)
    test_accuracy = accuracy_score(y_test, y_test_pred)
    test_precision = precision_score(y_test, y_test_pred, average='weighted')
    test_recall = recall_score(y_test, y_test_pred, average='weighted')
    test_f1 = f1_score(y_test, y_test_pred, average='weighted')

    # Store the scores
    accuracy_scores.append(test_accuracy)
    precision_scores.append(test_precision)
    recall_scores.append(test_recall)
    f1_scores.append(test_f1)

    print(f"Test Scores - Accuracy: {test_accuracy}, Precision: {test_precision}, Recall: {test_recall}, F1 Score: {test_f1}")

# Calculate and print the average scores across all folds
print(f"\nAverage Test Scores across all folds - Accuracy: {sum(accuracy_scores) / len(accuracy_scores)}, Precision: {sum(precision_scores) / len(precision_scores)}, Recall: {sum(recall_scores) / len(recall_scores)}, F1 Score: {sum(f1_scores) / len(f1_scores)}")
```

现在，你可以使用这些方法来切分数据集，但它们有以下**局限性**：

+   ***随机训练-测试-验证切分****:* 这种方法无法确保切分后的数据具有**相似的** **分布**，尤其是在数据集不够大或目标变量存在不平衡时。

+   ***分层切分****:* 这种方法仅在目标变量（y）是**非连续的**时有效。虽然对于连续目标变量也有一些解决方法（例如通过某些条件将连续变量转换为类别变量，比如如果 y ≥ 四分位数 1 → 1，否则为 0），但这些方法仍然不能确保切分后的数据具有相似的分布。

现在，假设你的数据集中总的观测数**较小**，并且很难确保切分后的数据分布相似。在这种情况下，你可以结合**聚类**和**随机抽样（或分层抽样）**。

以下是我在**我的问题**上如何操作的：

# 5\. 基于聚类的训练-测试-验证切分

在这种方法中，我们首先对数据集进行聚类，然后对每个聚类使用抽样方法来获得数据切分。

例如，使用***HDBSCAN:***

```py
import hdbscan
from sklearn.metrics import silhouette_score
from sklearn.model_selection import ParameterGrid
import random
random.seed(48) #for regeneration of same results

def get_clusters(df):
  to_drop =["cluster_", "ID"]
  req_cols = sorted(set(df.columns) - set(to_drop))
  X = df[req_cols] #keep only required columns in X
  X_std = X.values #no need of scaling the training set for HDBSCAN

  # Define parameter grid for HDBSCAN, you can play with this grid accordingly
  param_grid = {
      'min_cluster_size': list(range(2,20))
      #'min_samples': [1, 2, 3]
  }

  best_score = -1
  best_params = None

  # Iterate over parameter grid
  for params in ParameterGrid(param_grid):
    model = hdbscan.HDBSCAN(**params, gen_min_span_tree=True)
    cluster_labels = model.fit_predict(X_std)
    unique_labels = np.unique(cluster_labels)
    if len(unique_labels) > 1:  # Check if more than one cluster is formed
      silhouette_avg = silhouette_score(X_std, cluster_labels) if len(unique_labels) > 1 else -1
      if silhouette_avg > best_score:
        best_score = silhouette_avg
        best_params = params

  if best_params is not None:
    print(best_params)
    best_model = hdbscan.HDBSCAN(**best_params, gen_min_span_tree=True)
    cluster_labels = best_model.fit_predict(X_std) #get cluster labels from best model
    df["cluster_"] = [str(i) for i in cluster_labels]
  else:
    print("HDBSCAN produced only one cluster label. Unable to split the data.")
    df["cluster_"] = "0" #when no clusters are found

  return df
```

根据你的问题，你也可以使用其他聚类方法，例如***K 均值聚类***：

```py
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score
from sklearn.preprocessing import StandardScaler
from yellowbrick.cluster import KElbowVisualizer

def get_clusters(df):

  to_drop =["cluster_", "ID"]
  req_cols = sorted(set(df.columns) - set(to_drop))
  X = df[req_cols].values #keep only required columns in X

  scaler = StandardScaler()
  X_std = scaler.fit_transform(X) #scaling is needed in case of K-Means

  model = KMeans()
  visualizer = KElbowVisualizer(model, k=(2, 50)) #you can play with the range accordingly
  visualizer.fit(X_std)
  #visualizer.show()

  optimal_n_clusters = visualizer.elbow_value_  #using elbow method to get optimal no. of clusters
  kmeans = KMeans(n_clusters=optimal_n_clusters, random_state=42)
  kmeans.fit(X_std)

  clust_labels = [str(i) for i in kmeans.labels_]

  # Evaluate the clustering using silhouette score
  silhouette_avg = silhouette_score(X_std, clust_labels)

  df["cluster_"] = clust_labels

return df
```

现在你还可以向数据集中添加**粒度层次**（任何类别变量），从而获得更精细的聚类，具体如下：

```py
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score
from sklearn.preprocessing import StandardScaler
from yellowbrick.cluster import KElbowVisualizer

def get_clusters(df):
  # taking animal categorical variable as a level of granularity to split on
  grp1 = df.loc[(df['animal']=='cat')]
  grp2 = df.loc[(df['animal']=='dog')]

  temps = []
  for num, temp in enumerate([grp1, grp2]):
    to_drop =["cluster_", "ID"]
    final_cols = sorted(set(temp.columns) - set(to_drop))
    X = temp[final_cols]

    X = X.values
    scaler = StandardScaler()
    X_std = scaler.fit_transform(X) #scaling of variables is needed for K-Means clustering

    model = KMeans()
    visualizer = KElbowVisualizer(model, k=(2, 50))
    visualizer.fit(X_std)
    # visualizer.show()

    #get optimal no. of clusters, K using elbow method
    optimal_n_clusters = visualizer.elbow_value_  
    kmeans = KMeans(n_clusters=optimal_n_clusters, random_state=42) 
    kmeans.fit(X_std)

    clust_labels = [str(num) + "_" + str(i) for i in kmeans.labels_]

    # Evaluate the clustering using silhouette score
    silhouette_avg = silhouette_score(X_std, clust_labels)

    temp["cluster_"] = clust_labels
    temps.append(temp)

  df = pd.concat(temps, axis=0)

  return df
```

一旦你从任何聚类方法中获得了**聚类标签**，你可以使用**随机抽样或分层抽样**从每个聚类中选择样本。

我们将随机选择索引，然后使用这些索引来选择我们的训练-测试-验证集，如下所示：

```py
import numpy as np
import pandas as pd
from sklearn.model_selection import train_test_split

# Assuming df is your DataFrame, "cluster_" is the column with cluster labels,
unique_clusters = df["cluster_"].unique()

train_indices = []
val_indices = []
test_indices = []

for cluster in unique_clusters:
    cluster_data = df[df["cluster_"] == cluster]
    cluster_indices = cluster_data.index.values
    cluster_y = cluster_data['y'].values

    if stratify_ == True: #if you have categorical target variable
      train_idx, temp_idx, _, temp_y = train_test_split(cluster_indices, cluster_y, test_size=0.4, stratify=cluster_y, random_state=42)
      val_idx, test_idx = train_test_split(temp_idx, test_size=0.5, stratify=temp_y, random_state=42)
    else:
      # Split indices of the current cluster into train and temp (which will be further split into val and test)
      train_idx, temp_idx = train_test_split(cluster_indices, test_size=0.4, random_state=42)
      val_idx, test_idx = train_test_split(temp_idx, test_size=0.5, random_state=42)

    train_indices.extend(train_idx)
    val_indices.extend(val_idx)
    test_indices.extend(test_idx)

# Convert the indices lists to numpy arrays
train_indices = np.array(train_indices)
val_indices = np.array(val_indices)
test_indices = np.array(test_indices)

# Assuming 'X' are the features and 'y' is the target column
X = df.drop(columns=['y', 'cluster_']).values
y = df['y'].values

# Select the corresponding data for train, validation, and test sets
X_train, y_train = X[train_indices], y[train_indices]
X_val, y_val = X[val_indices], y[val_indices]
X_test, y_test = X[test_indices], y[test_indices]
```

根据**我的使用案例**，将目标变量 y 排序，然后选择每个**第 1、第 2 和第 3**个索引作为训练、测试和验证集，分别为（互斥的），即所谓的**系统性随机抽样**，如下所示：

```py
def get_indices(df):
  np.random.seed(seed=48)

  total_length = len(df)
  sample1_length = int(0.60 * total_length) #you can choose proportion accordingly
  remaining_length = total_length - sample1_length

  sample2_length = int(remaining_length / 2)
  sample3_length = total_length - (sample1_length + sample2_length)

  #create an array with range 0 - length of the df
  all_indxs = np.array(range(total_length))

  # Create arrays of indices divisible by 2 and 3 exclusively
  indices_divisible_by_2 = np.array(list(set(np.where(all_indxs % 2 == 0)[0]) - set(np.where(all_indxs % 6 == 0)[0])))
  indices_divisible_by_3 = np.array(list(set(np.where(all_indxs % 3 == 0)[0]) - set([0])))

  #randomly choose indices divisibly by 2 with sample2_length
  sample2_indices = sorted(indices_divisible_by_2[np.random.choice(len(indices_divisible_by_2), size=sample2_length, replace=False)])
  try:
    sample3_indices = sorted(indices_divisible_by_3[np.random.choice(len(indices_divisible_by_3), size=sample3_length, replace=False)])
  except:
    sample3_indices = []

  sample1_indices = sorted(set(all_indxs) - set(sample2_indices) - set(sample3_indices))

  return sample1_indices, sample2_indices, sample3_indices
```

```py
indices_train = []
indices_test = []
indices_val = []

for num, cluster in enumerate(df['cluster_'].unique()):
  temp_df = df[df['cluster_'] == cluster]
  sample1_indices, sample2_indices, sample3_indices = get_indices(temp_df)
  indices_train.append(list(temp_df.iloc[sample1_indices].index))
  indices_test.append(list(temp_df.iloc[sample2_indices].index))
  indices_val.append(list(temp_df.iloc[sample3_indices].index))

# to flatten the list of lists containing indices for train,test,val set
indices_train = [x for xs in indices_train for x in xs]
indices_test = [x for xs in indices_test for x in xs]
indices_val = [x for xs in indices_val for x in xs]
```

```py
def traintestvalsplit(df, id_col, cols_to_drop, cont_var, train_indices, test_indices, val_indices):

  train, test, val = df.loc[train_indices], df.loc[test_indices], df.loc[val_indices]

  # Split the data into train, validation, and test sets based on indices
  X_train = train.drop(cols_to_drop + [cont_var] ,axis=1) #add which columns to drop
  X_test = test.drop(cols_to_drop + [cont_var] ,axis=1)
  X_val = val.drop(cols_to_drop + [cont_var] ,axis=1)

  y_train = train[[cont_var]] #target variable
  y_test = test[[cont_var]]
  y_val = val[[cont_var]]

  train_ids = train[[id_col]] #to preserve the IDs
  test_ids = test[[id_col]]
  val_ids = val[[id_col]]

  print("Train set size:", X_train.shape, len(train_ids))
  print("Test set size:", X_test.shape, len(test_ids))
  print("Validation set size:", X_val.shape, len(val_ids))

  return X_train, X_val, X_test, y_train, y_val, y_test, train_ids, val_ids, test_ids

X_train, X_val, X_test, y_train, y_val, y_test, train_ids, val_ids, test_ids = traintestvalsplit(df, id_col, cols_to_drop, cont_var, train_indices, test_indices, val_indices)
```

上述将聚类与不同抽样方法结合的方式，在数据集观测数量较少时非常有用，因为它们确保了在训练、测试和验证集之间保持相似的分布。

感谢阅读，希望你觉得这篇文章有帮助！
