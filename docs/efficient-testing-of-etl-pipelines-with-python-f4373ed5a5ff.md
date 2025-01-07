# 使用Python高效测试ETL管道

> 原文：[https://towardsdatascience.com/efficient-testing-of-etl-pipelines-with-python-f4373ed5a5ff?source=collection_archive---------4-----------------------#2024-10-03](https://towardsdatascience.com/efficient-testing-of-etl-pipelines-with-python-f4373ed5a5ff?source=collection_archive---------4-----------------------#2024-10-03)

## 如何即时检测数据质量问题并找出其根本原因

[](https://medium.com/@robinvm?source=post_page---byline--f4373ed5a5ff--------------------------------)[![Robin von Malottki](../Images/f5724e8eecb4925706a83f353caa303a.png)](https://medium.com/@robinvm?source=post_page---byline--f4373ed5a5ff--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f4373ed5a5ff--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f4373ed5a5ff--------------------------------) [Robin von Malottki](https://medium.com/@robinvm?source=post_page---byline--f4373ed5a5ff--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f4373ed5a5ff--------------------------------) ·阅读时间：10分钟·2024年10月3日

--

![](../Images/6a803fdb717aa8b5c854a306c34e34b2.png)

图片由[Digital Buggu](https://www.pexels.com/de-de/foto/bunte-zahnrader-171198/)提供，来源于Pexels.com

在今天的数据驱动型世界中，组织在做出关键业务决策时，极度依赖准确的数据。作为一名负责任和值得信赖的数据工程师，确保数据质量至关重要。即使在仪表板上短时间展示不正确的数据，也可能导致整个组织内部错误信息的迅速传播，就像病毒在生物体内的传播一样。

那么，我们如何预防这一问题呢？理想情况下，我们会完全避免数据质量问题的发生。然而，残酷的事实是，完全避免它们是不可能的。不过，我们可以采取两个关键措施来减轻其影响。

1.  第一时间得知数据质量问题的出现

1.  最小化解决问题所需的时间

在这篇博客中，我将展示如何直接在代码中实现第二点。我将使用Mockaroo生成的数据在Python中创建一个数据管道，并利用Tableau快速识别任何故障的原因。如果你正在寻找一个替代的测试框架，可以查看我关于[Python中Great Expectations简介](https://medium.com/towards-data-engineering/an-introduction-in-great-expectations-with-python-4793a2609e4c)的文章。
