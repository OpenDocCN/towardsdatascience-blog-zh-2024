# 大 O — 一种实践方法

> 原文：[https://towardsdatascience.com/big-o-a-practical-approach-319a6a3c8b27?source=collection_archive---------4-----------------------#2024-01-19](https://towardsdatascience.com/big-o-a-practical-approach-319a6a3c8b27?source=collection_archive---------4-----------------------#2024-01-19)

## 使用代码示例实施大 O 最佳实践。思维方式的转变如何提升代码在运行时的性能

[](https://medium.com/@christopher_karg?source=post_page---byline--319a6a3c8b27--------------------------------)[![Christopher Karg](../Images/9d163d59e0c3167732f55d497caf9db2.png)](https://medium.com/@christopher_karg?source=post_page---byline--319a6a3c8b27--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--319a6a3c8b27--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--319a6a3c8b27--------------------------------) [Christopher Karg](https://medium.com/@christopher_karg?source=post_page---byline--319a6a3c8b27--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--319a6a3c8b27--------------------------------) ·13 分钟阅读·2024年1月19日

--

![](../Images/918883afa06c8e54bc10a83c06d48473.png)

来源：[https://www.pexels.com/photo/wooden-picnic-bench-beside-the-lake-9367323/](https://www.pexels.com/photo/wooden-picnic-bench-beside-the-lake-9367323/)

本文旨在提高我们数据专业人士的大 O 识字率。我们常常在软件工程（SWE）和数据世界之间进行比较。虽然这两个领域都有一些最佳实践（如版本控制、错误处理和测试），但根据我个人的经验，数据角色相较于 SWE 角色在编写高效代码方面似乎落后。更具体来说，是在实现和检查高效代码的思维方式上。我个人过去也曾犯过这种错误——如果我脚本中的代码按预期运行并且能够优雅地处理错误，我就会认为它“完成”了。我相信，任何创建生产级代码的人，不论职位如何，都有责任确保其代码在运行时具有高效性能。

我意识到，许多专注于数据的岗位已经在实施大 O 最佳实践。通过与同行交流，这些人大多是与软件工程师紧密合作的人，因此能够“吸收”这些方法论。这与那些在独立数据团队中工作的人形成对比。尽管我只能推测其中的原因…
