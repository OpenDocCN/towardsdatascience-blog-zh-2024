# 更快的 DataFrame 序列化

> 原文：[https://towardsdatascience.com/faster-dataframe-serialization-75205b6b7c69?source=collection_archive---------3-----------------------#2024-02-03](https://towardsdatascience.com/faster-dataframe-serialization-75205b6b7c69?source=collection_archive---------3-----------------------#2024-02-03)

## 使用 StaticFrame NPZ 格式读取和写入 DataFrame，速度比 Parquet 快多达十倍

[](https://medium.com/@flexatone?source=post_page---byline--75205b6b7c69--------------------------------)[![Christopher Ariza](../Images/35208ace15080724e4cd6690e43d6502.png)](https://medium.com/@flexatone?source=post_page---byline--75205b6b7c69--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--75205b6b7c69--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--75205b6b7c69--------------------------------) [Christopher Ariza](https://medium.com/@flexatone?source=post_page---byline--75205b6b7c69--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75205b6b7c69--------------------------------) ·9分钟阅读·2024年2月3日

--

![](../Images/d871807aea2d8d30a15f56c423e23e87.png)

作者提供的照片

Apache Parquet 格式提供了一种高效的列式数据表的二进制表示，如在 Apache Hadoop 和 Spark、AWS Athena 和 Glue 以及 Pandas DataFrame 序列化中的广泛使用所见。尽管 Parquet 提供了广泛的互操作性，并且其性能优于文本格式（如 CSV 或 JSON），但它的速度比 NPZ 慢多达十倍，后者是[StaticFrame](https://github.com/static-frame/static-frame)中引入的另一种 DataFrame 序列化格式。

StaticFrame（我参与编写的开源 DataFrame 库）基于 NumPy NPY 和 NPZ 格式对 DataFrame 进行编码。NPY 格式（数组数据的二进制编码）和 NPZ 格式（NPY 文件的压缩包）在 2007 年的[NumPy 增强提案](https://numpy.org/neps/nep-0001-npy-format.html)中有定义。通过扩展 NPZ 格式并使用专门的 JSON 元数据，StaticFrame 提供了一个完整的 DataFrame 序列化格式，支持所有 NumPy 数据类型。

本文扩展了在[PyCon USA 2022](https://youtu.be/HLH5AwF-jx4?si=9NSpPuf-jVoxotzg)首次展示的工作，加入了更多的性能优化和更广泛的基准测试。

# 序列化 DataFrame 的挑战

DataFrame 不仅仅是具有字符串列标签的列式数据集合，如关系型数据库中的数据那样。除了列式数据，DataFrame 还包含带标签的行和列，并且这些行和列标签可以是任何类型（或者具有层次标签时，可以是多种类型）。此外，常常会将元数据与 `name` 属性一起存储，可能存储在 DataFrame 上或轴标签上。

由于 Parquet 最初是为了存储列式数据集而设计的，因此它不能直接支持完整的 DataFrame 特性。Pandas 通过在 Parquet 文件中添加 JSON 元数据提供了这些附加信息。

此外，Parquet 支持的类型选择非常有限；NumPy 数据类型的完整范围并不直接支持。例如，Parquet 原生不支持无符号整数或任何日期类型。

虽然 Python 的 pickle 能够高效地序列化 DataFrame 和 NumPy 数组，但它们仅适用于来自可信来源的短期缓存。尽管 pickle 快速，但由于代码变更，它们可能会失效，而且从不可信来源加载时不安全。

Parquet 的另一个替代品来源于 Arrow 项目，是[Feather](https://arrow.apache.org/docs/python/feather.html)。尽管 Feather 支持所有 Arrow 类型，并且在读取 DataFrame 时比 Parquet 更快，但其速度仍然比 NPZ 慢至少两倍。

Parquet 和 Feather 支持压缩以减小文件大小。Parquet 默认使用“snappy”压缩，而 Feather 默认使用“lz4”。由于 NPZ 格式优先考虑性能，因此尚不支持压缩。如下面所示，NPZ 在性能上显著优于压缩和未压缩的 Parquet 文件。

# DataFrame 序列化性能比较

许多出版物通过仅测试一两个数据集来提供 DataFrame 基准测试。例如，[McKinney 和 Richardson](https://ursalabs.org/blog/2020-feather-v2)（2020）就以 Fannie Mae 贷款表现数据和纽约市黄出租车出行数据为例，概括其性能。这些特有的数据集不足以充分代表性能，因为 DataFrame 的形状和列类型异质性的程度会显著影响性能。

为了避免这种不足，我使用九个合成数据集进行性能比较。这些数据集在两个维度上有所变化：形状（高、方形和宽）和列异质性（列式、混合型和均匀型）。形状变化改变了元素在高（例如：10,000行和100列）、方形（例如：1,000行和列）和宽（例如：100行和10,000列）几何体之间的分布。列异质性变化改变了列之间类型的多样性，列式（没有相邻列具有相同类型）、混合型（部分相邻列具有相同类型）和均匀型（所有列具有相同类型）。

`[frame-fixtures](https://github.com/static-frame/frame-fixtures)`库定义了一种领域特定语言，用于创建可预测的、随机生成的 DataFrame 以供测试；这九个数据集是通过此工具生成的。

为了展示一些静态框架（StaticFrame）和 Pandas 接口的评估，以下 IPython 会话使用`%time`执行基本性能测试。如下面所示，方形、均匀类型的 DataFrame 可以通过 NPZ 格式读写，速度是未压缩 Parquet 的许多倍。

```py
>>> import numpy as np
>>> import static_frame as sf
>>> import pandas as pd

>>> # an square, uniform float array
>>> array = np.random.random_sample((10_000, 10_000))

>>> # write peformance
>>> f1 = sf.Frame(array)
>>> %time f1.to_npz('/tmp/frame.npz')
CPU times: user 710 ms, sys: 396 ms, total: 1.11 s
Wall time: 1.11 s

>>> df1 = pd.DataFrame(array)
>>> %time df1.to_parquet('/tmp/df.parquet', compression=None)
CPU times: user 6.82 s, sys: 900 ms, total: 7.72 s
Wall time: 7.74 s

>>> # read performance
>>> %time f2 = f1.from_npz('/tmp/frame.npz')
CPU times: user 2.77 ms, sys: 163 ms, total: 166 ms
Wall time: 165 ms

>>> %time df2 = pd.read_parquet('/tmp/df.parquet')
CPU times: user 2.55 s, sys: 1.2 s, total: 3.75 s
Wall time: 866 ms
```

以下提供的性能测试通过使用`frame-fixtures`对形状和类型异质性进行系统性变化，并对十次迭代结果进行平均，从而扩展了这一基本方法。虽然硬件配置会影响性能，但在不同机器和操作系统之间，相对特征保持一致。对于所有接口，使用默认参数，除非需要禁用压缩。用于执行这些测试的代码可在[GitHub](https://github.com/static-frame/static-frame/blob/master/doc/source/articles/serialize.py)上找到。

## 读取性能

由于数据通常被读取的频率高于写入，因此读取性能是优先考虑的。如图所示，对于所有包含百万级（1e+06）元素的九个DataFrame，NPZ在每个测试条件下的表现均显著优于Parquet和Feather。NPZ的读取性能比压缩后的Parquet快十倍以上。例如，在Uniform Tall测试条件下，压缩Parquet的读取时间为21毫秒，而NPZ为1.5毫秒。

下图显示了处理时间，其中较低的柱状图表示较快的性能。

![](../Images/fa38e9a0015f38c3dbe5f57596bec09d.png)

这一令人印象深刻的NPZ性能在扩展时依然得以保持。当数据量增加到1亿（1e+08）元素时，NPZ的表现仍然至少是Parquet和Feather的两倍，无论是否使用压缩。

![](../Images/9ec4be64facd1cb484fc3d7d0720a932.png)

## 写入性能

在将DataFrame写入磁盘时，NPZ在所有场景下都优于Parquet（无论是否压缩）。例如，在Uniform Square测试条件下，压缩后的Parquet写入时间为200毫秒，而NPZ为18.3毫秒。NPZ的写入性能通常与未压缩的Feather相当：在某些场景下NPZ更快，而在其他场景下Feather更快。

![](../Images/2a1401f5d019627ec1b4737267552649.png)

与读取性能相似，NPZ写入性能在规模扩展时也得以保持。当数据量增加到1亿（1e+08）元素时，NPZ的性能仍然是Parquet的至少两倍，无论是否使用压缩。

![](../Images/e1a0f86520284c384a95f1f21eaade24.png)

## 特殊性能

作为额外参考，我们还将基准测试相同的NYC黄出租车行程数据（来自2010年1月），该数据集用于[McKinney和Richardson](https://ursalabs.org/blog/2020-feather-v2)（2020）。该数据集包含近3亿（3e+08）个元素，存储在一个包含14,863,778行和19列的高大异质类型DataFrame中。

NPZ读取性能比Parquet和Feather快约四倍（无论是否压缩）。尽管NPZ的写入性能比Parquet快，但Feather写入在这里是最快的。

![](../Images/38dddbd6981acf48b401d4b1a43a010a.png)

## 文件大小

如下所示，对于100万（1e+06）元素和1亿（1e+08）元素的数据框架，未压缩的NPZ文件在磁盘上的大小通常与未压缩的Feather文件相等，并且始终小于未压缩的Parquet文件（有时也小于压缩的Parquet文件）。由于压缩对Parquet和Feather文件的大小减小效果有限，未压缩NPZ在速度上的优势可能会轻易超过其较大文件大小的成本。

![](../Images/8a39a64787084a7f31e46a1ce1428974.png)![](../Images/251704e219f51e212a421110d391ccdd.png)

# 序列化数据框架

StaticFrame将数据存储为1D和2D NumPy数组的集合。数组表示列值，以及可变深度的索引和列标签。除了NumPy数组外，还需要关于组件类型（即用于索引和列的Python类）以及组件`name`属性的信息，以完整地重建一个`Frame`。完全序列化一个数据框架需要将这些组件写入并从文件中读取。

数据框架（DataFrame）的组件可以通过以下图示表示，该图示隔离了数组、数组类型、组件类型和组件名称。此图示将用于演示NPZ如何编码一个数据框架。

![](../Images/bed0ae57980fff32c000e1e2fd2c8e65.png)

该图示的组件映射到Python中`Frame`字符串表示的组件。例如，给定一个包含整数和布尔值的`Frame`，且索引和列上都有层次标签（可以通过StaticFrame的`WWW`接口从GitHub下载），StaticFrame提供以下字符串表示：

```py
>>> frame = sf.Frame.from_npz(sf.WWW.from_file('https://github.com/static-frame/static-frame/raw/master/doc/source/articles/serialize/frame.npz', encoding=None))
>>> frame
<Frame: p>
<IndexHierarchy: q>       data    data    data    valid  <<U5>
                          A       B       C       *      <<U1>
<IndexHierarchy: r>
2012-03             x     5       4       7       False
2012-03             y     9       1       8       True
2012-04             x     3       6       2       True
<datetime64[M]>     <<U1> <int64> <int64> <int64> <bool>
```

字符串表示的组件可以通过颜色映射到数据框架图示：

![](../Images/9ce32383fa2f790683cb5bd0eca310d8.png)

## 编码一个NPY数组

一个NPY文件将NumPy数组存储为二进制文件，包含六个组件：（1）一个“魔术”前缀，（2）一个版本号，（3）一个头部长度，（4）头部（头部是Python字典的字符串表示），以及（5）填充和（6）原始数组字节数据。以下显示的是存储在名为“__blocks_1__.npy”文件中的三元素二进制数组的这些组件。

![](../Images/11621b73ed36bf9de3057e479f0b6fec.png)

给定一个名为“frame.npz”的NPZ文件，我们可以通过使用标准库的`ZipFile`从NPZ中读取NPY文件来提取二进制数据。

```py
>>> from zipfile import ZipFile
>>> with ZipFile('/tmp/frame.npz') as zf: print(zf.open('__blocks_1__.npy').read())
b'\x93NUMPY\x01\x006\x00{"descr":"|b1","fortran_order":True,"shape":(3,)}    \n\x00\x01\x01
```

由于NPY在NumPy中得到了很好的支持，可以使用`np.load()`函数将此文件转换为NumPy数组。这意味着，StaticFrame NPZ中的底层数组数据可以通过其他读取器轻松提取。

```py
>>> with ZipFile('/tmp/frame.npz') as zf: print(repr(np.load(zf.open('__blocks_1__.npy'))))
array([False,  True,  True])
```

由于NPY文件可以编码任何数组，因此可以从连续字节数据中加载大型二维数组，在StaticFrame中，当多个连续列由单个数组表示时，提供了卓越的性能。

## 构建NPZ文件

StaticFrame NPZ是一个标准的未压缩ZIP文件，其中包含NPY文件中的数组数据，以及包含组件类型和名称的JSON文件中的元数据。

给定上面`Frame`的NPZ文件，我们可以使用`ZipFile`列出其内容。该压缩包包含六个NPY文件和一个JSON文件。

```py
>>> with ZipFile('/tmp/frame.npz') as zf: print(zf.namelist())
['__values_index_0__.npy', '__values_index_1__.npy', '__values_columns_0__.npy', '__values_columns_1__.npy', '__blocks_0__.npy', '__blocks_1__.npy', '__meta__.json']
```

下图将这些文件映射到DataFrame图的组件。

![](../Images/a0e167bf14a88dd3b534e7dfd476a4ea.png)

StaticFrame扩展了NPZ格式，包含一个JSON文件作为元数据。该文件定义了名称属性、组件类型和深度计数。

```py
>>> with ZipFile('/tmp/frame.npz') as zf: print(zf.open('__meta__.json').read())
b'{"__names__": ["p", "r", "q"], "__types__": ["IndexHierarchy", "IndexHierarchy"], "__types_index__": ["IndexYearMonth", "Index"], "__types_columns__": ["Index", "Index"], "__depths__": [2, 2, 2]}'
```

在下图中，`__meta__.json`文件的组件被映射到DataFrame图的组件。

![](../Images/3cd9d8ba88640f490bd47f5e2c8f1854.png)

作为一个简单的ZIP文件，提取StaticFrame NPZ内容的工具非常普遍。另一方面，由于ZIP格式的历史和广泛特性，它会带来性能开销。StaticFrame实现了一个为NPZ使用优化的自定义ZIP读取器，这有助于NPZ的出色读取性能。

# 结论

DataFrame序列化的性能对许多应用至关重要。虽然Parquet得到了广泛支持，但它的通用性牺牲了类型特异性和性能。StaticFrame NPZ可以比Parquet快最多十倍地读取和写入DataFrame，无论是否压缩，文件大小相似（或仅略大）。虽然Feather是一个有吸引力的替代方案，但NPZ的读取性能通常是Feather的两倍。如果数据I/O成为瓶颈（而这通常是），StaticFrame NPZ提供了解决方案。
