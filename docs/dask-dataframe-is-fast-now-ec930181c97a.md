# Dask DataFrame 现在非常快速

> 原文：[`towardsdatascience.com/dask-dataframe-is-fast-now-ec930181c97a?source=collection_archive---------4-----------------------#2024-05-27`](https://towardsdatascience.com/dask-dataframe-is-fast-now-ec930181c97a?source=collection_archive---------4-----------------------#2024-05-27)

## Dask 如何高效地在 TB 级别处理数据

[](https://medium.com/@patrick_hoefler?source=post_page---byline--ec930181c97a--------------------------------)![Patrick Hoefler](https://medium.com/@patrick_hoefler?source=post_page---byline--ec930181c97a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ec930181c97a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ec930181c97a--------------------------------) [Patrick Hoefler](https://medium.com/@patrick_hoefler?source=post_page---byline--ec930181c97a--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ec930181c97a--------------------------------) ·阅读时间 9 分钟·2024 年 5 月 27 日

--

![](img/308b0407b24987f46372d5b56e3cf07d.png)

`Performance Improvements for Dask DataFrames — 所有图片由作者创建`

## 介绍

Dask DataFrame 将 pandas DataFrame 扩展到 100GB-100TB 的规模进行操作。

从历史上看，Dask 相比于该领域的其他工具（如 Spark）要慢得多。通过专注于性能的多项改进，它现在非常快速（比以前快大约 20 倍）。新的实现使 Dask 从在每个基准测试中都被 Spark 完全碾压，变成了在 TPC-H 查询中经常大幅超越 Spark。

Dask DataFrame 的工作负载面临许多挑战。性能和内存使用常常是主要的痛点，数据集较大时洗牌操作不稳定，导致扩展变得困难。编写高效代码需要了解 Dask 的很多内部细节。

新的实现改变了这一切。那些无法正常工作的部分被完全从头重写，现有的实现也得到了改进。这为 Dask DataFrame 打下了坚实的基础，未来可以进行更快速的迭代。

我们将讨论三项最显著的变化，介绍它们如何提升性能并使 Dask 更易于使用，即使是对分布式计算较为陌生的用户。我们还将讨论未来改进的计划。

我是 Dask 核心团队的一员，还是[Coiled](https://www.coiled.io)的开源工程师，参与了本文中讨论的一些改进的实现。

## 1\. Apache Arrow 支持：高效的字符串数据类型

Dask DataFrame 由多个 pandas DataFrame 组成。历史上，pandas 使用 NumPy 处理数值数据，但对文本数据使用 Python 对象，这样做效率低下，并且会极大增加内存使用。对对象数据的操作也会占用 GIL（全局解释器锁），这对于 pandas 影响不大，但对于像 Dask 这样的并行系统来说，性能将受到灾难性的影响。

pandas 2.0 发布时引入了对通用 Arrow 数据类型的支持，因此 Dask 现在默认使用 PyArrow 支持的字符串。这些字符串*要好得多*。PyArrow 字符串将内存使用量减少了多达 80%，并且解锁了字符串操作的多线程功能。以前因内存不足而遇到问题的工作负载，现在能够在更少的内存中顺利运行，而且速度更快，因为它们不再不断将多余的数据溢写到磁盘。

![](img/d84c32d3800a76ea017400b5c0439f0a.png)

`与 Arrow 字符串相比，传统 DataFrame 的内存使用`

我写了一篇关于此内容的文章，详细[探讨了 Arrow 集成](https://docs.coiled.io/blog/pyarrow-in-pandas-and-dask.html)，如果你想了解更多，可以阅读。

## 2\. 使用新洗牌算法加速连接操作

洗牌是分布式系统中的一个关键组件，用于实现排序、连接和复杂的分组操作。它是一种全对全、网络密集型的操作，通常是工作流中最昂贵的部分。我们重写了 Dask 的洗牌系统，这对整体性能产生了巨大影响，尤其是在复杂的数据密集型工作负载下。

洗牌操作本质上是一种全对全的通信操作，其中每个输入分区必须将一小部分数据提供给每个输出分区。Dask 最初使用了自己的基于任务的算法，成功地将 `O(n * n)` 的任务复杂度减少到了 `O(log(n) * n)`，其中 `n` 是分区数量。这大大减少了任务数量，但非线性扩展性最终使得 Dask 无法处理任意大的数据集。

Dask 引入了一种新的 P2P（点对点）洗牌方法，将任务复杂度降低到了 `O(n)`，并且随着数据集大小和集群规模的增长呈线性扩展。它还集成了高效的磁盘功能，使得可以轻松地洗牌大于内存的数据集。新系统极其稳定，并且在任何规模的数据上都能“正常工作”。

![](img/06eeae42ba346f26d229371a3f1d9e2c.png)

`与 P2P 相比，传统洗牌的内存使用`

我的一个同事写了[关于此内容的文章](https://docs.coiled.io/blog/shuffling-large-data-at-constant-memory.html)，其中包含了更为详细的解释和大量技术细节。

## 3\. 优化器

Dask 本身是惰性计算的，这意味着它会在执行任何实际工作之前注册整个查询。这是一个强大的概念，使得许多优化成为可能，但历史上 Dask 并没有充分利用这一知识。Dask 在隐藏内部复杂性方面做得不好，用户在应对分布式计算和执行大规模查询的困难时只能靠自己。这使得非专家编写高效代码变得非常痛苦。

[3 月发布的 Dask 版本](https://docs.dask.org/en/stable/changelog.html#query-planning)包含了 DataFrame API 的完全重新实现，以支持查询优化。这是一个大事件。新的引擎围绕查询优化器展开，优化器会重写我们的代码，使其更加高效，更好地发挥 Dask 的优势。让我们深入了解一些优化策略，它们如何使我们的代码运行得更快，并更好地扩展。

在深入探讨一些针对分布式系统和 Dask 更具体的技术之前，我们将从一些对每个类似 DataFrame 的工具都有用的通用优化开始。

## 3.1 列投影

大多数数据集的列数超过我们实际需要的列数。去除不必要的列需要前瞻性思维（“我在这个查询中需要哪些列？🤔”），因此大多数人在加载数据时不会考虑这一点。这对性能不好，因为我们携带了许多不需要的数据，导致一切变得更慢。列投影在不再需要列时立即去除它们。这是一个简单的优化，但却非常有益。

传统实现总是从存储中读取所有列，只有在我们主动要求时才去除列。仅仅减少操作的数据量就能大幅提升性能和内存使用效率。

优化器查看查询并确定每个操作所需的列。我们可以将其想象成从查询的最终步骤开始，然后一步步回溯到数据源，并注入丢弃操作以去除不必要的列。

![](img/a84cc99cdcd2f59bd804a4137b77b521.png)

`最终我们只需要一个子集的列。替换操作不需要访问所有列，因此我们可以在 IO 步骤中直接丢弃不必要的列。`

## 3.2 过滤下推

过滤下推是另一种通用优化，其目标与列投影相同：操作较少的数据。传统实现只是将过滤器保留在我们放置的位置。新的实现尽可能早地执行过滤操作，同时保持相同的结果。

优化器识别我们查询中的每个过滤器，并查看之前的操作，判断是否可以将过滤器移得更靠近数据源。它会重复这一过程，直到找到一个不能与过滤器交换的操作。这比列投影要难一些，因为我们需要确保这些操作不会改变数据框的值。例如，交换过滤器和合并操作是可以的（值不变），但交换过滤器和替换操作是无效的，因为我们的值可能会改变，原本会被过滤掉的行现在可能不会被过滤掉，反之亦然。

![](img/cf3fab96fed4a7b19ba76286970b9b3c.png)

`最初，过滤操作发生在 Dropna 之后，但我们可以在不改变结果的情况下，在 Dropna 之前执行过滤操作。这允许我们将过滤器推送到 IO 步骤中。`

此外，如果我们的过滤器足够强大，那么我们可能在 IO 步骤中直接丢弃完整的文件。这是最佳情况，其中早期的过滤操作带来了巨大的性能提升，甚至需要从远程存储读取更少的数据。

## 3.3 自动调整分区大小

除了实现上述常见的优化技术外，我们还改进了一个普遍存在的痛点，特别是对于分布式系统和 Dask 用户：最佳分区大小。

Dask DataFrame 由许多称为*分区*的小型 pandas DataFrame 组成。通常，分区的数量是由系统决定的，Dask 用户被建议在减少或扩展数据后手动“重新分区”（例如通过删除列、过滤数据或通过连接操作进行扩展）（参见[Dask 文档](https://docs.dask.org/en/stable/dataframe-best-practices.html#repartition-to-reduce-overhead)）。如果没有这一步，Dask 的（通常很小的）开销可能会成为瓶颈，特别是当 pandas DataFrame 变得过小时，这会使 Dask 的工作流变得非常缓慢。

手动控制分区大小是一项困难的任务，我们作为 Dask 用户不应该为此担忧。这也很慢，因为它需要通过网络传输一些分区。现在，Dask DataFrame 会自动做两件事来帮助处理当分区变得过小的情况：

+   保持每个分区的大小恒定，基于你想要计算的数据与原始文件大小的比例。例如，如果你筛选掉原始数据集的 80%，Dask 会自动将结果中较小的分区合并成更少、更大的分区。

+   基于绝对最小值（默认为 75MB），将过小的分区合并为更大的分区。例如，如果你的原始数据集被拆分成许多很小的文件，Dask 会自动将它们合并。

![](img/29a069b1cef38c8590db87fd8c1d8acc.png)

`我们从整个文件的 200MB 中选择了两个占用 40MB 内存的列。`

优化器会查看列的数量以及这些列中的数据大小。它会计算一个比率，用于将多个文件合并为一个分区。

![](img/34dd0149f27adf9b8b3dc472e87ba4a2.png)

`40/200 的比率导致将五个文件合并为一个分区。`

目前，这一步骤仅限于 IO 操作（如读取 Parquet 数据集），但我们计划将其扩展到其他允许廉价合并分区的操作。

## 3.4 简单的合并和连接操作

在单机上使用 Pandas 时，合并和连接操作通常是便宜的，但在分布式环境中则很昂贵。在共享内存中合并数据是便宜的，而通过网络合并数据则非常慢，因为前面提到的洗牌操作。

这是分布式系统中最昂贵的操作之一。传统实现每次合并操作都会触发输入 DataFrame 的网络传输。这在某些情况下是必要的，但代价高昂。

![](img/a51a432975c2076cfb5712349c8db866.png)

`两个连接操作都是在同一列上进行的。左侧的 DataFrame 在第一次连接后已经正确分区，因此我们可以避免在新实现中再次进行洗牌操作。`

优化器将判断何时需要洗牌，何时仅仅是简单的连接足够，因为数据已经对齐。这可以使个别合并操作变得更快。这个原则同样适用于其他通常需要洗牌的操作，如`groupby().apply()`。

Dask 的合并操作曾经效率低下，导致运行时间过长。优化器解决了这些操作在彼此之后发生时的简单情况，但该技术目前还不够先进。仍然有很大的改进空间。

![](img/cc5ea176270a4da971f228de933a3f7a.png)

`当前的实现会对来自同一表的两个分支进行洗牌。通过在更高层次插入一个洗牌节点，可以避免其中一个昂贵的操作。`

优化器会查看表达式，并在必要时插入洗牌节点，以避免不必要的洗牌操作。

## 相较于传统实现，这些改进的效果如何？

Dask 现在比以前快了 20 倍。这个改进适用于整个 DataFrame API（不仅仅是某些独立的组件），且没有已知的性能回归。Dask 现在能够运行以前无法在可接受的时间内完成的工作负载。这个性能提升是由于多个改进的叠加。它不是做一件事情特别好，而是避免做任何事情特别差。

![](img/308b0407b24987f46372d5b56e3cf07d.png)

`TPC-H 基准测试中的查询 3 性能提升，来自[`github.com/coiled/benchmarks/tree/main/tests/tpch`](https://github.com/coiled/benchmarks/tree/main/tests/tpch_)`

性能虽然是最吸引人的改进，但并不是唯一得到改善的地方。优化器隐藏了很多复杂性，使得用户从 pandas 迁移到 Dask 变得更加容易，因为现在写出性能差的代码变得更加困难。整个系统变得更加健壮。

新的 API 架构也更容易使用。旧版实现将许多内部复杂性泄露到高层 API 中，导致更改变得繁琐。现在，改进几乎是轻松易行的。

## 接下来会发生什么？

在过去 18 个月中，Dask DataFrame 发生了很大变化。旧版 API 通常难以使用，且在扩展性方面表现较差。新的实现去除了不奏效的部分，并改进了现有实现。现在，繁重的工作已经完成，这使得加快迭代周期、改进现状成为可能。增量改进现在变得轻而易举。

一些即将实施的事项：

+   **自动重新分区：** 这部分已经实现，但在优化过程中仍有更多潜力选择更高效的分区大小。

+   **更快的连接：** 这里仍有很多精细调整的空间。例如，我们有一个 PR 正在进行，其中带来了 30-40%的性能提升。

+   **连接重排序：** 我们还没有做到这一点，但它在即将实施的事项中。

# **了解更多**

本文重点介绍了对 Dask DataFrame 的多个改进，以及这些改进带来了更快、更可靠的表现。如果你在选择 Dask 和其他流行的 DataFrame 工具时，可能还需要考虑：

+   [**大规模数据框比较：** TPC-H](https://docs.coiled.io/blog/tpch.html)，该报告比较了 Dask、Spark、Polars 和 DuckDB 在从 10 GB 到 10 TB 的数据集上的性能，涵盖本地和云端。

感谢阅读。欢迎随时联系我们，分享您的想法和反馈。
