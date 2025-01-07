# 使用 Llama 3 构建 AI 代理

> 原文：[https://towardsdatascience.com/using-llama-3-for-building-ai-agents-7e74f79d1ccc?source=collection_archive---------3-----------------------#2024-07-31](https://towardsdatascience.com/using-llama-3-for-building-ai-agents-7e74f79d1ccc?source=collection_archive---------3-----------------------#2024-07-31)

## 使用 Llama 3 的功能调用能力构建 AI 代理的综合指南。

[](https://ransakaravihara.medium.com/?source=post_page---byline--7e74f79d1ccc--------------------------------)[![Ransaka Ravihara](../Images/ac09746938c10ad8f157d46ea0de27ca.png)](https://ransakaravihara.medium.com/?source=post_page---byline--7e74f79d1ccc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7e74f79d1ccc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7e74f79d1ccc--------------------------------) [Ransaka Ravihara](https://ransakaravihara.medium.com/?source=post_page---byline--7e74f79d1ccc--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e74f79d1ccc--------------------------------) ·阅读时间 10 分钟·2024年7月31日

--

![](../Images/869e4b150e9bf1a4b0c518954878e232.png)

图片由作者提供，通过 [Canva](https://www.canva.com/ai-image-generator/)

## 介绍

假设你想购买某些商品。你访问一个电商网站，使用搜索选项来找到你想要的东西。也许你有多个商品要购买，因此这个过程效率不高。现在考虑这种情形：打开一个应用程序，直接用简洁的英文描述你想要的商品，然后按回车键。你不必担心搜索和价格比较，因为应用程序会自动为你处理这些。很酷，对吧？这正是我们将在本教程中构建的功能。

让我们先看一些示例。

![](../Images/587a116069f2044362336f9b0c25b862.png)

用户一次请求多个商品

![](../Images/0b528b272bafed8c70176722d8e4391c.png)

用户正在寻求最具性价比的购买选项。

好的，让我们为这个应用程序赋予生命。我们将使用 Meta 的 Llama 3 模型，带有功能调用能力。然而，这也可以通过使用 3.1 模型来完成。根据[Meta 的公告](https://ai.meta.com/blog/meta-llama-3-1/)，3.1 模型能够更有效地使用工具和功能。
