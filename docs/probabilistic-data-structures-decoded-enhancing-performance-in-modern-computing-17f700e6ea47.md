# 概率数据结构解码：提升现代计算性能

> 原文：[https://towardsdatascience.com/probabilistic-data-structures-decoded-enhancing-performance-in-modern-computing-17f700e6ea47?source=collection_archive---------5-----------------------#2024-01-09](https://towardsdatascience.com/probabilistic-data-structures-decoded-enhancing-performance-in-modern-computing-17f700e6ea47?source=collection_archive---------5-----------------------#2024-01-09)

## 布隆过滤器与Count Min Sketch在Python中的实现与理解终极指南

[](https://namanagr03.medium.com/?source=post_page---byline--17f700e6ea47--------------------------------)[![Naman Agrawal](../Images/6bb885397aec17f5029cfac7f01edad9.png)](https://namanagr03.medium.com/?source=post_page---byline--17f700e6ea47--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--17f700e6ea47--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--17f700e6ea47--------------------------------) [Naman Agrawal](https://namanagr03.medium.com/?source=post_page---byline--17f700e6ea47--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17f700e6ea47--------------------------------) ·阅读时间：26分钟·2024年1月9日

--

![](../Images/4192b4e13217e5e16700e6354b81f9c6.png)

图片来自 Google DeepMind：[https://www.pexels.com/photo/an-artist-s-illustration-of-artificial-intelligence-ai-this-image-visualises-the-input-and-output-of-neural-networks-and-how-ai-systems-perceive-data-it-was-created-by-rose-pilkington-17485706/](https://www.pexels.com/photo/an-artist-s-illustration-of-artificial-intelligence-ai-this-image-visualises-the-input-and-output-of-neural-networks-and-how-ai-systems-perceive-data-it-was-created-by-rose-pilkington-17485706/)

# 目录

1.  介绍

1.  什么是概率数据结构？

1.  布隆过滤器

    3.1 它们是如何工作的

    3.2 用Python实现布隆过滤器

    3.3 布隆过滤器：时间与空间复杂度分析

    3.4 布隆过滤器：数学分析

1.  Count Min Sketch

    3.1 它们是如何工作的

    3.2 用Python实现Count Min Sketch

    3.3 Count Min Sketch：时间与空间复杂度分析

    3.4 Count Min Sketch：数学分析

1.  结论

1.  参考文献

> **注意：** 本文中使用的完整代码文件可以在以下仓库中找到：[https://github.com/namanlab/Probabilistic_Data_Structures](https://github.com/namanlab/Probabilistic_Data_Structures)

# 介绍

计算机科学爱好者常常被算法的微妙魅力所吸引——这些默默无闻的工作马简化了我们的数字交互。编程的核心就是通过高效的算法与最佳数据结构的同步使用来完成任务。这也是为什么计算机科学中有一个专门的领域致力于算法的设计与分析，因为它们作为数字时代的建筑师，默默地塑造着我们的技术体验，融合了逻辑与精确。

传统的数据结构与算法课程通常会让学生接触一些基本的确定性数据结构，例如数组、链表、栈和队列、二叉搜索树、AVL树、堆、哈希映射，当然还有图。事实上，学习这些数据结构及相关算法构成了开发更复杂程序的基础，这些程序旨在解决各种任务。在本文中，我将介绍概率数据结构，如布隆过滤器和计数最小草图，这些较为鲜为人知的数据结构。虽然一些入门课程简要提到过这些，但它们只是通常被忽视的数据结构中的一部分，然而它们却在各种学术讨论中展现出重要性。在本文中，我们将描述什么是概率数据结构，它们的重要性，示例及其实现，并通过一些所需的数学知识来更好地评估它们的性能。让我们开始吧！

# 什么是概率数据结构？

概率数据结构是计算机科学中的巧妙工具，它们提供了某些操作的快速且节省内存的近似值。与总是给出精确结果的确定性数据结构不同，概率数据结构为了提高效率，会牺牲一点准确性。简单来说，这些结构利用随机性快速估算问题的答案，而不需要存储所有精确的细节；有点像智能捷径，通过做出合理的猜测而不是执行所有的工作。

以布隆过滤器为例，它是一种与 HashSet 非常相似的概率数据结构。它帮助你检查一个元素是否可能在一个集合中，或者肯定不在。它可能会说“可能在集合中”或者“肯定不在集合中”，但不会保证“肯定在集合中”。这种不确定性使它能够更快速，并比传统的哈希集节省更多内存。

而我们常见的确定性数据结构，如链表和 AVL 树，则会给出 100% 确定的答案。例如，如果你询问某个元素是否在哈希集内，它会毫不犹豫地回答“是”或“否”。然而，这种确定性往往以使用更多内存或更长时间的处理为代价。

总体的思路是，概率数据结构通过牺牲一点准确性来换取速度和效率，这使得它们在可以容忍少量错误的情况下非常有用。它们就像快速而聪明的助手，提供几乎完美的答案，却不需要做所有繁重的工作。假设你有一个庞大的数据集，并且需要执行像搜索、插入或检查成员资格之类的操作。确定性结构能够保证正确性，但在处理海量数据时可能会变得缓慢。通过接受一定的不确定性，概率结构提供了一种快速处理这些任务并减少内存需求的方法。在接下来的文章中，我们将探讨一些常用的概率数据结构：布隆过滤器和计数最小概述。让我们从布隆过滤器开始！

# 布隆过滤器

布隆过滤器旨在提供快速且内存高效的成员资格测试，即它们帮助回答这样的问题：“这个元素是集合的成员吗？”它们在速度和资源节省至关重要的场景中尤为有用，比如数据库查找、网络路由器和缓存系统。

## 它们是如何工作的？

布隆过滤器实现为一个大小为 m 的位数组，实际上就是一个大小为 m 的数组，数组中的每个元素是 0 或 1。一个空的布隆过滤器最初用全 0 填充。每当添加一个元素时，一组哈希函数将元素映射到一组索引。哈希函数是将任何大小的输入数据转换为一个小的整数（称为哈希值或哈希码），该整数可以作为索引使用。

回想一下传统哈希集合的工作原理。传统的哈希集合对输入数据（即要添加到集合中的元素）应用一个哈希函数，并生成一个哈希值，该值对应于元素在表中的索引位置。尽管这种方法提供了一种简单且快速的组织和检索数据的方式（通常是常数时间 O(1)），但它存在固有的内存低效问题，主要与哈希冲突和固定大小的表有关。

使用单一哈希函数可能导致哈希冲突，即不同的元素生成相同的哈希值并试图占用哈希表中的相同索引。这会影响哈希集合的效率，因为需要额外的机制来处理和解决此类冲突。此外，哈希表的固定大小导致在处理不同工作负载时效率低下。随着元素数量的增加或减少，负载因子（元素与表大小的比例）可能变得不利。这可能导致更多的哈希冲突和性能下降。为了解决这个问题，哈希集合通常需要重新调整大小，这一过程涉及创建一个新的、更大的表并重新哈希所有现有元素。尽管这个操作是必要的，但它会引入额外的计算开销和内存需求。

另一方面，Bloom Filter 不需要如此大的表格，它可以通过较小的 m 大小数组并使用多个哈希函数（例如，k 个不同的哈希函数）来完成工作，而不是仅仅使用一个。该过滤器的工作原理是对输入数据应用所有 k 个哈希函数，并将所有 k 个输出索引在原数组中标记为 1。以下是一个示例，展示其工作原理。

假设我们有一个 m = 5 位大小的数组，最初填充为 0，且有 k = 2 个哈希函数。通常，我们使用的哈希函数必须满足两个关键特性：i) 它们应该计算快速，ii) 输出应该大致均匀分布，以尽量减少假阳性的风险（别担心，细节稍后会讨论）。接下来，我们将元素 64 和 78 添加到数组中。

![](../Images/bcefca769e3234baab4a582897714f1f.png)

Bloom Filter 示例 [图片由作者提供]

上图展示了将条目添加到数组中的过程。一旦条目被添加，我们就可以进行成员测试。假设我们测试元素 36 的成员资格。如果 h1 和 h2 产生索引 0 和 3，我们可以很容易地看到索引 0 尚未被占用。这意味着该元素一定不在集合中。然而，我们可能会得到索引 2 而不是 3，而索引 2 已经被标记为已占用。这可能导致我们错误地得出结论，认为 36 在集合中，即使实际上并非如此。这就是假阳性的情况，这也是为什么我们说 Bloom Filter 只能保证当元素不在时。如果元素被认为存在，仍然有可能它并不在，尽管通过调整 m 和 k 的值，可以将这种情况的概率降到很小。

如果你仍然不清楚，可以将 Bloom Filter 想象成一个紧凑的检查表，里面有空框。这个检查表代表一个最初为空的集合。当你向集合中添加一个元素时，Bloom Filter 会根据该元素的特征填写某些框：每个元素都会使用不同的哈希函数进行多次哈希，从而决定标记哪些框。多个哈希函数确保检查表的不同部分受到影响，从而使其对假阳性更具弹性。当你想检查某个元素是否在集合中时，你使用在插入时标记框的相同哈希函数进行哈希。如果所有相应的框都被标记，那么 Bloom Filter 会提示该元素可能在集合中。然而，假阳性可能会发生——过滤器可能会错误地声明元素在集合中，而实际上并不在。另一方面，假阴性永远不会发生。如果框没有被标记，则该元素一定不在集合中。

如你所见，布隆过滤器在速度和效率方面提供了优势，但也有一些权衡。它们可能会产生假阳性，但绝不会产生假阴性。它们非常适合需要节省内存并快速筛选出非成员的场景，但可能不适用于那些需要绝对确定性的应用。现在，让我们尝试用 Python 实现它们吧！

## 用 Python 实现布隆过滤器

以下是布隆过滤器的 Python 实现。

```py
import hashlib
from bitarray import bitarray

class BloomFilter:
    def __init__(self, size, hash_functions):
        """
        Initialize the Bloom Filter with a given size and number of hash functions.

        :param size: Size of the Bloom Filter (number of bits in the bit array).
        :param hash_functions: Number of hash functions to use.
        """
        self.size = size
        self.bit_array = bitarray(size)
        self.bit_array.setall(0)
        self.hash_functions = hash_functions

    def add(self, item):
        """
        Add an element to the Bloom Filter.

        :param item: The item to be added to the Bloom Filter.
        """
        for i in range(self.hash_functions):
            # Calculate the index using the hash function and update the corresponding bit to 1.
            index = self._hash_function(item, i) % self.size
            self.bit_array[index] = 1

    def contains(self, item):
        """
        Check if an element is likely to be in the Bloom Filter.

        :param item: The item to check for presence in the Bloom Filter.

        :return: True if the element is likely present, False otherwise.
        """
        for i in range(self.hash_functions):
            # Calculate the index using the hash function.
            index = self._hash_function(item, i) % self.size
            # If any corresponding bit is 0, the item is definitely not in the set.
            if not self.bit_array[index]:
                return False
        # If all corresponding bits are 1, the item is likely in the set.
        return True

    def _hash_function(self, item, index):
        """
        To compute the hash function for a given item and index.

        :param item: The item to be hashed.
        :param index: The index used to vary the input to the hash function.

        :return: An integer value obtained by hashing the concatenated string of item and index.
        """
        hash_object = hashlib.sha256()
        hash_object.update((str(item) + str(index)).encode('utf-8'))
        return int.from_bytes(hash_object.digest(), byteorder='big')

# Example usage:
size = 10  # Size of the Bloom Filter
hash_functions = 3  # Number of hash functions

bloom_filter = BloomFilter(size, hash_functions)

# Add elements to the Bloom Filter
bloom_filter.add("apple")
bloom_filter.add("banana")
bloom_filter.add("orange")

# Check if elements are present in the Bloom Filter
print(bloom_filter.contains("apple"))    # True
print(bloom_filter.contains("grape"))    # False (not added)
```

在上述代码中，我们使用了 bitarray 库来更高效地表示过滤器。你可以通过以下命令安装它：*pip3 install bitarray*。

BloomFilter 类通过指定的大小（表示内部位数组的位数）和使用的哈希函数数量进行初始化。该类具有将元素添加到过滤器中（add）和检查元素可能存在的（contains）方法。哈希函数方法是一个私有函数，基于 SHA-256 算法生成哈希值（它是一个不错的选择，因为它已知能产生均匀分布的输出）。

## 布隆过滤器：时间与空间复杂度分析

让我们也来做一个简短的时间和空间复杂度分析。在时间复杂度方面，添加元素（add 操作）和检查其可能存在（contains 操作）都是常数时间操作 — O(k)，其中 k 是哈希函数的数量。这是因为过滤器涉及多个哈希函数，每个哈希函数对整体过程贡献一个常数时间（假设哈希函数可以在常数时间内计算）。

在空间复杂度方面，布隆过滤器具有内存高效的特点，因为它们使用固定大小的位数组。所需的空间与数组的大小和使用的哈希函数数量成正比。空间复杂度是 O(m)，其中 m 是位数组的大小。当处理大型数据集时，空间效率尤为有利，因为过滤器可以用比其他数据结构少得多的内存表示一个元素集合。

## 布隆过滤器：数学分析

最后，谈到概率数据结构时，提出它们可能产生的误差界限变得至关重要，因为与确定性数据结构不同，它们并不是完美的。在这里，我们将通过一些数学推导来量化产生假阳性错误的概率，并利用这个概率来得出有意义的结论。

首先，假设我们有一个大小为 m 的位数组以及 k 个哈希函数。此外，我们假设哈希函数是均匀工作的，即对于任何给定的输入，它们选择位数组中任意 m 个位置的概率是相等的。那么，某个特定索引 i 被选中的概率是：

![](../Images/e80200507ce7dad84a05d8863c43a2f4.png)

因此，位数组中第 i 个索引未被选中的概率是：

![](../Images/e8ed8d08a2fc10072ddbf1a69f602c6a.png)

现在，我们引入k个哈希函数。我们假设它们每个都独立工作并产生一个输出。此外，假设n个元素已经插入到数组中。这意味着，哈希函数总共已独立使用了nk次。因此，第i个索引仍为空的概率为：

![](../Images/33e2cfe401208c6572672d72e75267c7.png)

因此，第i个索引被占用的概率现在是：

![](../Images/7e9c2161cbccc6035cd98e945f5346e7.png)

什么时候会出现假阳性？当所有元素返回的索引值都是1，即使该元素并不在集合中时，就会发生假阳性。当所有k个哈希函数生成的索引都被占用时，就会发生这种情况。再次根据哈希函数独立性的假设，这就变成了以下概率：

![](../Images/89250e768d46d0986904e7c3603fd322.png)

对于足够大的m值，我们可以将上述概率近似为一个更简单的表达式：

![](../Images/6c5c50cc8ccef57b8a0c48625db778fd.png)

我们能否用这个方法找到最小化ε的最优k值？当然，这是一个简单的微积分练习。我们只需要对上述函数关于k求导并令其为0。但在此之前，我们可以通过对两边取对数来简化该函数，

![](../Images/1ce2d350910f32f4293ac3c38f071448.png)

这给出了最小化假阳性率的k值。为了确保这是误差函数的最小值，我们理想情况下需要计算f(k)的二阶导数并验证我们的计算。为了简化起见，我们在这里省略了证明，但通过简单的微积分可以很容易地展示。接下来，使用k的最优值，我们可以找到误差函数的最小值：

![](../Images/652c08b0ad3c78c713fc89639bdc0a7b.png)

使用上述表达式，我们还可以求出m的最优值：

![](../Images/18611cdcf57b590e8ccf447ee95def6d.png)

好吧，这涉及了很多数学内容。让我们试着理解我们做了什么以及为什么这么做。首先，我们只是使用了一些简单的概率表达式，找出了在插入n个元素并使用k个哈希函数后，第i个索引位置仍然为空的概率。通过这个，我们得到了假阳性误差的概率，它是n、k和m的函数。然后，我们对k进行了最小化处理。使用这个最优的k值，我们找到了误差函数的最小值。求解这个值对于m来说，让我们能够确定位数组的最小大小，从而容忍给定的误差值。

让我们举一个例子来更好地理解这些结果。假设我们要实现一个布隆过滤器，并需要找到m和k的合适值。我们预计大约会插入200,000个元素，即n = 200000，而我们可以容忍的最大假阳性误差约为1%，即ε ̄ = 0.01。那么，m和k的最优值可以通过以下方式找到：

![](../Images/452b4f6caf8198282ecbb033bb760d25.png)

利用这些计算，我们可以优化原始的布隆过滤器实现：

```py
import math
import hashlib
from bitarray import bitarray

class OptimizedBloomFilter:
    def __init__(self, n=10000, p=0.05):
        """
        Initialize the Optimized Bloom Filter with dynamically calculated parameters.

        :param n: Expected number of elements to be added.
        :param p: Acceptable false positive rate.
        """
        self.n = n
        self.p = p
        self.m, self.k = self._calculate_parameters(n, p)

        self.bit_array = bitarray(self.m)
        self.bit_array.setall(0)

    def add(self, item):
        """
        Add an element to the Optimized Bloom Filter.

        :param item: The item to be added to the Bloom Filter.
        """
        for i in range(self.k):
            index = self._hash_function(item, i) % self.m
            self.bit_array[index] = 1

    def contains(self, item):
        """
        Check if an element is likely to be in the Optimized Bloom Filter.

        :param item: The item to check for presence in the Bloom Filter.

        :return: True if the element is likely present, False otherwise.
        """
        for i in range(self.k):
            index = self._hash_function(item, i) % self.m
            if not self.bit_array[index]:
                return False
        return True

    def _calculate_parameters(self, n, p):
        """
        To calculate the optimal parameters m and k based on n and p.

        :param n: Expected number of elements.
        :param p: Acceptable false positive rate.

        :return: Tuple (m, k) representing the optimal parameters.
        """
        m = - (n * math.log(p)) / (math.log(2) ** 2)
        k = (m / n) * math.log(2)
        return round(m), round(k)

    def _hash_function(self, item, index):
        """
        To compute the hash function for a given item and index.

        :param item: The item to be hashed.
        :param index: The index used to vary the input to the hash function.

        :return: An integer value obtained by hashing the concatenated string of item and index.
        """
        hash_object = hashlib.sha256()
        hash_object.update((str(item) + str(index)).encode('utf-8'))
        return int.from_bytes(hash_object.digest(), byteorder='big')

# Example usage:
expected_elements = 2000000
false_positive_rate = 0.01

optimized_bloom_filter = OptimizedBloomFilter(expected_elements, false_positive_rate)

# Add elements to the Optimized Bloom Filter
optimized_bloom_filter.add("apple")
optimized_bloom_filter.add("banana")
optimized_bloom_filter.add("orange")

# Check if elements are present in the Optimized Bloom Filter
print(optimized_bloom_filter.contains("apple"))    # True
print(optimized_bloom_filter.contains("grape"))    # False (not added)
```

在此实现中，`OptimizedBloomFilter`类根据提供的期望元素数量（n）和可接受的假阳性率（p）动态计算m和k的最佳值。`calculate parameters`方法负责执行此计算。其余实现与之前版本类似，优化后的参数提高了空间效率并最小化了假阳性的概率。

这就是我们关于布隆过滤器的讨论。希望你到目前为止阅读愉快！在下一节中，我们将讨论另一种重要的概率数据结构：计数最小草图（Count Min Sketch）。

# 计数最小草图（Count Min Sketch）

在本节中，我们将讨论另一种有趣的概率数据结构：计数最小草图。计数最小草图在某种程度上是布隆过滤器的扩展。就像哈希集合的概率对应物是布隆过滤器一样，多重集合的对应物就是计数最小草图。

多重集合本质上是一个集合，它还记录了元素在输入数据流中出现的频率或次数。例如，如果输入数据是[2, 3, 3, 4, 1, 1, 0, 1, 0]，则一个集合仅仅是唯一元素的集合，即{0, 1, 2, 3, 4}。但是，一个多重集合还会记录频率，即{0: 2, 1: 3, 2: 1, 3: 3, 4: 1}，可以根据需求进行查询。传统的哈希表使用与哈希集合相同的数据结构，但也记录了每个元素的频率计数。虽然它非常高效并且可以在O(1)时间内查询，但存储所有元素可能会有较大的内存开销，同时处理冲突时还会有额外的开销。

和以前一样，计数最小草图通过显著减少内存需求并去除处理冲突时的不必要计算开销，通常比确定性的哈希表更高效。但是，它必须付出准确性的代价，幸运的是，可以通过像以前一样调整m和k的值来解决这个问题。让我们更仔细地了解计数最小草图是如何工作的。

## 它们是如何工作的？

计数最小草图的核心思想是使用多个哈希函数将输入元素映射到一个计数器数组。该数组被组织为一个二维矩阵，包含m列和k行，每行对应一个哈希函数，列代表计数器。当数据流中遇到一个元素时，它会使用每个哈希函数进行哈希处理，并相应地增加矩阵中的计数器。由于哈希函数的性质，冲突是不可避免的。然而，使用多个哈希函数和二维矩阵可以使计数最小草图将冲突分布到不同的计数器中。这种分布有助于减少冲突对计数准确性的影响。

假设我们有 m = 5（列数）和 k = 2（行数）个哈希函数。这意味着我们只需要一个大小为 m×k = 5×2 的数组。最初，该数组的所有元素都是 0。接下来，我们将元素 2、3、2、2 和 1 添加到集合中。这个过程与布隆过滤器类似。我们对输入数据（待添加的元素）应用 k 个哈希函数，得到 k 个索引。假设 ji 是第 i 个哈希函数的输出。然后，我们将第 i 行的 jith 索引值加 1。下图展示了这个过程的具体操作：

![](../Images/e2f74d630ade158add500c26316a616f.png)

Count Min Sketch 示例 [作者提供的图片]

要估计一个元素的频率，需要查找与其哈希值对应的计数器，并选择其中的最小计数。这个最小计数提供了对真实频率的近似值。通过对多个元素重复这一过程并取最小计数，可以获得数据流中各个项的近似频率估计。例如，如果我们查询集合中数字 2 的频率，我们查看 3 和 4 的最小值，即 3，这实际上就是输入流中 2 的频率。类似地，数字 1 的频率是 1 和 4 的最小值，即 1，仍然是正确的输出。需要注意的是，Count Min Sketch 仅提供元素频率的最大边界。这个边界是通过算法如何将计数分配到不同计数器的方式得出的。可能会发生这样的情况：所有 k 个哈希函数对输入数据的输出对应的索引可能都会被递增，这并不是因为该元素本身的加入，而是由于该集合中的其他所有元素的影响。下方是一个示例，当我们向集合中添加 4 时的情况。

![](../Images/de2dd71a25353c509686e5b9429e3913.png)

Count Min Sketch 示例（假阳性）[作者提供的图片]

现在，如果我们尝试查询数字 4 的频率，我们查看 4 和 2 的最小值，即 2，这比实际的频率 1 要高。这是因为与哈希函数输出相关的索引已经被其他元素填充，发生了冲突。输出虽然不完美，但仍然是实际频率的上限。

如前所述，Count Min Sketch 矩阵中的参数 'm' 和 'k' 在平衡内存使用和准确性方面起着至关重要的作用。行数 'm' 决定了使用的哈希函数数量，而列数 'k' 则决定了每行的大小，从而影响所用内存量。调整这些参数可以让用户根据应用的具体要求控制空间效率和估计准确性之间的权衡。

## 在 Python 中实现 Count Min Sketch

以下是 Count Min Sketch 的 Python 实现。

```py
import hashlib

class CountMinSketch:
    def __init__(self, m, k):
        """
        Initialize the Count-Min Sketch with specified width and depth.

        :param width: Number of counters in each hash function's array.
        :param depth: Number of hash functions.
        """
        self.width = m
        self.depth = k
        self.counters = [[0] * m for _ in range(k)]

    def update(self, item, count=1):
        """
        Update the Count-Min Sketch with the occurrence of an item.

        :param item: The item to be counted.
        :param count: The count or frequency of the item (default is 1).
        """
        for i in range(self.depth):
            index = self._hash_function(item, i) % self.width
            self.counters[i][index] += count

    def estimate(self, item):
        """
        Estimate the count or frequency of an item in the Count-Min Sketch.

        :param item: The item to estimate the count for.

        :return: The estimated count of the item.
        """
        min_count = float('inf')
        for i in range(self.depth):
            index = self._hash_function(item, i) % self.width
            min_count = min(min_count, self.counters[i][index])
        return min_count

    def _hash_function(self, item, index):
        """
        To compute the hash function for a given item and index.

        :param item: The item to be hashed.
        :param index: The index used to vary the input to the hash function.

        :return: An integer value obtained by hashing the concatenated string of item and index.
        """
        hash_object = hashlib.sha256()
        hash_object.update((str(item) + str(index)).encode('utf-8'))
        return int.from_bytes(hash_object.digest(), byteorder='big')

# Example usage:
m = 100
k = 5

count_min_sketch = CountMinSketch(m, k)

# Update the sketch with occurrences of items
count_min_sketch.update("apple", 3)
count_min_sketch.update("banana", 5)
count_min_sketch.update("orange", 2)

# Estimate counts for items
print(count_min_sketch.estimate("apple"))    # Estimated count for "apple"
print(count_min_sketch.estimate("grape"))    # Estimated count for "grape" (not updated)
```

在上面的代码中，CountMinSketch类使用指定的m和k参数进行初始化，分别表示每个哈希函数数组中的计数器数目和哈希函数的数量。草图通过更新项目出现的次数来进行更新，估计的项目计数可以被获取。哈希函数方法负责生成哈希值，示例使用部分展示了如何使用计数最小草图以流式方式估算特定项目的计数。与布隆过滤器相似，哈希使用了SHA-256算法，因为它已知能够产生均匀分布的输出。

## 计数最小草图：时间与空间复杂度分析

让我们也快速分析一下计数最小草图的时间与空间复杂度。在时间复杂度方面，计数最小草图中元素的添加（递增计数器）和它们频率的估计都是常数时间操作：O(k)。常数因子受哈希函数数量（'k'）和矩阵大小（'m'）的影响。然而，假设哈希函数的评估可以在常数时间内完成，整体复杂度仍然是常数。操作涉及多个哈希函数，每个哈希函数对整体过程贡献一个常数时间。

关于空间复杂度，计数最小草图由于使用了紧凑的矩阵结构，具有较高的内存效率。所需的空间与行数（'m'）和每行计数器数（'k'）的乘积成正比。因此，空间复杂度可以表示为O(m * k)。我们可以调整'm'和'k'的值，以在空间效率和估计精度之间找到平衡。较小的值可以减少内存使用，但可能导致更多的估计误差，而较大的值则提高精度，但会增加内存需求。这种可调性使得计数最小草图成为一种在内存受限且可接受近似频率计数的场景下非常灵活的解决方案。

## 计数最小草图：数学原理

如前所述，利用我们对概率论的知识，我们可以优化计数最小草图，即找到最小化错误可能性的m和k的值。首先，让我们正式化符号。令hat-fₓ表示元素x的频率估计值，fₓ表示该元素的实际频率。根据我们之前的讨论，我们必须满足：

![](../Images/930919c0257cbf1e2b2f1b63d93ff125.png)

即，我们始终可以确信我们的估计值至少等于实际的频率计数。但仅此足够吗？一个始终输出非常大数字的愚蠢算法也可以实现这一点。使计数最小草图如此特别的是，我们不仅可以对实际频率fₓ进行上界限制，还可以对估计频率hat-fₓ与实际频率之间的偏差进行上界限制（当然是概率性的）。通过使用最优的m和k值，我们可以保证以下几点：

![](../Images/137f0ee281a9576a8c9fb6a9d348f661.png)

即，估计频率超过实际频率超过或等于εn（n是输入流的大小）的概率至少为(1 − δ)，其中ε和δ可以按需取足够小。满足此条件的m和k的值如下：

![](../Images/cd4813fc28efb1f0a71352535e2f74cb.png)

如我们所见，列数（m）控制最大偏差值（hat-fₓ − fₓ），而行数/哈希函数数（k）则控制概率保证1 − δ。这个证明相当复杂，需要一些中间的概率理论知识，如随机变量和马尔科夫不等式。我将在接下来的段落中详细讲解这个证明。如果你感兴趣并且具备必要的前置知识，可以继续阅读。

所以，让我们从证明开始。首先，我们定义一些量：令αᵢ, ₕᵢ₍ₓ₎表示第i行中的值，hᵢ(x)表示计数最小草图数组的列。这仅仅是根据第i行/第i个哈希函数对x的频率估计。这意味着估计频率只是所有行中该值的最小值：

![](../Images/816700ab8f3054b96763e00135ada35f.png)

我们现在定义随机变量Zᵢ（称为超计数）如下：

![](../Images/cfda04f9b26574ad76ebbc97c8d645e9.png)

接下来，我们定义一个指示随机变量Xᵢ,ᵧ，检查在第i行中是否与其他元素y ≠ x发生碰撞。形式上，

![](../Images/4dae31722d2ff1cdf4c9f193755fe928.png)

现在，让我们思考Zᵢ和Xᵢ,ᵧ之间的关系。回想一下，Zᵢ是超计数，即αᵢ, ₕᵢ₍ₓ₎超过实际频率fₓ的值。这什么时候发生呢？当与元素y ≠ x发生碰撞时，就会发生这种情况。这让我们能够定义以下关系：

![](../Images/4a7c559b4e11d039caec84d056d2d5b1.png)

请注意，由于元素可能多次出现，每次都会导致超计数增加，我们已经用y的实际频率估计对总和进行了加权。下一步涉及使用期望的性质来找到Zᵢ或超计数的期望值：

![](../Images/61c49c4da41ce40dc16335d536be62d7.png)

其中n表示输入流的预期长度，它大于或等于所有其他元素频率之和。最后，我们使用马尔科夫不等式来找到Zᵢ超过其期望值并乘以常数的概率：

![](../Images/3d80bdbe1caa09ee8ccd0a529425515a.png)

上述只是直接使用了马尔科夫不等式。现在我们可以利用E [Zᵢ] ≤ n/m的事实得到：

![](../Images/44fa4d57df89fc32e9bffee19a71aa86.png)

回想一下我们想要证明的表达式。我们只希望在不等式右侧的概率项中出现εn。目前我们有a/m。取a = mε可以得到这一项。因此，

![](../Images/3aa530c03256b0ee476535a955e98415.png)

现在我们代入m = e/ε来得到：

![](../Images/070aab6abb8b36d25b182ad432ef9cb3.png)

上述内容对所有Zᵢ（i = 1到i = k）都必须成立。因此，我们可以写出：

![](../Images/bdc29310badf3cceb511d254432eba55.png)

由于上述表达式对所有i成立，因此它也必须对最小值成立：

![](../Images/878f45a3df2d2327e196d2935615c338.png)

最后，我们取上述概率的补集并代入k的值：

![](../Images/8038942a83fefb97db26aaada0973176.png)

这完成了证明。从中我们可以得出结论，为了在概率保证1 − δ下实现最大偏差为εn，我们需要以下条件：

![](../Images/576644e30b9173c9941a9d03ea1af174.png)

使用这些计算方法，我们可以优化我们原始的count min sketch实现：

```py
import hashlib
import math

class OptimizedCountMinSketch:
    def __init__(self, epsilon, delta):
        """
        Initialize the Count-Min Sketch with specified width and depth.

        :param epsilon: Quantifies the max deviation from true count (epsilon * n)
        :param delta: To compute the probabilistic guarantee
        """
        self.epsilon = epsilon
        self.delta = delta
        self.width, self.depth = self._calculate_parameters(epsilon, delta)
        self.counters = [[0] * self.width for _ in range(self.depth)]

    def update(self, item, count=1):
        """
        Update the Count-Min Sketch with the occurrence of an item.

        :param item: The item to be counted.
        :param count: The count or frequency of the item (default is 1).
        """
        for i in range(self.depth):
            index = self._hash_function(item, i) % self.width
            self.counters[i][index] += count

    def estimate(self, item):
        """
        Estimate the count or frequency of an item in the Count-Min Sketch.

        :param item: The item to estimate the count for.

        :return: The estimated count of the item.
        """
        min_count = float('inf')
        for i in range(self.depth):
            index = self._hash_function(item, i) % self.width
            min_count = min(min_count, self.counters[i][index])
        return min_count

    def _calculate_parameters(self, epsilon, delta):
        """
        To calculate the optimal parameters m and k based on s and e.

        :param epsilon: Quantifies the max deviation from true count (epsilon * n)
        :param delta: To compute the probabilistic guarantee

        :return: Tuple (m, k) representing the optimal parameters.
        """
        m = math.ceil(math.e/epsilon)
        k = math.ceil(math.log(1/delta))
        return m, k

    def _hash_function(self, item, index):
        """
        To compute the hash function for a given item and index.

        :param item: The item to be hashed.
        :param index: The index used to vary the input to the hash function.

        :return: An integer value obtained by hashing the concatenated string of item and index.
        """
        hash_object = hashlib.sha256()
        hash_object.update((str(item) + str(index)).encode('utf-8'))
        return int.from_bytes(hash_object.digest(), byteorder='big')

# Example usage:
eps = 0.01
delta = 0.05

optimized_count_min_sketch = OptimizedCountMinSketch(eps, delta)

# Update the sketch with occurrences of items
optimized_count_min_sketch.update("apple", 3)
optimized_count_min_sketch.update("banana", 5)
optimized_count_min_sketch.update("orange", 2)

# Estimate counts for items
print(count_min_sketch.estimate("apple"))    # Estimated count for "apple"
print(count_min_sketch.estimate("grape"))    # Estimated count for "grape" (not updated)
```

在这个实现中，OptimizedCountMinSketch类根据提供的最大偏差容忍度（epsilon）和概率要求（delta）动态计算m和k的最优值。calculate parameters方法处理这个计算。其余实现与之前版本相似，优化后的参数实现了所需的保证。

这就结束了我们关于Count Min Sketch的讨论。

# 结论

算法分析之父唐纳德·克努斯曾说过，

> “算法必须通过观察才能相信”。

事实上，理解一个算法的能力不仅来源于使用它，还来源于理解它如何工作以及为什么能工作。在本文中，我尝试描述了两个重要的概率数据结构及其算法的内部工作原理。我们讨论了它们相对于确定性算法的性能和实现，并使用一些数学方法量化了它们的误差边界。尽管这些数据结构在文献中非常重要，但它们仅仅构成了其他概率数据结构（如跳表、超对数哈希、treap、商数滤波器、最小哈希等）海洋的表面。许多这些数据结构非常有趣，并在计算机科学领域有着广泛的潜在应用。我希望在后续的文章中进一步详细探讨其中的一些。

希望你喜欢阅读这篇文章！如果你有任何疑问或建议，请在评论框中回复。也可以通过[邮件](mailto:naman.agr03@gmail.com)与我联系。

如果你喜欢我的文章并想阅读更多，请关注我。

**注意：** 所有图像（封面图像除外）均由作者制作。

# 参考文献

1.  Bloom filter — Wikipedia — en.wikipedia.org. [https://en.wikipedia.org/wiki/Bloom_filter.](https://en.wikipedia.org/wiki/Bloom_filter.) [访问日期：2024年1月7日]。

1.  Bloom Filters — Introduction and Implementation — GeeksforGeeks — geeksforgeeks.org. [https://www.](https://www.) geeksforgeeks.org/bloom-filters-introduction-and-python-implementation/. [访问日期：2024年1月7日]。

1.  Count-Min Sketch 数据结构与实现 — GeeksforGeeks — geeksforgeeks.org. [https://www.](https://www.) geeksforgeeks.org/count-min-sketch-in-java-with-examples/. [访问日期 2024年1月7日]。

1.  Counting Bloom Filters — 介绍与实现 — GeeksforGeeks — geeksforgeeks.org. [https://www.](https://www.) geeksforgeeks.org/counting-bloom-filters-introduction-and-implementation/. [访问日期 2024年1月7日]。

1.  Count–min sketch — 维基百科 — en.wikipedia.org. [https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch.](https://en.wikipedia.org/wiki/Count%E2%80%93min_sketch.) [访问日期 2024年1月7日]。

1.  Humberto Villalta. Bloom Filter 数学证明 — humberto521336\. [https://medium.com/@humberto521336/](https://medium.com/@humberto521336/) bloom-filters-mathematical-proof-8aa2e5d7b06b. [访问日期 2024年1月7日]。
