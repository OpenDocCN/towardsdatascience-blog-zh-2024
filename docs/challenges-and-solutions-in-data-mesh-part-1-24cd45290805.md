# 数据网格的挑战与解决方案 – 第一部分

> 原文：[`towardsdatascience.com/challenges-and-solutions-in-data-mesh-part-1-24cd45290805?source=collection_archive---------5-----------------------#2024-04-26`](https://towardsdatascience.com/challenges-and-solutions-in-data-mesh-part-1-24cd45290805?source=collection_archive---------5-----------------------#2024-04-26)

## 为什么 Zhamak Dehghani 定义的数据网格面临挑战，以及如何解决这些挑战。

[](https://medium.com/@bernd.wessely?source=post_page---byline--24cd45290805--------------------------------)![Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--24cd45290805--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--24cd45290805--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--24cd45290805--------------------------------) [Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--24cd45290805--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--24cd45290805--------------------------------) ·7 分钟阅读·2024 年 4 月 26 日

--

![](img/3193e22509911913897a11339a9a722b.png)

图片来自[Tobias Fischer](https://unsplash.com/@tofi?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的发布

你是否曾经体验过那种“哦，原来如此！”的时刻，当你读到一篇关于你深感兴趣且对你有重要意义的文章时，文章完美地捕捉了眼前问题的本质？我清晰地记得，大约四年前我第一次看到 Zhamak Dehghani 的[原始博客文章](https://martinfowler.com/articles/data-monolith-to-mesh.html)时的情景（她是在 2019 年写的）。

Zhamak 对我们当前数据工程状态的分析非常深入且经过深思熟虑。除了对分析本身的讨论，她还提出了解决所描述问题的方案，并将其命名为“数据网格（Data Mesh）”，这一方案随后在数据工程社区内获得了广泛关注。她的社会技术方法基于这四个相互作用的原则：

+   数据的去中心化领域所有权

+   数据即产品

+   自服务数据基础设施作为平台

+   联邦计算数据治理

我不会在这篇文章中详细讲解这些原则，但总的来说，我可以突出 Zhamak 讨论的许多内容。这篇文章以及后续的书籍识别了许多我依然关注的问题……
