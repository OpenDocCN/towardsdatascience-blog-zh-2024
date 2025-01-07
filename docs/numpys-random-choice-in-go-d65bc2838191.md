# Numpy 的随机选择在 Go 语言中的实现

> 原文：[https://towardsdatascience.com/numpys-random-choice-in-go-d65bc2838191?source=collection_archive---------12-----------------------#2024-01-23](https://towardsdatascience.com/numpys-random-choice-in-go-d65bc2838191?source=collection_archive---------12-----------------------#2024-01-23)

[](https://mlefarov.medium.com/?source=post_page---byline--d65bc2838191--------------------------------)[![Max Lefarov](../Images/cb8c4df2e894bf4957f0206ee3df7e5f.png)](https://mlefarov.medium.com/?source=post_page---byline--d65bc2838191--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d65bc2838191--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d65bc2838191--------------------------------) [Max Lefarov](https://mlefarov.medium.com/?source=post_page---byline--d65bc2838191--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d65bc2838191--------------------------------) ·阅读时间 5 分钟·2024年1月23日

--

![](../Images/a8b5ef60ccf65bdd8b82b5bb8edf3698.png)

由 ChatGPT 生成

最近我帮助实现了一些 Java 逻辑，这些逻辑本可以通过简单调用 Numpy 的`random.choice`来实现。这最终成了一个任务，让我有机会去深入了解那些你每天都在使用，但从未真正有时间完全理解其工作原理的东西。同时，我也有一段时间想开始学习 Go，那么为什么不一举两得，再次用 Go 实现 `random.choice` 呢？

`random.choice` 允许我们根据指定的概率从提供的集合中采样 N 个元素。重要的是（对于激励这项工作的使用案例），它允许我们进行无重复的采样。也就是说，如果集合中的一个元素已经被采样，它将不会再次被采样。例如，如果我们有一个集合 [A, B, C]，其对应的概率为 [0.1, 0.7, 0.2]，并且我们想无重复地采样 3 个元素，那么大多数时候我们会得到 [B, C, A] 作为输出。如果我们进行有重复的采样，预期的输出将是 [B, B, B]。

首先，让我们定义 Go 函数的签名。我们希望它尽可能与 Numpy 的对应函数保持一致。

```py
func Choice[T any](
 arr []T,
 size int,
 replace bool,
 probs []float64,
 rng *rand.Rand,
) ([]T, error) {}
```

关于函数签名，有几点需要注意：

+   我们使用了泛型类型 T。这允许我们对不同类型的数组调用该函数（只要它满足类型约束，而在我们这个例子中并没有类型约束）。这应该模仿 Python 中 `random.choice` 的语义，也就是说，它不关心输入数组中元素的类型。

+   我们还传递了一个指向随机数生成器（rng）对象的指针，供我们在抽样时使用。我从Jax中学到了这种定义随机函数的风格（与直接访问全局rng实例相比）。根据我的经验，这简化了测试和可重复性。

+   这个函数有两个返回值，一个是样本数组，另一个是`error`类型。这是Go语言中处理“异常”执行流的方式（Go没有断言或异常）。

现在我们需要弄清楚如何使用仅在[0, 1]之间均匀抽样的浮动随机数（由rng返回）从由`probs`参数定义的离散概率分布中抽取元素。幸运的是，有一种方法可以完美解决这个问题。

# CDF反演法

首先，CDF代表累积分布函数。在离散情况下，它可以表示为一个数组，其中索引`i`处的元素等于所有输入概率在位置`i`及之前的累计和。让我们用一个简单的帮助函数来实现这个公式。

```py
func CDF(probs []float64) []float64 {
 cdf := make([]float64, len(probs))
 cum := 0.0

 for i := range cdf {
  cum += probs[i]
  cdf[i] = cum
 }

 return cdf
}
```

通过离散概率分布的CDF和随机数生成器，我们可以通过以下方式从输入集合中抽取元素：

1.  从统一分布中随机抽取一个介于[0, 1]之间的浮动数值。

1.  找到第一个CDF值大于等于随机浮动数的索引。

1.  返回原集合中该索引位置的元素。

为了理解它为何有效，我们可以进行一个简单的视觉实验。我们可以把CDF数组中的值看作是放置在区间[0, 1]上每个箱子的右边界。每个箱子的宽度与输入的概率成正比。当生成介于[0, 1]之间的均匀随机浮动数时，我们可以把它看作是将球随机投掷到这个区间，并选择我们撞到的箱子。撞到箱子的概率正好与输入概率成正比（这正是我们需要的）。下面是我们最后一个例子——集合[A, B, C]及其相关概率[0.1, 0.7, 0.2]的视觉示范。

![](../Images/9a22c4e1bb87c7c5af9188bff8d5a420.png)

由作者在Excalidraw中创建

要获得箱子的索引，我们可以返回第一个右边界大于或等于抽样值的索引。同样，这里有一个简单的帮助函数来实现这个功能：

```py
func FindIndexFromRight(val float64, cdf []float64) int {
 for i, cumProb := range cdf {
  if cumProb >= val {
   return i
  }
 }

 return len(cdf) - 1
}
```

# 将所有内容整合在一起

这样，我们就具备了实现带有重复的`random.choice`所需的一切。无重复抽样则需要多一个技巧。为了确保我们不会抽取已经被抽取过的元素，可以在抽样后将其概率置为0。然而，这样会使我们的离散概率分布失效，因为其总和将不再等于1。为了修正这一点，我们需要通过将概率除以新的总和来重新归一化概率。作为额外的优化，我们可以直接对CDF进行重新归一化，而不需要先重新归一化输入概率再计算CDF。将所有内容整合在一起：

```py
func Choice[T any](
 arr []T,
 size int,
 replace bool,
 probs []float64,
 rng *rand.Rand,
) ([]T, error) {
 if !replace && (size > len(arr)) {
  return nil, errors.New("cannot sample more than array size without replacements")
 }

 samples := make([]T, size)
 probsCopy := make([]float64, len(probs))
 copy(probsCopy, probs)

 for i := 0; i < size; i++ {
  cdf := CDF(probsCopy)

  if !replace {
   total := cdf[len(cdf)-1]
   for cdfInd := range cdf {
    cdf[cdfInd] /= total
   }
  }

  randFloat := rng.Float64()
  sampledIndex := FindIndexFromRight(randFloat, cdf)
  samples[i] = arr[sampledIndex]

  if !replace {
   probsCopy[sampledIndex] = 0.0
  }
 }

 return samples, nil
}
```

再次提醒几个需要注意的事项：

+   如果我们进行不放回抽样，就无法抽取超过输入集合初始大小的样本。

+   Go 传递“切片”（没有定义大小的数组）作为可变参数。因此，我们复制输入的概率，以避免用掩码修改原始数组。

驱动代码如下：

```py
rngSource := rand.NewSource(time.Now().UnixNano())
rng := rand.New(rngSource)

arr := []string{"A", "B", "C", "D"}
probs := []float64{0.1, 0.6, 0.2, 0.1}

samples, err := Choice(arr, 3, false, probs, rng)
if err != nil {
 log.Fatal(err)
}

fmt.Println("Samples: ", samples)
```

就是这样，随时可以在评论区提问。
