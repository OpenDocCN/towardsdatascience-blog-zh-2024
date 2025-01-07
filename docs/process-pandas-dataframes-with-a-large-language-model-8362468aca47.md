# 使用大语言模型处理 Pandas 数据框

> 原文：[`towardsdatascience.com/process-pandas-dataframes-with-a-large-language-model-8362468aca47?source=collection_archive---------3-----------------------#2024-03-22`](https://towardsdatascience.com/process-pandas-dataframes-with-a-large-language-model-8362468aca47?source=collection_archive---------3-----------------------#2024-03-22)

## Python、Pandas 和 LLM 的无缝集成

[](https://dmitryelj.medium.com/?source=post_page---byline--8362468aca47--------------------------------)![Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--8362468aca47--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8362468aca47--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8362468aca47--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--8362468aca47--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8362468aca47--------------------------------) ·阅读时长 12 分钟·2024 年 3 月 22 日

--

![](img/070200dcd79e221e92f67dd1e64493f0.png)

Pandas，图片由[Stone Wang](https://unsplash.com/@stonewyq)提供，Unsplash

如今，通过网页界面或公共 API 使用不同的大型语言模型（LLM）变得十分容易。但我们能否将 LLM 无缝集成到数据分析过程中，并直接从 Python 或 Jupyter Notebook 中使用这些模型呢？事实上，我们可以，在本文中，我将展示三种不同的方法来实现这一点。和往常一样，文章中使用的所有组件都是免费的。

让我们开始吧！

## 1\. Pandas AI

我将测试的第一个 Python 库是[Pandas AI](https://github.com/Sinaptik-AI/pandas-ai)。它允许我们用自然语言查询 Pandas 数据框中的内容。作为一个示例，我创建了一个包含所有欧盟国家及其人口的小型数据框：

```py
import pandas as pd

df = pd.DataFrame({
    "Country": ['Austria', 'Belgium', 'Bulgaria', 'Croatia', 'Cyprus', 'Czech Republic', 'Denmark', 'Estonia', 'Finland',
                'France', 'Germany', 'Greece', 'Hungary', 'Iceland', 'Ireland', 'Italy', 'Latvia', 'Liechtenstein', 'Lithuania',
                'Luxembourg', 'Malta', 'Monaco', 'Montenegro', 'Netherlands', 'Norway', 'Poland', 'Portugal', 'Romania', 'Serbia',
                'Slovakia', 'Slovenia', 'Spain', 'Sweden', 'Switzerland'],
    "Population": [8_205000, 10_403000…
```
