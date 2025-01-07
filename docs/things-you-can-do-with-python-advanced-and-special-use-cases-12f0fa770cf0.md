# 使用 Python 可以做的事情：高级和特殊用例

> 原文：[https://towardsdatascience.com/things-you-can-do-with-python-advanced-and-special-use-cases-12f0fa770cf0?source=collection_archive---------4-----------------------#2024-02-15](https://towardsdatascience.com/things-you-can-do-with-python-advanced-and-special-use-cases-12f0fa770cf0?source=collection_archive---------4-----------------------#2024-02-15)

[](https://towardsdatascience.medium.com/?source=post_page---byline--12f0fa770cf0--------------------------------)[![TDS 编辑](../Images/4b2d1beaf4f6dcf024ffa6535de3b794.png)](https://towardsdatascience.medium.com/?source=post_page---byline--12f0fa770cf0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--12f0fa770cf0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--12f0fa770cf0--------------------------------) [TDS 编辑](https://towardsdatascience.medium.com/?source=post_page---byline--12f0fa770cf0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--12f0fa770cf0--------------------------------) ·作为 [时事通讯](https://towardsdatascience.com/?source=post_page---byline--12f0fa770cf0--------------------------------) 发送 ·阅读时间 3 分钟 ·2024年2月15日

--

Python在数据科学和机器学习工作流程中发挥着至关重要的作用，以至于它有时几乎融入了我们的日常节奏；毕竟，你有多久没有考虑过办公室的开关或门把手了呢？你也经常使用它们。

在我们2024年第一期以Python为中心的《Variable》版本中，我们决定聚焦一些我们最近发布的更有趣且与众不同的用例。我们喜欢好的Pandas或Matplotlib教程——我们的许多读者也一样——但有时从日常的基础话题中稍作休息，深入探讨一些更高级的内容也是很有趣的。本周，让我们稍微放纵一下！我们希望你们喜欢我们挑选的九篇Python文章，它们涵盖了丰富多样的项目和挑战。

+   [**如何构建一个基于图的神经网络进行异常检测的六个步骤**](/how-to-build-a-graph-based-neural-network-for-anomaly-detection-in-6-steps-a7dc47723788) 在她的最新逐步教程中，[Claudia Ng](https://medium.com/u/ba2da7b3b9c8?source=post_page---user_mention--12f0fa770cf0--------------------------------)，她经常撰写有关有趣的基于Python的项目的文章，重点介绍了构建一个基于图的神经网络，该网络能够处理异构图数据以进行链接预测。

+   [**介绍 Quad-Tile 图表与 Squaremap：让数据方块化**](/introducing-the-quad-tile-chart-squaremap-squarify-your-data-20be336a1dd7)为什么要满足于现有的可视化格式，当你可以创建自己的格式？[Nick Gerend](https://medium.com/u/fa23f7cc3eed?source=post_page---user_mention--12f0fa770cf0--------------------------------)邀请我们一起走进 Quad-Tile 图表背后的制作过程，这是一种通过 Python 实现的无坐标轴方法，将一组数值可视化为方块。

+   [**使用 bart-large-mnli 标记登山事故报告**](/tagging-mountaineering-accident-reports-using-bart-large-mnli-1b1f6d97756a)仍然是在创新可视化领域，[Karla Hernández](https://medium.com/u/e613ed6e6f3?source=post_page---user_mention--12f0fa770cf0--------------------------------)带我们走过创建一个惊艳的数据故事艺术品的步骤，突出了从自定义标记的登山事故数据集中得到的发现。

+   [**有限自动机模拟与 AI 辅助系统的应用**](/bird-by-bird-using-finite-automata-9d50b36bcbd3)通过使用有限状态机，[Sofya Lipnitskaya](https://medium.com/u/96353360997a?source=post_page---user_mention--12f0fa770cf0--------------------------------)探讨了一种优化复杂现实世界 AI 辅助过程性能的潜在方法——在本例中，一个对象检测系统会启动喷水装置来吓跑入侵的鸡群！

![](../Images/a0f4874a195b5c2dbd6e18cfe5bdb84e.png)

图片由 [Nicholas Safran](https://unsplash.com/@nicholas_safran?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   [**从太空看风暴：一个创建惊人视图的 Python 脚本**](/watching-storms-from-space-a-python-script-for-creating-an-amazing-view-79d8bb2f5ff1)处理地理空间数据有其自身的挑战；[Mahyar Aboutalebi, Ph.D.](https://medium.com/u/7e6350a085ee?source=post_page---user_mention--12f0fa770cf0--------------------------------)的最新指南详细介绍了如何编写一个 Python 脚本，帮助你收集卫星图像并将其转化为强大的风暴动画。

+   [**Python 最强大的装饰器**](/pythons-most-powerful-decorator-6bc39e6a8dd8)如果你错过了，[Siavash Yasini](https://medium.com/u/17613cac9c65?source=post_page---user_mention--12f0fa770cf0--------------------------------)对 Python 的 @property 装饰器的详细介绍是我们最近几周阅读量最高的编程文章之一。文章介绍了几种利用其强大功能的实用方法：从保护数据属性免受覆盖到懒加载和内存优化。

+   [**塑造想象力：利用人工智能创造新的 3D 打印对象**](/molding-the-imagination-using-ai-to-create-new-3d-printable-objects-cf3682f8563b)在文字、图像、音乐和视频之后，3D 对象是否会成为生成型人工智能的下一个前沿？[罗伯特·A·贡萨尔维斯](https://medium.com/u/c97e6c73c13c?source=post_page---user_mention--12f0fa770cf0--------------------------------)分享了他最近的实验结果，这些实验依赖于 Midjourney 进行图像生成，并利用一些经典的 Python 代码将这些图像转化为实际的 3D 对象。

+   [**文本嵌入：全面指南**](/text-embeddings-comprehensive-guide-afd97fce8fb5)如果你是文本嵌入领域的新手，[玛丽亚·曼苏罗娃](https://medium.com/u/15a29a4fc6ad?source=post_page---user_mention--12f0fa770cf0--------------------------------)的入门指南是一个很好的起点——它既（非常）全面*又*易于理解，实践部分还包含了你开始动手操作时所需要的所有 Python 代码片段。

+   [**理解连接点（链条、分叉和碰撞点）及其在因果推断中的作用**](/understanding-junctions-chains-forks-and-colliders-and-the-role-they-play-in-causal-inference-211db6770e2f)在他最近对有向无环图（DAGs）的深入探讨中，[格雷厄姆·哈里森](https://medium.com/u/bd1c93739f33?source=post_page---user_mention--12f0fa770cf0--------------------------------)聚焦于连接点类型及其在因果推断任务中的重要性。在此过程中，他还展示了如何生成数据集，执行普通最小二乘法（OLS）回归等操作，所有这些都依赖于——你猜对了——Python。

感谢您支持我们作者的工作！如果您感到受到启发，为什么不[写下您的第一篇文章呢？我们很想读一读](http://bit.ly/write-for-tds)。

直到下次变量再见，

TDS 团队
