# 如何将你的 AI 点子变成一个可扩展的产品：技术指南

> 原文：[`towardsdatascience.com/how-to-turn-your-ai-idea-into-a-scalable-product-a-technical-guide-aec143668a8b?source=collection_archive---------1-----------------------#2024-07-02`](https://towardsdatascience.com/how-to-turn-your-ai-idea-into-a-scalable-product-a-technical-guide-aec143668a8b?source=collection_archive---------1-----------------------#2024-07-02)

## 是时候告别 localhost，开始获取用户了

[](https://medium.com/@mattchapmanmsc?source=post_page---byline--aec143668a8b--------------------------------)![Matt Chapman](https://medium.com/@mattchapmanmsc?source=post_page---byline--aec143668a8b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aec143668a8b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aec143668a8b--------------------------------) [Matt Chapman](https://medium.com/@mattchapmanmsc?source=post_page---byline--aec143668a8b--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aec143668a8b--------------------------------) ·9 分钟阅读·2024 年 7 月 2 日

--

![](img/21b02a00ba97f7641c349bf663de5cbe.png)

图片由[Abhijeet Wankhade](https://unsplash.com/@wankhade)提供，发布在[Unsplash](https://unsplash.com/photos/black-white-and-blue-floral-tablet-computer-case-PxOiAc0g8-s)

你是否曾经有过一个关于 AI 驱动应用程序或数据科学产品的好点子？

我知道我有。我在我的 iPhone 上有个名为*Ideas*的笔记，里面有 50 多个点子。

但是，如何将你的点子转化为一个具有真实用户的可扩展产品呢？

当然，你可能知道如何开发一个机器学习模型或调优一个大语言模型（LLM）。但是[如果一个模型卡在 Jupyter notebook 里](https://medium.com/towards-data-science/deploy-a-lightgbm-ml-model-with-github-actions-781c094acfa3)或只在`localhost`上运行，它对任何人都没有用处。

本指南将展示如何将一个点子变成一个生产级产品。

如果你是一个想要创业的人或是初创公司员工，这篇文章将为你提供必要的知识，帮助你摆脱`localhost`，推出你的产品并开始获取用户。

正如你将看到的，构建 AI 驱动产品并没有*单一*的方式——有*许多*可能的选项。我的目标不是倡导某个特定策略或深入研究代码。相反，我的目标是从技术架构的角度，给你提供一个广泛的选项概览。这样，当你阅读未来的博客，展示某个特定的部署策略时，你就能拥有足够的知识和信心，批判性地评估这篇博客，并判断它是否是最佳方案。
