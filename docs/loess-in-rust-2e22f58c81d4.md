# Rust中的LOESS

> 原文：[https://towardsdatascience.com/loess-in-rust-2e22f58c81d4?source=collection_archive---------7-----------------------#2024-08-12](https://towardsdatascience.com/loess-in-rust-2e22f58c81d4?source=collection_archive---------7-----------------------#2024-08-12)

## 是时候将Python中的LOESS代码移植到Rust了。

[](https://medium.com/@joao.figueira?source=post_page---byline--2e22f58c81d4--------------------------------)[![João Paulo Figueira](../Images/54e4176f66e4ab0324d86ec71d8b033d.png)](https://medium.com/@joao.figueira?source=post_page---byline--2e22f58c81d4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2e22f58c81d4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2e22f58c81d4--------------------------------) [João Paulo Figueira](https://medium.com/@joao.figueira?source=post_page---byline--2e22f58c81d4--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e22f58c81d4--------------------------------) ·阅读时长5分钟·2024年8月12日

--

![](../Images/199267c891b0dc1dd794f992bbc5a653.png)

摄影：[Matt Foxx](https://unsplash.com/@foxxmd?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

五年前，从本文写作之时算起，我在Medium上发布了我最成功的文章。那篇文章源于一个需求：从车载远程信息处理数据流中过滤一个特别嘈杂的传感器数据。具体来说，它是连接到卡车传动轴的扭矩传感器，需要去除噪声。LOESS是解决方案，因此写了那篇文章。

[](/loess-373d43b03564?source=post_page-----2e22f58c81d4--------------------------------) [## LOESS

### 使用局部回归平滑数据

towardsdatascience.com](/loess-373d43b03564?source=post_page-----2e22f58c81d4--------------------------------)

到那时，我已经深陷于Python的世界，并且该项目需要使用Spark，因此在Python中实现算法是毫不犹豫的选择。然而，随着时间的推移，我现在更多使用Rust，并决定尝试将旧代码移植过来。本文描述了移植过程以及我在重写代码时的选择。你应该阅读原始文章和参考材料，进一步了解算法。这里，我们将重点讨论在Rust中编写矩阵代码的细节，尽可能地替代之前的[NumPy](https://numpy.org/)实现。

# Rust数值计算

作为一个坚定的反对重复造轮子的人，我寻找了推荐的Rust包来替代我在原始Python代码中使用的[NumPy](https://numpy.org/)，很快就找到了[**nalgebra**](https://nalgebra.org/)。

> **nalgebra** 旨在成为一个通用的低维线性代数库，提供一套针对计算机图形学和物理学优化的工具集。

尽管我们不会做任何物理学或计算机图形学相关的工作，但我们符合低维度的要求，简直是量体裁衣。

## 差异

当将 Python 代码转换为 Rust 时，我遇到了一些困难，花了一些时间才理清楚。在 Python 中使用 NumPy 时，我们可以利用语言和库提供的所有特性来提高代码的表达性和可读性。Rust 比 Python 更冗长，而且在撰写本文时（版本 0.33.0），**nalgebra** crate 仍然缺少一些有助于提高表达性的特性。简洁性是一个挑战。

我的第一个障碍是使用其他数组对数组进行索引。使用 NumPy 时，我们可以用另一个整数或布尔值数组对数组进行索引。在第一种情况下，索引数组的每个元素都是源数组的索引，索引数组的维度可以等于或小于数据数组。在布尔索引的情况下，索引数组的大小必须与数据数组相同，每个元素必须表示是否包含对应的数据元素。在使用布尔表达式选择数据时，这个特性非常方便。

尽管如此，我在整个 Python 代码中都使用了这个特性：

```py
# Python
xx = self.n_xx[min_range]
```

在这里，`min_range` 变量是一个整数数组，包含了从 `self.n_xx` 数组中检索的索引子集。

尽管我尽力而为，但我未能在 Rust crate 中找到模仿 NumPy 索引的方法，因此我不得不自己实现一个。在尝试和基准测试几次之后，我得到了最终的版本。这个解决方案直接而有效。

```py
// Rust
fn select_indices(values: &DVector<f64>,
                  indices: &DVector<usize>) -> DVector<f64> {
    indices.map(|i| values[i])
}
```

map 表达式非常简单，但使用函数名更具表达性，因此我将上面的 Python 代码替换为相应的 Rust 代码：

```py
// Rust
let xx = select_indices(&self.xx, min_range);
```

另外，也没有内置方法从整数范围创建向量。虽然使用 **nalgebra** 很容易做到，但代码会稍微长一些：

```py
// Rust
range = DVector::<usize>::from_iterator(window, 0..window);
```

如果我们在编译期间固定了向量和数组的大小，可以避免很多这种繁琐的操作，但由于维度未知，这里我们无法做到。对应的 Python 代码要简洁得多：

```py
# Python
np.arange(0, window)
```

这种简洁性也延伸到其他领域，例如按行填充矩阵。在 Python 中，我们可以这样做：

```py
# Python
for i in range(1, degree + 1):
    xm[:, i] = np.power(self.n_xx[min_range], i)
```

在撰写本文时，我发现用 **nalgebra** 做同样的事情，没有比这个更好的方法了：

```py
// Rust
for i in 1..=degree {
    for j in 0..window {
        xm[(j, i)] = self.xx[min_range[j]].powi(i as i32);
    }
}
```

也许包中隐藏着某些东西，等待被发现，从而帮助我们提高简洁性。

最后，我发现 **nalgebra** 的文档相对稀少。我们可以理解这一点，因为这是一个相对年轻的 Rust crate，未来充满了潜力。

## 优势

最精彩的部分在最后——原始性能。我邀请你尝试运行两种版本的相同代码（GitHub 仓库链接见下方），并比较它们的性能。在我的 2019 年款 2.6 GHz 6 核 Intel Core i7 MacBook Pro 上，Rust 代码的 *release* 版本运行时间不到 200 *微秒*，而 Python 代码的运行时间不到 5 *毫秒*。

# 结论

这个项目是我将旧代码从 Python 转移到 Rust 的另一个令人兴奋且富有教育意义的过程。尽管从著名的 Python 控制结构转换到 Rust 正变得越来越容易，但从 **NumPy** 转换到 **nalgebra** 的过程更加具有挑战性。Rust 包展现了很大的潜力，但仍需要更多的文档和在线支持。我热烈欢迎更为详细的用户指南。

Rust 比 Python 更加正式，但在适当使用时性能更好。在构建原型和发现阶段，我将继续使用 Python 进行日常工作，但在进入生产阶段时，我会转向 Rust 以获得更好的性能和内存安全性。我们甚至可以通过像[PyO3](https://pyo3.rs/v0.15.1/)这样的 crate 混合使用两者，所以这是一个双赢的局面。

*Rust 真棒！*

# 参考文献

[joaofig/loess-rs: 一个在 Rust 中实现的 LOESS / LOWESS 算法](https://github.com/joaofig/loess-rs)

[joaofig/pyloess: 使用 numpy 实现的简单 LOESS 算法](https://github.com/joaofig/pyloess)

# 赞扬

我使用了[Grammarly](https://app.grammarly.com/)来审查写作，并接受了它的一些重写建议。

[JetBrains 的 AI](https://www.jetbrains.com/ai/) 助手帮助我编写了一部分代码，我也用它来学习 Rust。它已经成为我日常工作中处理 Rust 和 Python 的重要工具。不幸的是，对 **nalgebra** 的支持仍然不足。

João Paulo Figueira 是位于葡萄牙里斯本的 [tb.lx by Daimler Truck](https://tblx.io/) 的数据科学家。
