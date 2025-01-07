# Python 中的动态、延迟依赖注入

> 原文：[https://towardsdatascience.com/dynamic-lazy-dependency-injection-in-python-a96e6980becd?source=collection_archive---------10-----------------------#2024-11-22](https://towardsdatascience.com/dynamic-lazy-dependency-injection-in-python-a96e6980becd?source=collection_archive---------10-----------------------#2024-11-22)

## 自动 Python 依赖注入，使您的代码更具可测试性、解耦性、简单性和可读性

[](https://mikehuls.medium.com/?source=post_page---byline--a96e6980becd--------------------------------)[![Mike Huls](../Images/8f9f55a0d25db00799c5d37383b7f5b6.png)](https://mikehuls.medium.com/?source=post_page---byline--a96e6980becd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a96e6980becd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a96e6980becd--------------------------------) [Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--a96e6980becd--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a96e6980becd--------------------------------)·6 分钟阅读·2024 年 11 月 22 日

--

![](../Images/48dbe91b9e0d4d3e5489fac91cf2f169.png)

照片由[Rapha Wilde](https://unsplash.com/@veloradio?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)拍摄

依赖注入（DI）通过提高可测试性、解耦性、可维护性和可读性来解决许多问题。然而，管理依赖关系有时可能会引入新问题。我们何时初始化它们？如何初始化？它们能有效地重复使用吗？

为了将 DI 提升到下一个级别，我创建了[FastInject](https://pypi.org/project/fastinject)：一个简化依赖管理的 Python 包，只需几个装饰器即可。FastInject 自动处理依赖实例化和注入，让您可以专注于您的项目。特点：

+   **性能提升**：只在实际需要时创建依赖关系

+   **更简单的初始化**：依赖关系动态解析

+   **避免循环导入**：依赖关系推迟到运行时解析

+   **灵活性提升**：依赖关系可以受运行时信息或配置的影响

让我们开始编码！

# 内容

1.  DI 刷新：使用 DI 与不使用 DI 的代码比较

1.  使用 FastInject 进行依赖管理…
