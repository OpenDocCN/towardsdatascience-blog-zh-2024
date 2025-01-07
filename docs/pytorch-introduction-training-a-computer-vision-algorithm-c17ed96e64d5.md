# PyTorch简介 — 训练计算机视觉算法

> 原文：[https://towardsdatascience.com/pytorch-introduction-training-a-computer-vision-algorithm-c17ed96e64d5?source=collection_archive---------7-----------------------#2024-05-30](https://towardsdatascience.com/pytorch-introduction-training-a-computer-vision-algorithm-c17ed96e64d5?source=collection_archive---------7-----------------------#2024-05-30)

## 在这篇文章中，我们将学习如何使用卷积神经网络在PyTorch中训练计算机视觉模型。

[](https://ivopbernardo.medium.com/?source=post_page---byline--c17ed96e64d5--------------------------------)[![Ivo Bernardo](../Images/39887b6f3e63a67c0545e87962ad5df0.png)](https://ivopbernardo.medium.com/?source=post_page---byline--c17ed96e64d5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c17ed96e64d5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c17ed96e64d5--------------------------------) [Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--c17ed96e64d5--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c17ed96e64d5--------------------------------) ·10分钟阅读·2024年5月30日

--

![](../Images/a86a01498aea362ee3f8e9a1baa1bb97.png)

自动驾驶汽车主要由计算机视觉算法驱动 — 由微软设计师在AI中生成的图像

*PyTorch*目前是深度学习领域最热门的库之一。全球成千上万的开发者在使用这个库，它自从*ChatGPT*发布并深度学习进入主流新闻头条以来，声名鹊起。

由于其高效训练深度学习模型的能力（具备GPU加速功能），它已成为机器学习工程师和数据科学家在训练复杂神经网络算法时的最佳伙伴。

到目前为止，在这个*PyTorch*系列中，我们已经涵盖了几项基础知识，为我们从零开始使用这个库打下了基础。例如：

+   [我们已经学习了关于张量的基础知识](/pytorch-introduction-tensors-and-tensor-calculations-412ff818bd5b?sk=2cf4d44549664fc647baa3455e9d78e8);

+   我们[已经了解了如何使用*PyTorch*创建我们的第一个线性模型（回归）。](/pytorch-introduction-building-your-first-linear-model-d868a8681a41)

+   [我们已经学会了如何使用非线性激活函数以及如何解决非线性问题。](/pytorch-introduction-enter-nonlinear-functions-4dd893845592)

+   [我们已经了解了如何在库的上下文中使用自定义数据。](https://medium.com/@ivopbernardo/pytorch-introduction-using-custom-data-4c70940d5358?sk=be995a685a4f573aea64c4491283ab32)

在这篇博客文章中，我们终于要拿出“大杀器”并训练我们的…
