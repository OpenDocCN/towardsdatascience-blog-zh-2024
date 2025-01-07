# 从零开始的置换特征重要性

> 原文：[https://towardsdatascience.com/permutation-feature-importance-from-scratch-b8dcaceba9c2?source=collection_archive---------3-----------------------#2024-04-24](https://towardsdatascience.com/permutation-feature-importance-from-scratch-b8dcaceba9c2?source=collection_archive---------3-----------------------#2024-04-24)

## 理解置换在可解释人工智能领域的重要性

[](https://conorosullyds.medium.com/?source=post_page---byline--b8dcaceba9c2--------------------------------)[![Conor O'Sullivan](../Images/2dc50a24edb12e843651d01ed48a3c3f.png)](https://conorosullyds.medium.com/?source=post_page---byline--b8dcaceba9c2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b8dcaceba9c2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b8dcaceba9c2--------------------------------) [Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--b8dcaceba9c2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b8dcaceba9c2--------------------------------) ·阅读时间10分钟·2024年4月24日

--

![](../Images/15a78f5d1179cb2d667a50836357cef7.png)

（来源：作者）

如果你深入了解最先进的[XAI](/what-is-interpretable-machine-learning-2d217b62185a)方法，你会发现它们都涉及置换。[SHAP](/introduction-to-shap-with-python-d27edc23c454)、[LIME](/squeezing-more-out-of-lime-with-python-28f46f74ca8e)、[PDPs & ICE图](/the-ultimate-guide-to-pdps-and-ice-plots-4182885662aa)、ALE和Friedman的H-stat都依赖于此。这也是为什么理解置换及其局限性对于该领域如此重要的原因。所以，让我们从最简单的XAI方法——置换特征重要性（PFI）开始。

为了深入理解这种方法，我们将：

+   使用Python从零开始计算PFI。

+   解释该方法背后的选择，包括为什么要进行置换、重复以及使用哪种度量标准。

+   讨论置换的局限性。

你可以在[GitHub](https://github.com/a-data-odyssey/XAI-tutorial)上找到完整的项目。

你可能也会喜欢这段关于该主题的视频。如果你想了解更多，可以查看我的课程——[Python中的XAI](https://adataodyssey.com/courses/xai-with-python/)。如果你注册我的[通讯](https://mailchi.mp/aa82a5ce1dc0/signup)，你可以免费访问。

# 为什么选择PFI？
