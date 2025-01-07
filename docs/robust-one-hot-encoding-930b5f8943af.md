# 稳健的独热编码

> 原文：[https://towardsdatascience.com/robust-one-hot-encoding-930b5f8943af?source=collection_archive---------4-----------------------#2024-04-26](https://towardsdatascience.com/robust-one-hot-encoding-930b5f8943af?source=collection_archive---------4-----------------------#2024-04-26)

## Python和R中的生产级独热编码技术

[](https://medium.com/@hc.ekne?source=post_page---byline--930b5f8943af--------------------------------)[![Hans Christian Ekne](../Images/c85483d8b5dd89584b996b321b7f4a45.png)](https://medium.com/@hc.ekne?source=post_page---byline--930b5f8943af--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--930b5f8943af--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--930b5f8943af--------------------------------) [Hans Christian Ekne](https://medium.com/@hc.ekne?source=post_page---byline--930b5f8943af--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--930b5f8943af--------------------------------) ·阅读时间11分钟·2024年4月26日

--

![](../Images/3b472814a17418f0804ebfbcc8a1e9db.png)

图像由作者使用DALL-E生成/还是Dali？;)

你是否在机器学习生产环境中遇到过崩溃？

这并不有趣，尤其是当涉及到可以避免的问题时。一个经常引起问题的原因是数据的独热编码。通过我自己的经验，我发现很多这些问题在遵循一些与独热编码相关的最佳实践时是可以大大避免的。在这篇文章中，我将简要介绍这个话题，并通过一些简单的示例分享一些最佳实践，以确保你的机器学习模型的稳定性。

# 独热编码

## 什么是独热编码？

独热编码是将存储在一列中的因子变量转化为多个列中的虚拟变量，并以0和1的形式表示的做法。一个简单的例子说明了这个概念。

例如，考虑这个包含一些数字和颜色列的数据集：

```py
import pandas as pd

# Creating the training_data DataFrame in Python
training_data = pd.DataFrame({
    'numerical_1': [1, 2, 3, 4, 5, 6, 7, 8],
    'color_1_': ['black', 'black', 'red', 'green', 
                'green', 'black', 'red', 'blue'],
    'color_2_': ['black', 'blue', 'pink', 'purple', 
                'black', 'blue', 'pink', 'purple']
})
```

或者更直观地展示：

![](../Images/de251ce1de486b95f24cf64ab9c324bd.png)

训练数据，包含3列/图像由作者提供

`color_1_`列也可以像下面的表格一样表示：

![](../Images/17f1d9b0fd2a6e5438855cd8aa8dbabf.png)

“color_1_”的独热编码表示 / 图像由作者提供

将`color_1_`从一个单列的紧凑表示转化为多列的二进制表示，这就是我们所说的独热编码（one-hot encoding）。

## 为什么我们要使用它？

使用独热编码有多个原因。它们可能与避免隐含排序、提高模型性能，或只是使数据与各种算法兼容有关。

例如，当你将一个类别变量（如颜色）编码成数值结构时（例如：黑色为1，绿色为2，红色为3），如果不将其转换为虚拟变量，模型可能会错误地将数据误解为存在顺序关系（黑色 < 绿色 < 红色），而实际上并不存在这种顺序。

此外，在训练神经网络时，最佳实践是在将数据输入神经网络之前对数据进行标准化，对于类别变量，独热编码是一种不错的方法。其他线性模型，如逻辑回归和线性回归，假设输入是线性关系和数值型数据，因此对于这一类模型，独热编码也是一个好方法。

此外，进行独热编码的过程迫使我们确保不会将未见过的因子级别输入到我们的机器学习模型中。

最终，独热编码使得机器学习模型更容易理解数据，从而做出更好的预测。

# 独热编码失败的主要原因

我们构建传统机器学习模型的方式是，首先在“训练数据集”上训练模型——通常是一个历史数据集——然后在新的数据集（即“推理数据集”）上生成预测。如果训练数据集和推理数据集的列不匹配，机器学习算法通常会失败。主要原因是推理数据集中缺少列或包含新的因子级别。

## 第一个问题：缺失因子

对于以下示例，假设你使用上面的数据集来训练机器学习模型。你将数据集进行独热编码转换成虚拟变量，并且你的完全转换后的训练数据如下所示：

![](../Images/3838878fe8c718db9f2226fa542dc159.png)

使用`pd.get_dummies`转换后的训练数据集 / 图片来自作者

现在，让我们引入推理数据集，这就是你用于进行预测的数据集。假设它是如下所示：

```py
# Creating the inference_data DataFrame in Python
inference_data = pd.DataFrame({
    'numerical_1': [11, 12, 13, 14, 15, 16, 17, 18],
    'color_1_': ['black', 'blue', 'black', 'green', 
                'green', 'black', 'black', 'blue'],
    'color_2_': ['orange', 'orange', 'black', 'orange', 
                'black', 'orange', 'orange', 'orange']
})
```

![](../Images/9647cc2c91077b979ff0ab9f045c0c76.png)

推理数据集（包含3列）/ 图片来自作者

使用我们上面使用的简单独热编码策略（`pd.get_dummies`）

```py
# Converting categorical columns in inference_data to 
# Dummy variables with integers
inference_data_dummies = pd.get_dummies(inference_data, 
  columns=['color_1_', 'color_2_']).astype(int)
```

这将以相同的方式转换你的推理数据集，你会得到如下的数据集：

![](../Images/beb8f6bd286cd54bd9ff34350c8cefda.png)

使用`pd.get_dummies`转换后的推理数据集 / 图片来自作者

你注意到问题了吗？第一个问题是推理数据集中缺少以下列：

```py
missing_colmns =['color_1__red', 'color_2__pink', 
  'color_2__blue', 'color_2__purple']
```

如果你在一个用“训练数据集”训练的模型中运行这段代码，通常会崩溃。

## 第二个问题：新因子

另一个可能发生的独热编码问题是，如果推理数据集中包含了新的、未见过的因子。再次考虑上面的数据集。如果你仔细检查，你会发现推理数据集现在有了一个新列：`color_2__orange`。

这是与之前问题相反的情况，我们的推理数据集包含了训练数据集中没有的新列。这实际上是一个常见的情况，如果你的某个因子变量发生了变化，就可能会发生这种情况。例如，如果上面提到的颜色代表汽车的颜色，而一个汽车生产商突然开始生产橙色的汽车，那么这些数据可能在训练数据中不可用，但仍然可能出现在推理数据中。在这种情况下，你需要一种健壮的方式来处理这个问题。

有人可能会争论，为什么不直接将转化后的训练数据集中的所有列列为推理数据集所需的列呢？这里的问题是，通常你无法事先知道训练数据中的因子水平。

例如，新的水平可能会定期引入，这可能使得维护变得困难。此外，还需要将推理数据集与训练数据进行匹配，因此你需要检查所有实际转化后的列名，这些列名是用于训练算法的，然后将它们与转化后的推理数据集进行匹配。如果有任何列缺失，你需要插入新列并填充 0 值，如果有多余的列，比如上面的`color_2__orange`列，那么需要删除这些列。这是一种相当繁琐的解决问题的方法，幸好有更好的选择可供使用。

# 解决方案

解决这个问题的方法相对直接，然而许多试图简化预测模型创建过程的包和库并未很好地实现它。关键在于拥有一个先在训练数据上进行拟合的函数或类，然后使用该函数或类的相同实例来转化训练数据集和推理数据集。下面我们将探索如何使用 Python 和 R 来完成这一操作。

## 在 Python 中

Python 无疑是进行机器学习的最佳编程语言之一，这主要得益于其广泛的开发者网络和成熟的包库，以及它的易用性，促进了快速开发。

关于我们上述描述的与独热编码相关的问题，它们可以通过使用广泛可用且经过测试的 scikit-learn 库来缓解，尤其是使用`sklearn.preprocessing.OneHotEncoder`类。因此，让我们看看如何在我们的训练和推理数据集上使用它来创建一个健壮的独热编码。

```py
from sklearn.preprocessing import OneHotEncoder

# Initialize the encoder
enc = OneHotEncoder(handle_unknown='ignore')

# Define columns to transform
trans_columns = ['color_1_', 'color_2_']

# Fit and transform the data
enc_data = enc.fit_transform(training_data[trans_columns])

# Get feature names
feature_names = enc.get_feature_names_out(trans_columns)

# Convert to DataFrame
enc_df = pd.DataFrame(enc_data.toarray(), 
                          columns=feature_names)

# Concatenate with the numerical data
final_df = pd.concat([training_data[['numerical_1']], 
                      enc_df], axis=1)
```

这将生成一个最终的 `DataFrame`，如下面所示：

![](../Images/f305e941d89808c5f5ac9eb6977727f2.png)

使用 sklearn 转化后的训练数据集 / 作者提供的图片

如果我们分解上面的代码，我们会看到第一步是初始化编码器类的实例。我们使用`handle_unknown='ignore'`选项，以便在使用编码器转化推理数据集时避免出现未知值的问题。

之后，我们将`fit`和`transform`操作合并为一步，使用`fit_transform`方法。最后，我们从编码后的数据创建一个新的数据框，并将其与原始数据集的其余部分拼接在一起。

现在的任务是使用编码器来转换我们的推断数据集。

```py
# Transform inference data
inference_encoded = enc.transform(inference_data[trans_columns])

inference_feature_names = enc.get_feature_names_out(trans_columns)

inference_encoded_df = pd.DataFrame(inference_encoded.toarray(), 
                                    columns=inference_feature_names)

final_inference_df = pd.concat([inference_data[['numerical_1']], 
                                inference_encoded_df], axis=1)
```

与之前我们使用简单的`pandas.get_dummies`时不同，现在我们看到我们的新数据集`final_inference_df`具有与训练数据集相同的列。

![](../Images/cd25ea429be35479ec782fb89827e12e.png)

转换后的推断数据集，具有正确的列 / 图片来源：作者

除了我们在上面的代码中展示的内容之外，`sklearn.preprocessing`中的`OneHotEncoder`类还有很多其他功能，也同样非常有用。

例如，它允许你设置`min_frequency`和`max_categories`选项。顾名思义，`min_frequency`选项允许你指定一个最低频率，低于该频率的类别将被视为不常见，并与其他不常见类别一起分组，或者是`max_categories`选项，它限制了类别的总数。如果你不希望在训练数据集中创建过多的列，这后一种选项特别有用。

欲了解完整功能概述，请访问以下文档页面：

[](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html?source=post_page-----930b5f8943af--------------------------------#sklearn.preprocessing.OneHotEncoder) [## sklearn.preprocessing.OneHotEncoder

### 使用sklearn.preprocessing.OneHotEncoder的示例：scikit-learn 1.4版本发布亮点……

scikit-learn.org](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html?source=post_page-----930b5f8943af--------------------------------#sklearn.preprocessing.OneHotEncoder)

## 在R中

我的几个客户使用R在生产环境中运行机器学习模型——而且R有很多优秀的功能。在`polars`发布之前，R的`data.table`包在速度和效率上优于`pandas`所能提供的。然而，R无法访问像Python中`scikit-learn`这样的生产级别的包。（虽然有一些库，但它们不像`scikit-learn`那样成熟。）此外，尽管某些包可能具有所需的功能，但它们需要大量其他包的支持，并可能会引入依赖冲突。考虑在使用r-base镜像构建的docker容器中运行下面的命令：

```py
RUN R -e "install.packages('recipes', dependencies=TRUE, repos='https://cran.rstudio.com/')"
```

它安装时间非常长，而且占用了你容器镜像的大量空间。在这种情况下，我们的解决方案是——不使用像`recipes`这样预构建包中的函数——而是引入我们自己实现的简单函数，使用`data.table`包：

```py
library(data.table)

OneHotEncoder <- function() {
  # Local variables
  categories <- list()

  # Method to fit data and extract categories
  fit <- function(dt, columns) {
    for (column in columns) {
      categories[[column]] <<- unique(dt[[column]])
    }
  }

  # Method to turn columns into factors and 
  factorize <- function(dt) {
    for (column_name in names(categories)) {
        set(dt, j = column_name, 
        value = factor(dt[[column_name]], 
        levels = categories[[column_name]]))
    }
    return(dt)
  }

  # Method to transform columns in categories list to 
  # dummy variables
  transform <- function(dt) {
    dt = factorize(dt)
    # add row number for joins later
    dt[, rn := .I]
    for (col in names(categories)) {
      print(col)
      # Construct the formula dynamically
      formula_str <- paste("~", col, "- 1")
      formula_obj <- as.formula(formula_str)
      # Create a model model.matrix object
      mm = model.matrix(formula_obj, dt)
      mm_dt <- as.data.table(mm, keep.rownames = "rn")
      mm_dt[, rn := as.integer(rn)]

      # Perform a merge based on these row numbers
      dt <- merge(dt, mm_dt, by = "rn", all = TRUE)

       # remove the original column
      dt[, (col) := NULL]

      # set any new NAs to 0
      for (ncol in names(mm_dt)) {
        set(dt, which(is.na(dt[[ncol]])), ncol, 0)
      }
    }
    dt[, rn := NULL]
    return(dt)
  }

  # Method to get categories
  get_categories <- function() {
    return(categories)
  }

  # Return a list of methods
  list(
    get_categories = get_categories,
    fit = fit,
    transform = transform
  )
}
```

让我们仔细研究这个函数，看看它在我们的训练和推断数据集上是如何工作的。（R 与 Python 稍有不同，我们不使用类，而是使用一个父函数，其工作方式类似。）

首先，我们需要创建一个函数的实例：

```py
 encoder = OneHotEncoder()
```

然后，就像`sklearn.preprocessing`中的`OneHotEncoder`类一样，我们的`OneHotEncoder`中也有一个适配函数。 我们在训练数据上使用适配函数，提供训练数据集和我们想要进行独热编码的列。

```py
# Columns to one-hot encode
fit_columns = c("color_1_", "color_2")
# Use the fit method
encoder$fit(dt=training_data, columns=fit_columns)
```

适配函数简单地循环遍历我们想要用于训练的所有列，并找到每个列包含的所有唯一值。 然后，在转换函数中使用这些列及其潜在值。 现在我们有一个已安装的独热编码器函数实例，我们可以使用 R 的`.RDS`文件将其保存以备将来使用。

```py
saveRDS(encoder, "~/my_encoder.RDS")
```

为了生成我们需要用于训练的独热编码数据集，我们在训练数据上运行转换函数：

```py
transformed_training_data = encoder$transform(training_data)
```

转换函数比拟拟适配函数稍微复杂一些，它首先将提供的列转换为因子 — 使用列的原始唯一值作为因子水平。 然后，我们循环遍历每个预测列，并创建数据的`model.matrix`对象。 然后，将这些对象添加回原始数据集，并删除原始因子列。 我们还确保将任何缺失值设置为 0。

现在我们得到了与之前完全相同的数据集：

![](../Images/f305e941d89808c5f5ac9eb6977727f2.png)

使用 R 算法转换的训练数据集 / 作者提供的图片

最后，当我们需要对我们的推断数据集进行独热编码时，我们在该数据集上运行编码器函数的相同实例：

```py
transformed_inference_data = encoder$transform(inference_data)
```

该过程确保我们在`transformed_inference_data`中有与`transformed_training_data`中相同的列。

# 进一步考虑

在我们总结之前，有一些额外的考虑事项需要提及。 与机器学习中的许多其他事物一样，关于何时以及如何使用特定技术并没有总是一个简单的答案。 尽管它显然可以缓解一些问题，但在进行独热编码时也可能出现新问题。 最常见的问题与如何处理高基数分类变量以及由于增加表格大小而导致的内存问题有关。

此外，还有替代编码技术，如标签编码、嵌入或目标编码，有时可能更适合于独热编码。

每个主题都足够丰富，值得撰写一篇专门的文章，因此我们将这些内容留给有兴趣进一步探索的读者。

# 结论

我们已经展示了如何错误地使用 one-hot 编码技术可能导致推理数据中的错误和问题，也展示了如何通过 Python 和 R 来减轻和解决这些问题。如果不解决，one-hot 编码的管理不当可能会导致崩溃和推理问题，因此强烈建议使用更稳健的技术——例如 sklearn 的 `OneHotEncoder` 或我们开发的 R 函数。

感谢阅读！

*文中展示并使用的所有代码可以在以下 GitHub 仓库找到：* [*https://github.com/hcekne/robust_one_hot_encoding*](https://github.com/hcekne/robust_one_hot_encoding)

*如果你喜欢阅读这篇文章并希望访问更多我的内容，欢迎通过 LinkedIn 与我联系，链接为* [*https://www.linkedin.com/in/hans-christian-ekne-1760a259/*](https://www.linkedin.com/in/hans-christian-ekne-1760a259/) *，或者访问我的个人网站* [*https://www.ekneconsulting.com/*](https://www.ekneconsulting.com/) *，了解我提供的一些服务。如有任何疑问，请通过电子邮件 hce@ekneconsulting.com 随时与我联系。*
