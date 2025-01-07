# Julia 中的 One Billion Row Challenge

> 原文：[https://towardsdatascience.com/the-one-billion-row-challenge-in-julia-bdd19cde58d5?source=collection_archive---------9-----------------------#2024-06-05](https://towardsdatascience.com/the-one-billion-row-challenge-in-julia-bdd19cde58d5?source=collection_archive---------9-----------------------#2024-06-05)

## 如果数据科学家决定接受这个任务，他们能学到什么？

[](https://medium.com/@vikas.negi10?source=post_page---byline--bdd19cde58d5--------------------------------)[![Vikas Negi](../Images/3f5974d44cfdbdecb77e3b4cb3098af0.png)](https://medium.com/@vikas.negi10?source=post_page---byline--bdd19cde58d5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdd19cde58d5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bdd19cde58d5--------------------------------) [Vikas Negi](https://medium.com/@vikas.negi10?source=post_page---byline--bdd19cde58d5--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdd19cde58d5--------------------------------) ·阅读时间 8 分钟·2024年6月5日

--

![](../Images/dded10279fcf25261adbea5aecf60745.png)

图片来源：[Indira Tjokorda](https://unsplash.com/@indiratjokorda?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

今年早些时候，Gunnar Morling 启动了 [One Billon Row Challenge](https://www.morling.dev/blog/one-billion-row-challenge/)，自那以后，它获得了很大的关注。虽然最初的挑战是使用 Java 完成的，但令人惊叹的开源社区随后分享了多种编程语言下的精彩解决方案。我注意到，使用 Julia 的人并不多（至少没有公开分享过结果），所以决定通过这篇文章分享我自己的尝试。

我常常想一个问题，这个挑战对数据科学家有什么价值？除了做一个有趣的练习之外，我们能学到更多的东西吗？毕竟，这个挑战的目标是“简单地”解析一个大型虚拟数据文件，计算基本统计量（最小值、最大值和均值），并以特定格式输出数据。这对于大多数数据科学家通常从事的项目来说，可能并不是一个现实的情况。

嗯，问题的一个方面涉及数据的大小与可用RAM之间的关系。在本地工作（笔记本电脑或桌面）时，对大多数人来说，难以将所有数据一次性加载到内存中。因此，处理比内存更大的数据集成为一项必要的技能，这在原型化大数据管道或进行大数据分析/可视化任务时可能会派上用场。原始挑战的规则也指出应该避免使用外部库/包。这迫使你思考新颖的解决方案，并为学习语言本身的细节提供了一个迷人的机会。

在接下来的文章中，我将分享两种方法的结果——使用基础的Julia以及通过外部包进行的结果。通过这种方式，我们可以比较每种方法的优缺点。所有实验均在配备AMD Ryzen 9 5900X（12核心，24线程）、32 GB RAM和三星NVMe SSD的桌面上进行。Julia 1.10.2运行在Linux（Elementary OS 7.1 Horus）上。所有相关代码可以在[这里](https://github.com/vnegi10/1brc_julia)找到。请注意，这里的性能也与硬件有关，因此如果你决定在自己的系统上运行脚本，结果可能会有所不同。

# 前提条件

推荐使用最近发布的[Julia](https://julialang.org/downloads/)版本，例如1.10。对于那些想使用笔记本的人，上述[仓库](https://github.com/vnegi10/1brc_julia)还包含一个Pluto文件，使用该文件需要安装[Pluto.jl](https://github.com/fonsp/Pluto.jl)。挑战的输入数据文件是每个人独特的，必须使用[这个Python脚本](https://github.com/gunnarmorling/1brc/blob/main/src/main/python/create_measurements.py)生成。请记住，该文件大约有15 GB。

```py
python3 create_measurements.py 1000000000
```

此外，我们还将使用[BenchmarkTools.jl](https://github.com/JuliaCI/BenchmarkTools.jl)包来运行基准测试。请注意，这不会影响挑战，只是用来收集适当的统计数据，以衡量和量化Julia代码的性能。

# 使用基础的Julia

输入数据文件`measurements.txt`的结构如下（这里只显示前五行）：

```py
attipūdi;-49.2
Bas Limbé;-43.8
Oas;5.6
Nesebar;35.9
Saint George’s;-6.6
```

该文件包含十亿行（也称为记录或行）。每一行都有一个站点名称，后跟`；`分隔符，然后是记录的温度。唯一的站点数量最多可达10,000。这意味着同一个站点会出现在多行中。因此，我们需要收集文件中所有不同站点的所有温度数据，然后计算所需的统计信息。很简单，对吧？

## 让我们从简单但慢慢来开始

我的第一次尝试是简单地逐行解析文件，然后将结果收集到一个字典中，每个站名作为键，温度作为`Float64`类型的向量添加到值中。我预期这会很慢，但我们的目标是获得基准性能的数字。

一旦字典准备好，我们就可以计算所需的统计数据：

所有数据处理的输出需要以某种特定格式显示。以下函数实现了这一点：

由于这个实现预计需要较长时间，我们可以通过仅运行一次`@time`来进行简单的测试：

```py
@time get_stations_dict_v2("measurements.txt") |> calculate_output_v3 |> print_output_v1

<output omitted for brevity> 526.056399 seconds (3.00 G allocations: 302.881 GiB, 3.68% gc time)
```

我们这个简单的实现大约需要526秒，即约9分钟。虽然它确实很慢，但也并没有那么糟糕！

## 升级一下——进入多线程！

与其一次读取输入文件的一行，不如尝试将文件拆分成多个块，然后并行处理所有块。Julia让实现并行`for`循环变得非常简单。然而，在这样做时，我们需要采取一些[预防措施](https://docs.julialang.org/en/v1/manual/multi-threading/#Caveats)。

在进入循环之前，我们首先需要弄清楚如何将文件拆分成多个块。可以通过使用[内存映射](https://docs.julialang.org/en/v1/stdlib/Mmap/https://docs.julialang.org/en/v1/stdlib/Mmap/)来读取文件。然后，我们需要确定每个块的`start`和`end`位置。需要注意的是，输入数据文件中的每一行以换行符结尾，其字节表示为`0x0a`。因此，每个块应该在该字符处结束，以确保在解析文件时不出错。

以下函数将块的数量`num_chunks`作为输入参数，然后返回一个数组，其中每个元素都是内存映射的块。

由于我们从不同的块中解析站点和温度数据，我们还需要在最后将它们合并。每个块将首先被处理为字典，如前所示。然后，我们按如下方式合并所有块：

现在我们知道如何将文件拆分成多个块，以及如何在最后将各个块解析出的字典合并。然而，只有在我们能够并行处理这些块时，才能获得所需的加速效果。这可以通过`for`循环来实现。请注意，Julia应该使用[多线程启动](https://docs.julialang.org/en/v1/manual/multi-threading/#Starting-Julia-with-multiple-threads) `julia -t 12`，否则这个解决方案不会产生任何影响。

此外，我们现在希望运行一个正式的统计基准测试。这意味着该挑战应该执行一定次数，然后我们可以可视化结果的分布。幸运的是，所有这些都可以通过[BenchmarkTools.jl](https://github.com/JuliaCI/BenchmarkTools.jl)轻松完成。我们将最大样本数限制为10，整个运行的最大时间设为20分钟，并启用垃圾回收（将在样本之间释放内存）。所有这些都可以在一个脚本中整合。请注意，输入参数现在是文件名`fname`和数据块数`num_chunks`。

以下是基准测试结果和所用输入。请注意，我们在这里使用了12个线程。

```py
julia> Threads.nthreads()
12

julia> ARGS = ["measurements.txt", "48"]
2-element Vector{String}:
 "measurements.txt"
 "48"
```

![](../Images/72ec4e552a082a0938866ae4f1e12de3.png)

12个线程，数据块数 = 48（图片由作者提供）

多线程显著提升了性能，我们现在的时间大约降至2分钟多一点。接下来我们看看还能做什么改进。

## 避免存储所有温度数据

到目前为止，我们的方法是存储所有的温度数据，然后在最后确定所需的统计值（最小值、平均值和最大值）。然而，我们已经可以在解析输入文件的每一行时实现相同的功能。每当找到一个新的值时（如果是更大的值则更新最大值，或更小的值则更新最小值），我们就替换现有的值。对于平均值，我们将所有的值相加，并保持一个单独的计数器，用于记录某个站点的温度出现了多少次。

总体而言，我们的新逻辑如下所示：

合并所有结果（来自不同数据块）的函数也需要相应地进行更新。

让我们进行一个新的基准测试，看看这个改变是否能改善时间表现。

![](../Images/03e629680c5f7ea6a82d412a8dfd74b9.png)

12个线程，数据块数 = 48（图片由作者提供）

中位数时间似乎有所改善，但改进幅度很小。不过，依然算是一个胜利！

## 更多性能提升

我们之前的逻辑用于计算和保存温度的最大值和最小值可以进一步简化。此外，按照这个[Julia Discourse帖子](https://discourse.julialang.org/t/the-one-billion-row-challenge/109534/24)中的建议，当解析站点名称和温度数据时，我们可以使用视图（通过`@view`）。这一点在Julia性能手册中也有[讨论](https://docs.julialang.org/en/v1/manual/performance-tips/index.html#Consider-using-views-for-slices-1)。由于我们在解析每一行时使用了切片表达式，`@view`帮助我们避免了分配和复制的开销。

其余的逻辑保持不变。现在运行基准测试得到以下结果：

![](../Images/f521124dfe4e634f7f0478fcbea99c64.png)

12个线程，数据块数 = 48（图片由作者提供）

哇！我们成功地将时间缩短到了接近一分钟。看起来切换到不同的视图确实有很大的影响。也许，还有更多的调整可以进一步提高性能。如果你有任何建议，请在评论中告诉我。

# 使用外部包

仅限使用基础 Julia 进行限制是很有趣的。然而，在实际应用中，我们几乎总是会使用包，因此可以利用现有的高效实现来执行相关任务。在我们的例子中，CSV.jl（并行解析文件）和 DataFrames.jl（执行 `groupby` 和 `combine`）将会派上用场。

以下函数执行以下任务：

+   使用 `Mmap` 读取大文件

+   将文件分割成预定义数量的块

+   遍历这些块，使用 `CSV.read`（将 12 个线程传递给 `ntasks`）并行读取每个块到 DataFrame 中。

+   使用 DataFrame 的 `groupby` 和 `combine` 获取每个车站的结果

+   将所有 DataFrame 连接起来，合并所有分块的结果

+   循环结束后，执行一次 `groupby` 和 `combine`，以获取所有车站的最终结果集。

现在，我们可以以与之前相同的方式运行基准测试。

![](../Images/72da5f5c1ebda12a8453bd0ddd76df4a.png)

12 个线程，分块数 = 48，使用外部包（图片由作者提供）

使用 CSV.jl 和 DataFrames.jl 的性能相当不错，尽管比我们使用基础 Julia 实现的要慢。在实际项目中，这些包是数据科学家工具包中的重要组成部分。因此，探索使用这种方法是否可以进行进一步优化将是很有趣的。

# 结论

在这篇文章中，我们通过 Julia 解决了十亿行挑战。从一个非常简单的实现开始，花费大约 10 分钟，我们通过对代码的迭代修改，成功地大幅提升了性能。最优化的实现可以在约 1 分钟内完成挑战。我确信仍然有改进的空间。作为额外的收获，我们学到了一些宝贵的技巧，如何处理超出内存的数据集。当使用 Julia 进行大数据分析和可视化时，这可能会派上用场。

希望你觉得这个练习有用。感谢你的时间！可以通过 [LinkedIn](https://www.linkedin.com/in/negivikas/) 与我联系，或者访问我的 [Web 3.0 网站](https://vikasnegi.eth.limo/)。

# 参考文献

1.  [https://www.morling.dev/blog/one-billion-row-challenge/](https://www.morling.dev/blog/one-billion-row-challenge/)

1.  [https://docs.julialang.org/en/v1/manual/performance-tips/index.html#man-performance-annotations](https://docs.julialang.org/en/v1/manual/performance-tips/index.html#man-performance-annotations)
