# GPU 加速 Polars — 直观且详尽的解释

> 原文：[`towardsdatascience.com/gpu-accelerated-polars-intuitively-and-exhaustively-explained-e823a82f92a8?source=collection_archive---------3-----------------------#2024-09-17`](https://towardsdatascience.com/gpu-accelerated-polars-intuitively-and-exhaustively-explained-e823a82f92a8?source=collection_archive---------3-----------------------#2024-09-17)

## 大数据问题的快速数据框

[](https://medium.com/@danielwarfield1?source=post_page---byline--e823a82f92a8--------------------------------)![Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--e823a82f92a8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e823a82f92a8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e823a82f92a8--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--e823a82f92a8--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e823a82f92a8--------------------------------) ·14 分钟阅读·2024 年 9 月 17 日

--

![](img/6487129f23c7883d29170358d9f22491.png)

“加速极地”由 Daniel Warfield 使用 Midjourney 制作。所有图像均由作者提供，除非另有说明。本文最初发布在[直观且详尽的解释](https://iaee.substack.com/)。

最近，我参加了一个由 Cuda 和 Polars 团队举办的秘密演示。他们让我通过金属探测器，给我戴上了袋子，把我送到法国乡村的一座小屋里。他们收走了我的手机、钱包和护照，以确保我在最后揭示他们的工作成果之前不会泄露消息。

或者说，感觉是这样的。实际上那是一次 Zoom 会议，他们礼貌地要求我在指定时间之前不要说话，但作为一名技术作家，这种神秘感让我有些像詹姆斯·邦德。

在本文中，我们将讨论那次会议的内容：Polars 中的一个新执行引擎，它支持 GPU 加速计算，使得能够对 100GB 以上的数据进行交互式操作。我们将讨论 Polars 中的数据框是什么，GPU 加速如何在 Polars 数据框中工作，以及新 CUDA 驱动的执行引擎能带来多大的性能提升。

**这对谁有用？** 任何与数据打交道并希望加速工作的人。

**这篇文章有多先进？** 这篇文章包含了简单但前沿的数据工程概念。它适用于各个层次的读者。
