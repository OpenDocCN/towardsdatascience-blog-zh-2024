# 为什么集合在编程中如此有用

> 原文：[https://towardsdatascience.com/why-sets-are-so-useful-in-programming-b1157532f7e5?source=collection_archive---------6-----------------------#2024-12-19](https://towardsdatascience.com/why-sets-are-so-useful-in-programming-b1157532f7e5?source=collection_archive---------6-----------------------#2024-12-19)

## 以及如何利用它们提升代码性能

[](https://medium.com/@afjal.nc?source=post_page---byline--b1157532f7e5--------------------------------)[![Afjal Chowdhury](../Images/92814d13296326ea8dc7d5a64a443092.png)](https://medium.com/@afjal.nc?source=post_page---byline--b1157532f7e5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b1157532f7e5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b1157532f7e5--------------------------------) [Afjal Chowdhury](https://medium.com/@afjal.nc?source=post_page---byline--b1157532f7e5--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b1157532f7e5--------------------------------) ·8分钟阅读·2024年12月19日

--

一个*集合*是一个简单的结构，定义为*不同元素的集合*。集合通常在数学或逻辑领域中出现，但在编程中，它们也非常有用，可以编写高效的代码。在本文中，我详细介绍了集合在某些情况下优于其他数据类型，如*列表*，以及集合的底层实现，使它们对程序员非常有用。

![](../Images/aa841073ef120a63e6fbafcfd823d962.png)

由[Robert Ruggiero](https://unsplash.com/@robert2301?utm_source=medium&utm_medium=referral)拍摄的组成集合的螺栓照片，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

集合是容器的一种示例；它们用于在一个变量下存储多个元素。在寻找容器数据类型时，列表（通常用方括号 [ ] 定义）是首选，它在几乎所有编程语言中都有广泛应用。集合与列表有很多相似之处，最显著的是它们都是**动态**的，允许根据需要增长或缩小大小。唯一的区别在于，列表会保留元素的顺序并允许重复，而集合则不保留顺序，也不允许重复，这在某些场景中提供了独特的优势。了解何时选择集合而非列表，可以大大提高程序的性能并改善代码的可读性。

在 Python 中，集合可以使用花括号声明，或者使用集合构造函数来声明。

```py
set_a = {1, 4, 9, 16}
set_b = set([1,2,3]) #coverting list to set
empty_set = set()
```

# 集合不包含重复元素

集合的一个定义特征是每个元素都是独特的，因此没有重复的条目。一个简单的应用例子是通过将列表转换为集合来**移除所有重复条目**。

```py
#LIST Implementation O(n^2)
def remove_duplicate_entries(list input_list):
    output_list = []
    for element in input_list:
        if element in output_list: 
             output_list.append(element)
    return output_list
```

```py
#SET Implementation O(n)
def remove_duplicate_entries(list input_list):
    return list(set(input_list))
```

因为这里使用了集合，列表的顺序没有被保留。如果顺序很重要，可以使用像第一个实现那样的实现。

下一个函数**用于识别列表中是否包含重复元素**；元素的顺序在这里并不重要，重要的是*重复出现的次数*。集合的实现更为理想，因为集合上的操作通常比列表要快得多（这将在后面更详细地解释）。

```py
#LIST Implementation
def has_duplicates(list input_list):
    unique_elements = []
    for element in input_list:
        if element in unique_elements: 
             return True
        else:
             unique_elements.append(element)
    return False
```

```py
#SET Implementation
def has_duplicates(list input_list): 
    return len(input_list) != len(set(input_list))
```

*注意：*在存在重复元素较早出现的情况下，第一个实现可能会比第二个更高效，因为它能较早地捕捉到重复并返回`False`，无需检查每个条目，而第二个实现则总是遍历每一个元素。一个更优的解决方案可以采用与第一个实现相似的方法，但使用* ***集合*** *作为唯一元素容器。*

```py
def has_duplicates(list input_list) -> bool:
    unique_elements = set() #**modified here
    for element in input_list:
        if element in unique_elements: 
             return True
        else:
             unique_elements.add(element)
    return False
```

即使这些应用看起来相当粗糙或简单，但以“去除重复元素”为基础的无数用例需要一些创意才能看出。例如，[Leetcode 1832](https://leetcode.com/problems/check-if-the-sentence-is-pangram/) “*检查句子是否是回文*”- 你需要检测一个输入句子中是否使用了所有26个字母- 就可以优雅地使用集合推导来解决。

```py
def is_pangram(str sentence) -> bool:
    present_letters = {letter for letter in sentence}
    return len(present_letters) == 26
```

如果没有集合，这个问题就需要使用嵌套循环来处理，而嵌套循环不仅更难编写，而且效率较低，复杂度为O(n^2)。

识别集合有用场景的关键在于考虑：

+   如果我们**只关心出现的次数**，比如在本问题中，如果句子中包含单词“eel”，我们只关心字母“e”是否出现，而不在乎它是否出现两次，因此我们可以丢弃第二个及之后的“e”。

+   基于这一原理，在处理**非常大的**列表时，可能希望去除重复元素，以便继续处理更小的列表。

+   如果**我们不关心元素的顺序**，那么可以使用集合。在需要保持顺序的场景中，比如优先队列，应该使用列表。

# 哈希映射和缺乏顺序

倒退一步来看，可能会觉得使用集合的唯一好处就是简化重复元素的去除。我们之前讨论过集合没有顺序；数组有索引元素，可以简单地忽略它们并当作集合处理。看起来数组可以像集合一样完成任务，甚至做得更多。

然而，集合强制的这一简化方式带来了**不同的底层实现**。在列表中，元素通过索引来分配位置，从而确定每个元素在顺序中的位置。而集合不需要分配索引，因此它们采用了一种不同的引用方法：哈希映射。这些操作通过（伪）随机地为元素分配地址，而不是将元素顺序存储。地址的分配由哈希函数控制，哈希函数以元素为输入，输出一个地址。

![](../Images/48360d958818c5df76f7a6097855dfa1.png)

哈希表不保留顺序。

*H(x) 是确定性的，因此相同的输入总是会给出相同的输出，即 H 函数内部没有随机数生成（RNG），所以在这种情况下 H(4) 总是等于 6。*

执行此函数所需的时间与集合的大小无关，即哈希的时间复杂度是 O(1)。这意味着哈希的时间消耗与列表的大小无关，始终保持在一个常数、快速的速度。

# 集合操作

由于哈希通常非常快速，许多在大数组上通常较慢的操作可以在集合上高效执行。

## **搜索或成员测试**

在数组中搜索元素使用的是一种叫做*线性搜索*的算法，它通过逐一检查列表中的每个项来完成。在最坏情况下，如果要查找的元素不在列表中，算法将遍历整个列表的每个元素（*O(n)*）。在一个非常大的列表中，这个过程会花费很长时间。

![](../Images/57ec2db048f934d327520f813422a9b5.png)

线性搜索平均需要 n/2 次操作，图像由作者提供

然而，由于哈希是 O(1)，Python 将要查找的元素哈希处理，然后要么返回该元素在内存中的位置，要么返回该元素不存在——这一过程所需时间非常短。

```py
number_list = range(random.randint(1,000,000))
number_set = set(number_list)

#Line 1
#BEGIN TIMER
print(-1 in number_list)
#END TIMER

#Line 2
#BEGIN TIMER
print(-1 in number_set)
#END TIMER 
```

![](../Images/ee09a8ba99f1baca735980c4270738d5.png)

列表与集合中的搜索时间对比

*注意：使用哈希表进行搜索的* ***摊销*** *时间复杂度是 O(1)。这意味着在平均情况下，它的运行时间是常数时间，但从技术上讲，在最坏情况下，搜索是 O(n)。然而，这种情况极不可能发生，只有当哈希实现发生碰撞时才会出现碰撞，也就是当哈希表/集合中的多个元素被哈希到同一个地址时。*

![](../Images/7afe24366b2ac3a3495f2dedd5b2bdb5.png)

碰撞是罕见的

## 删除

从列表中删除一个元素，首先需要搜索以定位该元素，然后通过清除地址来移除对该元素的引用。在数组中，经过 O(n) 时间的搜索后，每个删除元素后面的元素的索引都需要向下移动一个位置。这本身是另一个 O(n) 的过程。

![](../Images/96a22105c0fd32bd07e8390552b1013c.png)

在列表中删除元素大约需要 n 次操作

从集合中删除元素涉及 O(1) 查找，然后是删除内存地址，这也是一个 O(1) 的过程，因此删除操作也是常数时间操作。集合还有更多删除元素的方法，这样可以避免错误发生，或者能够简洁地删除多个元素。

```py
#LIST
numbers = [1, 3, 4, 7, 8, 11]

numbers.remove(4)
numbers.remove(5) #Raises ERROR as 5 is not in list
numbers.pop(0) #Deletes number at index 0, ie. 1

#SET
numbers = {1, 3, 4, 7, 8, 11}

numbers.remove(4)
numbers.remove(5) #Raises ERROR as 5 is not in set
numbers.discard(5) #Does not raise error if 5 is not in the set
numbers -= {1,2,3} #Performs set difference, ie. 1, 3 are discarded 
```

## 插入

向列表追加元素和向集合添加元素都是常数操作；然而，向列表中的指定索引位置添加元素（.insert）则需要额外的时间来移动元素。

```py
num_list = [1,2,3]
num_set = {1,2,3}

num_list.append(4)
num_set.add(4)

num_list += [5,6,7]
num_set += {5,6,7} 
```

## 高级集合操作

此外，所有可以在集合上执行的数学操作，在 Python 中也有实现。这些操作对于列表来说执行起来非常耗时，而使用哈希则再次进行了优化。

![](../Images/02016ae2904e6d68483df2cfe84bde21.png)

集合操作

```py
A = {1, 2, 3, 5, 8, 13}
B = {2, 3, 5, 7, 13, 17}

# A n B
AintersectB = A & B
# A U B
AunionB = A | B
# A \ B
AminusB = A - B
# A U B - A n B or A Delta B
AsymmetricdiffB = A ^ B 
```

这还包括比较运算符，即适当的和放宽的子集与超集。这些操作再次比其列表对应操作快得多，运行时间为O(n)，其中n是两个集合中的较大者。

![](../Images/3f8510d9a0ffb98f79661f84c025e805.png)

子集

```py
A <= B #A is a proper subset of B
A > B #A is a superset of B 
```

## Frozen Sets

Python中的一个最终小特性，但被低估的特性是*frozen set*，本质上是一个只读或**不可变**的集合。这些集合提供了更高的内存效率，并且在频繁测试元组中的成员关系时非常有用。

## 结论

使用集合来提升性能的精髓可以通过**通过减少优化**的原则来概括。

![](../Images/48008eb14526a7d051fad8eafbbecf75.png)

像列表这样的数据结构功能最强——它们是可索引的且动态的——但代价是效率相对较低：无论是在速度还是内存方面。识别哪些特性是必需的，哪些是未使用的，从而决定使用哪种数据类型，将使代码运行更快，且更易于阅读。

*所有技术图表由作者提供。*
