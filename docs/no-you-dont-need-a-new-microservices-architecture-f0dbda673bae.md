# 不，你不需要一个新的微服务架构

> 原文：[https://towardsdatascience.com/no-you-dont-need-a-new-microservices-architecture-f0dbda673bae?source=collection_archive---------5-----------------------#2024-10-29](https://towardsdatascience.com/no-you-dont-need-a-new-microservices-architecture-f0dbda673bae?source=collection_archive---------5-----------------------#2024-10-29)

## 因为你几乎肯定已经拥有一个，只是没有明确意识到罢了

[](https://medium.com/@bernd.wessely?source=post_page---byline--f0dbda673bae--------------------------------)[![Bernd Wessely](../Images/e60e01c19412d8af8f8bddf78e561275.png)](https://medium.com/@bernd.wessely?source=post_page---byline--f0dbda673bae--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f0dbda673bae--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f0dbda673bae--------------------------------) [Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--f0dbda673bae--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f0dbda673bae--------------------------------) ·阅读时间：6分钟 ·2024年10月29日

--

![](../Images/60946a7816f2e16404817d528ed9f370.png)

企业架构作为一个混乱的系统——由DALL-E生成

如果你觉得AI生成的文章-图像确实很好地捕捉了你公司系统架构的本质，那么这篇文章就是为你准备的。

毫无疑问，将复杂任务分解为更小、更易管理的子任务，对于任何类型的问题解决都是有帮助的。这一点对于将我们的业务流程数字化的IT系统也同样适用。因此，架构师们遵循了IT领域中经过验证的“分而治之”的路径，将我们的系统拆分成多个小型应用程序/服务，分别为不同的业务领域执行特定的任务。

随着我们企业复杂性的增长，代表数字化业务流程的互联应用程序/服务的系统也变得过于复杂。因此，我们不断尝试保持秩序和结构，以防整个系统崩溃并彻底停止工作——这实际上就是*企业架构*，如果你曾经好奇那些坐在象牙塔里的人试图通过他们的架构组件图实现什么目标的话。

# 企业架构

关于如何通过合适的架构来驯服或防止混乱，已有大量的文献。
