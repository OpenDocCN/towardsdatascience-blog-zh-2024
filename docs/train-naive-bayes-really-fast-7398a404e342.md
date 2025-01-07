# 快速训练朴素贝叶斯模型

> 原文：[`towardsdatascience.com/train-naive-bayes-really-fast-7398a404e342?source=collection_archive---------10-----------------------#2024-05-31`](https://towardsdatascience.com/train-naive-bayes-really-fast-7398a404e342?source=collection_archive---------10-----------------------#2024-05-31)

![](img/5ca58b4de0a8ed93631227f9f6925e2f.png)

图片由[Marc Sendra Martorell](https://unsplash.com/de/@marcsm?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/de/fotos/zeitraffer-von-strassenlaternen--Vqn2WrfxTQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

## 在 Julia 中的性能调优

[](https://medium.com/@schaetzle.ka?source=post_page---byline--7398a404e342--------------------------------)![Roland Schätzle](https://medium.com/@schaetzle.ka?source=post_page---byline--7398a404e342--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7398a404e342--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7398a404e342--------------------------------) [Roland Schätzle](https://medium.com/@schaetzle.ka?source=post_page---byline--7398a404e342--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7398a404e342--------------------------------) ·12 分钟阅读·2024 年 5 月 31 日

--

在最近的一堂课上，我向学生展示了如何使用*多项式朴素贝叶斯（MNB）*模型进行文档分类。作为示例，我使用了[Enron 电子邮件数据集](https://www.cs.cmu.edu/~enron/)，并基于该模型创建了一个垃圾邮件过滤器。使用的数据集版本包含 33,716 封电子邮件，分类为“垃圾邮件”或“正常邮件”（即非垃圾邮件）。

我们选择了来自 Julia 的`MultinomialNBClassifier`，并使用同一包中的`CountTransformer`进行数据准备。我感到相当惊讶的是，使用整个数据集训练这个分类器（在一台 Apple M3，16 GB RAM 的机器上）竟然花费了超过 30 分钟。

通常，仅使用数据集的一部分进行训练，因为其余部分需要用于测试。即便仅使用数据集的 70%进行训练，仍然花费了超过 10 分钟。33,716 封电子邮件无疑超过了简单的教科书示例，但另一方面，朴素贝叶斯模型以低训练成本而著称。

因此，我开始调查为什么训练需要这么长时间，以及是否有方法可以加快速度。在接下来的部分，我将介绍我所应用的性能调优措施及所能实现的加速效果。这些措施并非特别针对这个问题，因此也应该适用于其他情况。

> 注意：所有实现和基准测试均使用 Julia 1.10.3 版本在 16 GB RAM 的 M3 MacBook Pro 上完成。所使用的 Julia 包包括 MLJ 0.20.0、TextAnalysis 0.7.5、Metal 1.1.0、CategoricalArrays 0.10.8 和 BenchmarkTools 1.5.0。

# 训练多项式朴素贝叶斯模型

但首先让我介绍一下训练 MNB 所必需的主要步骤，以便理解需要优化的算法。具体包括：

+   一个**数据准备**步骤，它将文档（在我们的案例中是电子邮件）转换为合适的数据结构（即所谓的*文档词项矩阵；DTM*）并

+   **实际训练**步骤，其中将文档词项矩阵（DTM）聚合为每个类别（垃圾邮件或非垃圾邮件）的向量

## 数据准备

用于 MNB 的文档被表示为“词袋”。也就是说，文档中单词的顺序被认为是无关的，只存储每个单词出现的次数。因此，“the cow eats grass”这个句子在这种表示下等同于“eats the cow grass”或“grass eats the cow”。

为了使用内存高效的表示形式将所有文档转换为这种形式，我们创建了一个*字典*，其中包含文档中出现的所有单词（它本质上是一个包含所有单词的数组）。假设我们有以下文档 D1、D2 和 D3：

+   D1: “灰色的猫躺在草地上”

+   D2: “牛吃草”

+   D3: “猫是灰色的”

然后字典如下：[“the”, “grey”, “cat”, “lies”, “on”, “grass”, “cow”, “eats”, “is”]，因为这三篇文档中有九个不同的单词。

每个文档随后表示为一个与字典长度相同的数组，数组中的每个元素是字典中对应单词出现的次数。因此，D1、D2 和 D3 将具有以下形式：

+   D1: [2, 1, 1, 1, 1, 1, 0, 0, 0] 例如，字典中的第一个词（“the”）出现了两次，第二个词（“grey”）出现了一次，依此类推

+   D2: [1, 0, 0, 0, 0, 1, 1, 1, 0]

+   D3: [1, 1, 1, 0, 0, 0, 0, 0, 1]

如果我们将这些数组合并为一个矩阵——每个文档一行，那么我们就得到了上面提到的*文档词项矩阵（DTM）*。在我们的案例中，它是一个 3 x 9 的矩阵，因为我们有三篇文档和一个包含九个不同单词的词典。

## 训练

MNB 的训练基本上是通过将所有文档向量按类别分开进行加总。也就是说，在我们的垃圾邮件示例中，我们必须将所有“垃圾邮件”类别的文档向量加起来，所有“非垃圾邮件”类别的文档向量加起来，最终得到两个向量，每个向量包含该类别的单词频率汇总。

如果我们假设文档 D1 和 D3 是“非垃圾邮件”，D2 是“垃圾邮件”，我们将得到以下结果：

+   “非垃圾邮件”单词频率: [3, 2, 2, 1, 1, 1, 0, 0, 1]

+   “垃圾邮件”单词频率: [1, 0, 0, 0, 0, 1, 1, 1, 0]

在 MNB 的完整训练步骤中，除了对这些数字进行一些后处理外，真正“耗时”的部分是这里展示的 DTM 的聚合过程，这是我们需要优化的部分。

# 从 Enron 数据集开始

## 数据准备

我使用 `CountTransformer` 创建了 Enron 数据集的 DTM，该方法是 `MLJ` 中的一部分，具体函数如下：

```py
function transform_docs(doc_list)
    CountTransformer = @load CountTransformer pkg=MLJText
    trans_machine = machine(CountTransformer(), doc_list)
    fit!(trans_machine)
    return(MLJ.transform(trans_machine, doc_list))
end
```

此函数的输入 `doc_list` 是一个分词后的电子邮件数组。也就是说，每封邮件中的每个词被分离成一个单独的字符串（使用 `TextAnalysis.tokenize()`）。

结果是一个 33,716 x 159,093 的矩阵，因为有 33,716 封邮件，字典中包含 159,093 个不同的单词。这是一个包含超过 53 亿个元素的矩阵。令人惊讶的是，创建 DTM 的时间不到一分钟。因此，性能调优的重点将完全放在训练步骤上。

由于 DTM 中大多数元素为 0，因此使用了所谓的 *稀疏矩阵* 来以内存高效的方式存储它们（在 Julia 中，这种类型是 `[SparseMatrixCSC](https://docs.julialang.org/en/v1/stdlib/SparseArrays/)`）。

更准确地说，`CountTransformer` 生成的 数据结构是 `LinearAlgebra.Adjoint{Int64,SparseMatrixCSC{Int64, Int64}}` 类型。稍后我们将讨论这个特殊结构。

## 训练

然后，训练 `MultinomialNBClassifier` 的方法如下，`X` 包含 DTM，`y` 是垃圾邮件/非垃圾邮件标签的数组（作为一个 `[CategoricalArray](https://github.com/JuliaData/CategoricalArrays.jl?tab=readme-ov-file)`，因为所有 MLJ 模型都期望这种类型）：

```py
MultinomialNBClassifier = @load MultinomialNBClassifier pkg=NaiveBayes
nb_classifier = MultinomialNBClassifier()
nb_machine = machine(nb_classifier, X, y)
fit!(nb_machine, verbosity=0)
```

对 `fit!` 的调用进行实际训练，处理所有 Enron 邮件时花费了超过 30 分钟，对于一个 70% 子集则花费了超过 10 分钟。

为了专注于训练步骤的分析和优化，我从自己实现的一个函数开始，该函数执行上述所有文档向量聚合，将其转换为包含“垃圾邮件”和“非垃圾邮件”总结词频的两个向量。`MultinomialNBCClassifier` 的相关代码有太多依赖，这使得演示以下优化步骤变得不可行。

该函数（名为 `count_words`）的第一个基线方法如下所示：

```py
function count_words_base(X::AbstractMatrix{Int64},y)
    ndocs = size(X,1)         # number of documents
    nwords = size(X,2)        # number of words in dictionary
    ncats = length(levels(y)) # number of categories in `y`
    wcounts = ones(Int64, ncats, nwords) # matrix for storing the word counts by category
    for col in 1:nwords
        for doc in 1:ndocs
            if y[doc] == “ham”
                wcounts[1,col] += X[doc, col]
            else
                wcounts[2,col] += X[doc, col]
            end
        end
    end
    return(wcounts)
end
```

应用于 `X` 和 `y` 时，完成需要 **241.076 秒**。

为了减少测试运行的时间，并避免内存成为影响运行时间的决定性因素，我将所有进一步的测试（除非另有说明）都限制在 DTM 的一部分（称为 `Xpart`），只包含前 10,000 列（即一个 33,716 x 10,000 的矩阵）。

对于这个减少后的 DTM，`count_words_base` 完成需要 **20.363 秒**。

# OPT1：以正确的方式使用正确的数据结构

性能调优的一个重要方面是所使用的数据结构，以及它们是否以最有效的方式使用。

## 列优先存储

从这个意义上来说，`count_words_base` 已经使用了一种优化。在 Julia 中，矩阵是按列优先顺序存储的。也就是说，每列的元素在内存中是紧密存储的。因此，遍历 *一列* 的所有元素比遍历 *一行* 中的元素更快。因此，`count_words_base` 中的内层循环是遍历 `X` 中的一列。

在 Julia 中，列优先顺序存储是常见的做法。这对于`SparseMatrixCSC`或`DataFrame`等也适用。但检查数据结构使用的是哪种存储顺序始终是一个好主意。

## CategoricalArrays

`count_words_base`中的 if 语句会对 DTM 的每个元素执行。所以优化这部分函数肯定会有帮助。

参数`y`并不是一个“普通的”数组，它不会将单词`"ham"`或“spam”存储 33,716 次。它是一个`[CategoricalArray](https://github.com/JuliaData/CategoricalArrays.jl?tab=readme-ov-file)`，它只存储这两个单词一次，并且内部使用一个整数数组来存储 33,716 个不同的`"ham"`和“spam”值（这两个值由数字 1 和 2 表示）。我们可以使用`levelcode`函数访问这种数值表示。所以`y[1]`返回的是`"ham"`，而`levelcode(y[1])`则返回 1。

因此，我们可以通过以下单行代码替换整个 if 语句（从而得到第一个优化版本`count_words_01`）：

```py
wcounts[levelcode(y[doc]),col] += X[doc, col]
```

这为我们提供了**18.006 s**的运行时间，约提升了 10%。

## 更高效的矩阵

通常内存高效的数据结构在访问其元素时效率较低。因此，我怀疑（稠密）矩阵（即二维`Array`）可能比用于 DTM 的稀疏矩阵更具性能。

作为**参考点**，我创建了一个与`Xpart`相同大小的**稠密矩阵**`Xref`（填充随机数字）：`Xref = rand(0:9, 33716, 10000)`。

该矩阵具有以下运行时间：

+   `count_words_base`: **2.378 s**

+   `count_words_01`: **0.942 s**

所以`CountTransformer`生成的 DTM 肯定存在实际问题。即使是基线实现也能让我们获得超过 8 倍的加速，而`count_words_01`中使用的优化在这种情况下更为有效，将运行时间减少到基线数值的一半以下！

如前所述，`CountTransformer`并没有生成实际的`SparseMatrixCSC`，而是生成了一个`LinearAlgebra.Adjoint{Int64,SparseMatrixCSC{Int64, Int64}}`。也就是说，稀疏矩阵被包装在其他结构中。这可能会成为一个问题。因此，我尝试提取实际的稀疏矩阵……但证明这是困难且昂贵的：提取过程需要将近 17 秒！

但是最终得到的**“纯”稀疏矩阵**要高效得多：

+   `count_words_base`: **3.22 s**

+   `count_words_01`: **1.435 s**

由于我们必须为提取这些数字额外增加将近 17 秒，这并没有从整体上提升处理速度。因此，我在寻找替代方案时，找到了`[TextAnalysis](https://github.com/JuliaText/TextAnalysis.jl?tab=readme-ov-file)`包，其中也有一个创建 DTM 的函数。创建过程与`CountTransformer`一样高效，但它直接生成“纯”稀疏矩阵。

因此，我们可以获得稀疏矩阵的运行时数字，而无需额外增加 17 秒。这在这一点上为我们带来了**加速**，即 20.363/1.435 = **14.2**。

# OPT2: 多线程

使用 Julia 相对容易实现多线程，尤其是在我们的案例中，我们迭代一个数据结构并在每次迭代中访问该数据结构的不同部分。因此，每次迭代都可以在另一个线程中执行，而不必担心数据访问冲突。

在这种设置下，我们只需要在`for`语句前面加上宏`@threads`，然后 Julia 会为我们做其余的工作。也就是说，它会将不同的迭代分配到特定机器上可用的线程中。由于 M3 芯片有八个内核，我将`JULIA_NUM_THREADS`环境变量设置为 8，并将`count_words`函数中的 for 循环部分更改如下（得到下一个优化版本`count_words_02`）：

```py
@threads for col in 1:nwords
    for doc in 1:ndocs
        wcounts[levelcode(y[doc]),col] += X[doc, col]
    end
end
```

这给我们带来了**231 ms**的运行时间，**加速**为 20.363/0.231 = **88.2**。

# OPT3：GPU 与矩阵运算

获得更高的性能通常通过使用 GPU 来实现。但这只有在算法能够适应 GPU 这种非常特殊的计算结构时才能做到。理想情况下，您的算法应该由向量和矩阵运算组成。那么我们来探讨一下，是否可以将我们的`count_words`函数以这种方式进行适配。

## 过滤行

我们上面的例子仅包含三份文档 D1、D2 和 D3，可能是一个不错的起点，帮助我们更好地理解。对于这个简单的例子，`X`和`y`如下所示：

```py
X = [2 1 1 1 1 1 0 0 0;               y = ["ham", "spam", "ham"]
     1 0 0 0 0 1 1 1 0;
     1 1 1 0 0 0 0 0 1]
```

函数`count_words`会对列中的数字求和，但只针对特定的行。在这个例子中，首先加总第 1 行和第 3 行的数据，然后查看第 2 行。也就是说，我们需要某种过滤器来筛选行，然后我们可以直接对列进行求和。

在 Julia 中，可以使用`BitArray`对数组进行索引。也就是说，`X[[1,0,1],:]`会返回`X`的第 1 行和第 3 行，`X[[0,1,0],:]`则返回第 2 行。如果我们用 1 和 0 替换`y`中的“ham”和“spam”，并将其转换为以下矩阵，我们就可以得到这些“过滤器”：

```py
yb = [1 0;
      0 1;
      1 0]
```

因此，`yb[:,1]`将是第一个过滤器，`yb[:,2]`将是第二个过滤器。

对于垃圾邮件模型，我们可以使用以下函数将`CategoricalArray`类型的`y`转换为这样的二进制矩阵（`y.refs`是仅使用整数的内部表示）：

```py
function y_as_bitmatrix(y)
    spam = y.refs .== 2
    ham = y.refs .== 1
    return([ham spam]) # Bit-Matrix (one column per category)
end
```

使用这种表示法的`y`，我们可以这样实现`count_words`：

```py
function count_words_03(X::AbstractMatrix{Int64},y::BitMatrix)
    nwords = size(X,2).   # number of words in dictionary
    ncats = size(y,2)     # number of categories in `y`
    wcounts = ones(Int64, ncats, nwords) # matrix for storing the word counts by category
    for cat in 1:ncats
        @threads for col in 1:nwords
            wcounts[cat,col] = sum(X[y[:,cat],col])
        end
    end
    return(wcounts)
end
```

该变体的运行时间为**652 ms**（在 CPU 上）。所以并没有比上面的版本更快，但我们仍在进行探索。

## 点积

让我们再次回到简单的三文档示例：

```py
X = [2 1 1 1 1 1 0 0 0;               yb = [1 0;
     1 0 0 0 0 1 1 1 0;                     0 1;
     1 1 1 0 0 0 0 0 1]                     1 0]
```

我们也可以通过计算`X`中每一列与`yb`的第一列的*点积*，然后再与`yb`的第二列做同样的操作来实现我们的目标。这将得到`count_words_04`：

```py
function count_words_04(X::AbstractMatrix{Int64},y::BitMatrix)
    nwords = size(X,2)    # number of words in dictionary
    ncats = size(y,2)     # number of categories in `y`
    wcounts = ones(Int64, ncats, nwords) # matrix for storing the word counts by category
    for cat in 1:ncats
        @threads for col in 1:nwords
            wcounts[cat,col] = dot(X[:,col], y[:,cat])
        end
    end
    return(wcounts)
end
```

这导致运行时间为**4.96 ms**（在 CPU 上），现在的**加速**为 20.363/0.00496 = **4,105.4!**

这种剧烈的改进可能需要一些解释。这里有两个因素是相辅相成的：

+   像点积这样的向量运算在 Julia 中被超级优化，依赖于像[BLAS](https://en.wikipedia.org/wiki/Basic_Linear_Algebra_Subprograms)这样的经过验证的库。

+   稀疏矩阵类型在此上下文中非常高效。在这种情况下，我们的密集参考矩阵`Xref`的运行时间仅为 455.7 毫秒。

## 矩阵乘法

将上述的思路稍作扩展，我们可以以其转置形式表示`yb`，如下所示：

```py
ybt = [1 0 1;        X = [2 1 1 1 1 1 0 0 0;
       0 1 0]             1 0 0 0 0 1 1 1 0;
                          1 1 1 0 0 0 0 0 1] 
```

这种描述使得`count_words`的最简洁、也许是最优雅的版本几乎显而易见。它只是一个矩阵乘法：

```py
function count_words_05(X::AbstractMatrix{Int64},y::BitMatrix)
    transpose(Y) * X
end
```

它也是最快的版本，**1.105 毫秒**，带来 20.363/0.00105 = **19,393**倍的加速！

多线程在这里是隐式的，因为底层的 BLAS 库默认是多线程的。可以通过`BLAS.get_num_threads()`获取使用的线程数。

此外，这个解决方案具有良好的扩展性。应用于完整数据集，包含 33,716 x 159,093 个元素的矩阵`X`，需要 13.57 毫秒才能完成。这是 241.076/0.01357 = **17,765**倍的加速。

# OPT4: GPU

最后，将最后一个变体应用到 GPU 上可以通过`Metal.jl`包完成。为此，所使用的矩阵只需通过`mtl`函数转换为相应的金属数组类型：

```py
const mtl_Xpart = mtl(Xpart)
const mtl_yb = mtl(yb)
```

用于 GPU 的`count_words`变体，除了数据类型外，和上面的一样：

```py
function count_words_06(X::MtlMatrix,y::MtlMatrix)
    transpose(y) * X
end
```

它的运行时间仅为**0.306 毫秒**。但是将数据复制到 GPU（使用`mtl`）所需的时间要远远超过在 GPU 上运行算法所节省的时间。因此，它并不是真的更快。

除此之外，适用于 Apple Silicon GPU 的`Metal`包还不如例如`CUDA.jl`那样成熟。这一点在尝试将大型矩阵`X`转换为金属数组时变得明显：转换会停止并显示错误信息。

# 结论

当然，并非每个算法都可以像`count_words_05`那样转换为如此简洁的变体。但即便是更“经典”的实现`count_words_04`也比我们最初的版本快了超过 4,000 倍。本文中展示的许多性能优化措施也可以应用于其他函数。此外，我还建议任何想要从 Julia 程序中获得更多性能的人，遵循 Julia 文档中的“[性能优化提示](https://docs.julialang.org/en/v1/manual/performance-tips/)”。
