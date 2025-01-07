# 使用PySide6构建你的第一个桌面应用程序[数据科学家版]

> 原文：[https://towardsdatascience.com/building-your-first-desktop-application-using-pyside6-a-data-scientist-edition-e2275cf0c977?source=collection_archive---------1-----------------------#2024-03-16](https://towardsdatascience.com/building-your-first-desktop-application-using-pyside6-a-data-scientist-edition-e2275cf0c977?source=collection_archive---------1-----------------------#2024-03-16)

## 惊讶吧，惊讶吧。这并不像我想象的那么难。

[](https://arunnthevapalan.medium.com/?source=post_page---byline--e2275cf0c977--------------------------------)[![Arunn Thevapalan](../Images/85cb8ba8eae0d5fd8e85e75bae8c794f.png)](https://arunnthevapalan.medium.com/?source=post_page---byline--e2275cf0c977--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2275cf0c977--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e2275cf0c977--------------------------------) [Arunn Thevapalan](https://arunnthevapalan.medium.com/?source=post_page---byline--e2275cf0c977--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2275cf0c977--------------------------------) ·阅读时长14分钟·2024年3月16日

--

![](../Images/2e004fd42ea473ba2241ca5df7960186.png)

图片由[Linus Mimietz](https://unsplash.com/@linusmimietz?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我作为数据科学家的工作中最难的部分是说服非技术性利益相关者，让他们意识到*又一个*数据科学解决方案如何帮助*他们*做出更好的决策。

不过，这对我来说并不新鲜。在我作为数据科学家和机器学习工程师的5年以上经验中，一直是这样的。

经过多次尝试和错误，对我来说，行得通的顺序是：

+   **通过简化技术概念分享定期进度更新（演示文稿幻灯片）**。

+   **构建一个机器学习网页应用程序**，在项目的最后阶段，为利益相关者提供与我们共同构建的解决方案互动的体验。

然而，有一个转折点，我和同一个团队工作了大约5年，一位同事使用.NET为一个不同的用例开发了一个桌面应用程序（而不是网页应用程序）。团队对这个应用程序非常喜欢。

所以我问自己：为什么不构建一个桌面应用程序，而不是网页应用程序呢？

不过有一个问题，那就是我一开始不懂.NET，而且之前从未开发过桌面应用程序……
