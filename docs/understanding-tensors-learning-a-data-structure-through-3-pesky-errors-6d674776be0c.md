# 理解张量：通过 3 个令人头疼的错误学习数据结构

> 原文：[https://towardsdatascience.com/understanding-tensors-learning-a-data-structure-through-3-pesky-errors-6d674776be0c?source=collection_archive---------4-----------------------#2024-03-13](https://towardsdatascience.com/understanding-tensors-learning-a-data-structure-through-3-pesky-errors-6d674776be0c?source=collection_archive---------4-----------------------#2024-03-13)

## 通过解决 TensorFlow 错误，我学到的关于张量的知识

[](https://medium.com/@erevear?source=post_page---byline--6d674776be0c--------------------------------)[![Eva Revear](../Images/675266fccb503690d50d83b8c92f48b8.png)](https://medium.com/@erevear?source=post_page---byline--6d674776be0c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6d674776be0c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6d674776be0c--------------------------------) [Eva Revear](https://medium.com/@erevear?source=post_page---byline--6d674776be0c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6d674776be0c--------------------------------) ·11分钟阅读·2024年3月13日

--

![](../Images/feff5a593eac65c3656a2db9bd90c879.png)

图片来源：[Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

最近我一直在 TensorFlow 中尝试深度学习模型，因此也开始学习如何将数据管理为张量。

作为一名数据工程师，整天工作在可以轻松切片、切块并可视化的表格中，我对张量的操作几乎没有任何直觉，并且我似乎总是遇到相同的错误，尤其是在开始时，这些错误让我一头雾水。

然而，深入研究这些错误让我学到了很多关于张量和 TensorFlow 的知识，我想在这里整理这些学习成果，以便作为参考。

如果你有喜欢的错误、解决方案或调试技巧，请留下评论！

# 调试的代码示例

在我们深入探讨这些错误之前，我想记录一些我在调试过程中发现的轻量级、简单的代码片段，它们对我很有帮助。（虽然必须声明的是，出于法律原因，我们当然总是使用官方的调试工具来调试，而不是仅仅依赖于打印语句 🙂）

## **查看我们 TensorFlow 数据集的内部**

首先，看看我们的实际数据。当我们打印 DataFrame 或在 SQL 中执行 SELECT * 时，我们能看到数据！但当我们打印张量数据集时，我们看到的是……

```py
<_TensorSliceDataset element_spec=(TensorSpec(shape=(2, 3), dtype=tf.int32, name=None), TensorSpec(shape=(1, 1), dtype=tf.int32, name=None))>
```

这些都是非常有用的信息，但它们并没有帮助我们理解数据中到底发生了什么。

要打印执行图中的单个张量，我们可以利用tf.print。如果你打算经常使用它，我强烈推荐这篇深入探讨tf.print的文章：[在TensorFlow中使用tf.Print()](https://wiki.example.org/feynmans_learning_method)。

但在开发过程中使用TensorFlow数据集时，有时我们需要一次查看几个值。为此，我们可以像这样循环并打印单个数据片段：

```py
 # Generate dummy 2D data
np.random.seed(42)
num_samples = 100
num_features = 5
X_data = np.random.rand(num_samples, num_features).astype(np.float32)
y_data = 2 * X_data[:, 0] + 3 * X_data[:, 1] - 1.5 * X_data[:, 2] + 0.5 * X_data[:, 3] + np.random.randn(num_samples)

# Turn it into a Tensorflow Dataset
dataset = tf.data.Dataset.from_tensor_slices((X_data, y_data))

# Print the first 10 rows
for i, (features, label) in enumerate(dataset.take(10)):
  print(f"Row {i + 1}: Features - {features.numpy()}, Label - {label.numpy()}")
```

我们也可以使用skip跳到特定的索引：

```py
mini_dataset = dataset.skip(100).take(20)
for i, (features, label) in enumerate(mini_dataset):
print(f"Row {i + 1}: Features - {features.numpy()}, Label - {label.numpy()}")
```

## **了解我们的张量规格**

在处理张量时，我们还需要知道它们的形状、秩、维度和数据类型（如果这些词汇对你来说有些陌生，就像最初对我来说一样，别担心，稍后我们会回到这些内容）。无论如何，下面是一些代码行，用于收集这些信息：

```py
 # Create a sample tensor
sample_tensor = tf.constant([[1, 2, 3], [4, 5, 6]])

# Get the size of the tensor (total number of elements)
tensor_size = tf.size(sample_tensor).numpy()

# Get the rank of the tensor
tensor_rank = tf.rank(sample_tensor).numpy()

# Get the shape of the tensor
tensor_shape = sample_tensor.shape

# Get the dimensions of the tensor
tensor_dimensions = sample_tensor.shape.as_list()
# Print the results
print("Tensor Size:", tensor_size)
print("Tensor Rank:", tensor_rank)
print("Tensor Shape:", tensor_shape)
print("Tensor Dimensions:", tensor_dimensions)
```

上面的输出：

```py
Tensor Size: 6
Tensor Rank: 2
Tensor Shape: (2, 3)
Tensor Dimensions: [2, 3]
```

## **增强model.summary()**

最后，能够看到数据如何在模型中流动，以及在各层之间输入输出的形状如何变化，总是很有帮助。许多错误的根源通常是输入和输出形状之间的不匹配，以及给定张量的形状。

[model.summary()](https://www.geeksforgeeks.org/tensorflow-js-tf-layersmodel-class-summary-method/) 当然可以完成这项工作，但我们可以通过以下代码片段补充一些信息，这为模型和层的输入输出提供了更多上下文：

```py
print("###################Input Shape and Datatype#####################")
[print(i.shape, i.dtype) for i in model.inputs]
print("###################Output Shape and Datatype#####################")
[print(o.shape, o.dtype) for o in model.outputs]
print("###################Layer Input Shape and Datatype#####################")
[print(l.name, l.input, l.dtype) for l in model.layers]
```

# 从我们的错误中学习

那么，让我们来看看一些错误吧！

## 秩

> ValueError: 形状必须是秩x，但却是秩y……

好的，首先，什么是秩？秩就是我们用来描述张量的维度单位。秩为0的张量是一个标量值；秩为1的张量是一个向量；秩为2的张量是一个矩阵，依此类推，对于所有n维结构。

以一个五维张量为例。

```py
rank_5_tensor = tf.constant([[[[[1, 2], [3, 4]], [[5, 6], [7, 8]]], [[[9, 10], [11, 12]], [[13, 14], [15, 16]]]],
[[[[17, 18], [19, 20]], [[21, 22], [23, 24]]], [[[25, 26], [27, 28]], [[29, 30], [31, 32]]]]])
print("\nRank 5 Tensor:", rank_5_tensor.shape)
```

```py
Rank 5 Tensor: (2, 2, 2, 2, 2)
```

上面的代码显示，每个维度的大小为2。如果我们想索引它，可以沿着这些轴中的任何一个进行。为了获取最后一个元素32，我们可以运行类似以下的代码：

```py
rank_5_tensor.numpy()[1][1][1][1][1]
```

[官方张量文档](https://www.tensorflow.org/guide/tensor#basics)中有一些非常有用的可视化，能够帮助你更容易地理解这一点。

回到错误：它只是标记了提供的张量与某个特定函数预期的维度不同。例如，如果错误声明“形状必须是秩1，但却是秩0…”，这意味着我们提供的是一个标量值，而它期望的是一个一维张量。

下面是一个示例，我们试图使用matmul方法将张量相乘。

```py
import tensorflow as tf
import numpy as np
# Create a TensorFlow dataset with random matrices
num_samples = 5
matrix_size = 3
dataset = tf.data.Dataset.from_tensor_slices(np.random.rand(num_samples, matrix_size, matrix_size))
mul = [1,2,3,4,5,6]

# Define a function that uses tf.matmul
def matmul_function(matrix):
  return tf.matmul(matrix, mul)

# Apply the matmul_function to the dataset using map
result_dataset = dataset.map(matmul_function)
```

如果我们查看[文档](https://www.tensorflow.org/api_docs/python/tf/linalg/matmul)，matmul函数至少需要一个秩为2的张量，因此将矩阵与[1,2,3,4,5,6]（这只是一个数组）相乘会引发此错误。

```py
ValueError: Shape must be rank 2 but is rank 1 for '{{node MatMul}} = MatMul[T=DT_DOUBLE, transpose_a=false, transpose_b=false](args_0, MatMul/b)' with input shapes: [3,3], [2].
```

解决这个错误的一个好方法是深入文档，理解你正在使用的函数期望什么（这里有一个关于张量的函数列表：[raw_ops](https://www.tensorflow.org/api_docs/python/tf/raw_ops)）。

然后使用秩方法来确定我们实际上提供了什么。

```py
print(tf.rank(mul))
```

```py
tf.Tensor(1, shape=(), dtype=int32)
```

至于解决方法，tf.reshape通常是一个不错的起点。让我们稍微谈一下tf.reshape，因为它将是我们整个Tensorflow旅程中的忠实伙伴：[tf.reshape(tensor, shape, name=None)](https://www.tensorflow.org/api_docs/python/tf/reshape)

reshape简单地接受我们想要重塑的张量，和另一个包含我们希望输出形状的张量。例如，让我们重塑我们的乘法输入：

```py
mul = [1,2,3,4,5,6]
tf.reshape(mul, [3, 2]).numpy()
```

```py
array([[1, 2],
       [3, 4],
       [5, 6]], dtype=int32)
```

我们的变量将变成一个(3,2)的张量（3行，2列）。快速提示，tf.reshape(t, [3, -1]).numpy()也会产生相同的结果，因为-1告诉Tensorflow计算该维度的大小，以确保总大小保持不变。形状张量中的元素数量即为秩。

一旦我们创建了具有正确秩的张量，我们的乘法就能顺利进行！

## 形状

> ValueError：层的输入与层不兼容……

对张量形状的直观理解，以及它如何在模型各层之间相互作用和变化，已经使得深度学习的生活变得更加轻松。

首先，先了解基本的词汇：张量的形状指的是沿张量每个维度或轴的元素数量。例如，一个3行4列的二维张量，其形状为(3, 4)。

那么，形状可能会出什么问题呢？很高兴你问了，问题可多了！

首先，训练数据的形状和秩必须与输入层期望的输入形状匹配。让我们看一个例子，一个基本的CNN：

```py
import tensorflow as tf
from tensorflow.keras import layers, models

# Create a function to generate sample data
def generate_sample_data(num_samples=100):
  for _ in range(num_samples):
  features = tf.random.normal(shape=(64, 64, 3))
  labels = tf.one_hot(tf.random.uniform(shape=(), maxval=10, dtype=tf.int32), depth=10)
  yield features, labels

# Create a TensorFlow dataset using the generator function
sample_dataset = tf.data.Dataset.from_generator(generate_sample_data, output_signature=(tf.TensorSpec(shape=(64, 64, 3), dtype=tf.float32), tf.TensorSpec(shape=(10,), dtype=tf.float32)))

# Create a CNN model with an input layer expecting (128, 128, 3)
model = models.Sequential()
model.add(layers.Conv2D(32, (3, 3), activation='relu', input_shape=(128, 128, 3)))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
model.add(layers.MaxPooling2D((2, 2)))
model.add(layers.Conv2D(64, (3, 3), activation='relu'))
model.add(layers.Flatten())
model.add(layers.Dense(64, activation='relu'))
model.add(layers.Dense(10, activation='softmax'))

# Compile the model
model.compile(optimizer='adam', loss='categorical_crossentropy', metrics=['accuracy'])
# Fit the model using the dataset
model.fit(sample_dataset.batch(32).repeat(), epochs=5, steps_per_epoch=100, validation_steps=20)
```

尝试运行上述代码将会导致：

```py
ValueError: Input 0 of layer "sequential_5" is incompatible with the layer: expected shape=(None, 128, 128, 3), found shape=(None, 64, 64, 3)
```

这是因为我们的模型期望输入张量的形状为(128, 128, 3)，而我们生成的数据是(64, 64, 3)。

在这种情况下，我们的好朋友reshape，或者Tensorflow的另一个函数resize，可以帮助我们。如果像上面那样我们在处理图像，我们可以简单地运行resize，或者改变模型输入的期望：

```py
def resize_image(image, label):
  resized_image = tf.image.resize(image, size=target_shape)
  return resized_image, label

# Apply the resize function to the entire dataset
resized_dataset = sample_dataset.map(resize_image)
```

在这种情况下，了解常见类型的模型和模型层如何期望不同形状的输入是有帮助的，所以让我们稍微绕个弯。

深度神经网络的全连接层接受的是一维张量（或者是二维的，取决于是否包括批次大小，不过我们稍后会讨论批次大小），格式为(feature_size, )，其中feature_size是每个样本的特征数量。

卷积神经网络接受表示图像的数据，使用三维张量（宽度，高度，通道），其中通道是颜色方案，1表示灰度，3表示RGB。

最后，递归神经网络（如LTSM）接受的是二维张量（时间步，特征大小）。

但回到错误！Tensorflow形状错误的另一个常见原因与数据通过模型层时形状的变化有关。如前所述，不同的层接受不同形状的输入，并且它们也可以重新调整输出的形状。

返回到我们上面的CNN示例，让我们再次破坏它，看看当我们移除Flatten层时会发生什么。如果我们尝试运行代码，我们将看到

```py
ValueError: Shapes (None, 10) and (None, 28, 28, 10) are incompatible
```

这就是打印我们所有模型输入和输出形状以及数据形状变得非常有用的地方，帮助我们定位不匹配的地方。

model.summary()将展示给我们

```py
Layer (type) Output Shape Param #
=================================================================
conv2d_15 (Conv2D) (None, 126, 126, 32) 896
max_pooling2d_10 (MaxPooli (None, 63, 63, 32) 0
ng2D)
conv2d_16 (Conv2D) (None, 61, 61, 64) 18496
max_pooling2d_11 (MaxPooling2D) (None, 30, 30, 64) 0
conv2d_17 (Conv2D) (None, 28, 28, 64) 36928
flatten_5 (Flatten) (None, 50176) 0
dense_13 (Dense) (None, 64) 3211328
dense_14 (Dense) (None, 10) 650
=================================================================
Total params: 3268298 (12.47 MB)
Trainable params: 3268298 (12.47 MB)
Non-trainable params: 0 (0.00 Byte)
```

我们进一步的诊断将揭示

```py
###################Input Shape and Datatype#####################
(None, 128, 128, 3) <dtype: 'float32'>
###################Output Shape and Datatype#####################
(None, 10) <dtype: 'float32'>
###################Layer Input Shape and Datatype#####################
conv2d_15 KerasTensor(type_spec=TensorSpec(shape=(None, 128, 128, 3), dtype=tf.float32, name='conv2d_15_input'), name='conv2d_15_input', description="created by layer 'conv2d_15_input'") float32
max_pooling2d_10 KerasTensor(type_spec=TensorSpec(shape=(None, 126, 126, 32), dtype=tf.float32, name=None), name='conv2d_15/Relu:0', description="created by layer 'conv2d_15'") float32
conv2d_16 KerasTensor(type_spec=TensorSpec(shape=(None, 63, 63, 32), dtype=tf.float32, name=None), name='max_pooling2d_10/MaxPool:0', description="created by layer 'max_pooling2d_10'") float32
max_pooling2d_11 KerasTensor(type_spec=TensorSpec(shape=(None, 61, 61, 64), dtype=tf.float32, name=None), name='conv2d_16/Relu:0', description="created by layer 'conv2d_16'") float32
conv2d_17 KerasTensor(type_spec=TensorSpec(shape=(None, 30, 30, 64), dtype=tf.float32, name=None), name='max_pooling2d_11/MaxPool:0', description="created by layer 'max_pooling2d_11'") float32
flatten_5 KerasTensor(type_spec=TensorSpec(shape=(None, 28, 28, 64), dtype=tf.float32, name=None), name='conv2d_17/Relu:0', description="created by layer 'conv2d_17'") float32
dense_13 KerasTensor(type_spec=TensorSpec(shape=(None, 50176), dtype=tf.float32, name=None), name='flatten_5/Reshape:0', description="created by layer 'flatten_5'") float32
dense_14 KerasTensor(type_spec=TensorSpec(shape=(None, 64), dtype=tf.float32, name=None), name='dense_13/Relu:0', description="created by layer 'dense_13'") float32
```

输出信息很多，但我们可以看到dense_13层正在寻找形状为(None, 50176)的输入。然而，conv2d_17层输出的是(None, 28, 28, 64)。

Flatten层将前面层的多维输出转换为Dense层期望的一维（平坦）向量。

Conv2d和Max Pooling层也以其他有趣的方式改变它们的输入数据，但这些超出了本文的范围。要了解更多详细信息，可以查看：[神经网络中输入形状和模型复杂度的终极指南](/ultimate-guide-to-input-shape-and-model-complexity-in-neural-networks-ae665c728f4b)

那批量大小呢？！我没有忘记！

如果我们再一次通过从model.fit中的数据集移除.batch(32)来破坏我们的代码，我们将得到如下错误：

```py
ValueError: Input 0 of layer "sequential_10" is incompatible with the layer: expected shape=(None, 128, 128, 3), found shape=(128, 128, 3)
```

这是因为，层输入的第一个维度是为批量大小或我们希望模型一次处理的样本数保留的。要深入了解，可以阅读[批量和轮次之间的区别](https://machinelearningmastery.com/difference-between-a-batch-and-an-epoch/)。

批量大小在拟合之前默认为None，正如我们在模型摘要输出中看到的那样，我们的模型期望我们在其他地方设置它，具体取决于我们如何调整超参数。我们也可以通过使用batch_input_size而不是input_size在输入层强制设置它，但这样会降低我们在测试不同值时的灵活性。

## 类型

> TypeError: 无法将类型的对象转换为张量。不支持的对象类型。

最后，让我们简单谈谈Tensor中一些数据类型的具体细节。

上述错误是另一个，如果你习惯于在使用各种数据构建表格的数据库系统中工作，可能会让人有些困惑，但它是比较容易诊断和修复的，尽管有几个常见原因需要注意。

主要问题是，尽管张量支持多种[数据类型](https://www.tensorflow.org/api_docs/python/tf/dtypes)，但当我们将NumPy数组转换为张量时（这是深度学习中的常见流程），数据类型必须是浮动类型。下面的脚本初始化了一个包含None和字符串数据点的虚拟数据框。让我们一起分析这个示例中的问题并修复它：

```py
import numpy as np
import pandas as pd
import tensorflow as tf
from tensorflow.keras.layers import Dense
from tensorflow.keras.models import Sequential
data = [
  [None, 0.2, '0.3'],
  [0.1, None, '0.3'],
  [0.1, 0.2, '0.3'],
]
X_train = pd.DataFrame(data=data, columns=["x1", "x2", "x3"])
y_train = pd.DataFrame(data=[1, 0, 1], columns=["y"])

# Create a TensorFlow dataset
train_dataset = tf.data.Dataset.from_tensor_slices((X_train.to_numpy(), y_train.to_numpy()))
# Define the model
model = Sequential()
model.add(Dense(1, input_dim=X_train.shape[1], activation='sigmoid'))
model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
# Fit the model using the TensorFlow dataset
model.fit(train_dataset.batch(3), epochs=3)
```

运行这段代码会提醒我们：

```py
ValueError: Failed to convert a NumPy array to a Tensor (Unsupported object type float).
```

最明显的问题是你传入的是一个包含某些非浮动类型（即对象）的NumPy数组。如果你有一个实际的类别数据列，有很多方法可以将其转换为数值数据（如独热编码等），但这超出了本讨论的范围。

我们可以通过运行`print(X_train.dtypes)`来确定，这会告诉我们数据框中Tensorflow不喜欢的内容。

```py
x1 float64
x2 float64
x3 object
dtype: object
```

如果我们遇到非浮动数据点，下面这行代码将神奇地解决我们所有的问题：

```py
X_train = np.asarray(X_train).astype('float32')
```

另一件需要检查的事情是是否在任何地方出现了`None`或`np.nan`。

要找出这一点，我们可以使用几行代码，例如：

```py
null_mask = X_train.isnull().any(axis=1)
null_rows = X_train[null_mask]
print(null_rows)
```

这告诉我们，第0行和第1行中有空值：

```py
x1 x2 x3
0 NaN 0.2 0.3
1 0.1 NaN 0.3
```

如果是这样，并且这是预期中的/故意的，我们需要用一个可接受的替代值来替换这些值。`Fillna`可以在这里帮助我们。

```py
X_train.fillna(value=0, inplace=True)
```

通过下面对代码的这些修改，我们的NumPy数组将成功转换为张量数据集，我们可以训练我们的模型！

# 总结

我常常发现，当我不得不处理错误时，我对某项技术的理解最深，希望这对你也有所帮助！

如果你有一些有趣的技巧或Tensorflow错误，请分享给我！
