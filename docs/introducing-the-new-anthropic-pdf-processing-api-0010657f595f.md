# 介绍全新的Anthropic PDF处理API

> 原文：[https://towardsdatascience.com/introducing-the-new-anthropic-pdf-processing-api-0010657f595f?source=collection_archive---------0-----------------------#2024-11-27](https://towardsdatascience.com/introducing-the-new-anthropic-pdf-processing-api-0010657f595f?source=collection_archive---------0-----------------------#2024-11-27)

![](../Images/e659afb72bb244af875d4665e20cec0f.png)

图片由AI（Dalle-3）生成

## Anthropic Claude 3.5现在支持理解PDF输入

[](https://medium.com/@thomas_reid?source=post_page---byline--0010657f595f--------------------------------)[![托马斯·里德](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--0010657f595f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0010657f595f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0010657f595f--------------------------------) [托马斯·里德](https://medium.com/@thomas_reid?source=post_page---byline--0010657f595f--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0010657f595f--------------------------------) ·阅读时间8分钟·2024年11月27日

--

在过去的几周里，Anthropic发布了一些令人兴奋的beta功能，这些功能大多未受到关注。其中之一是它的新token计数API。我已经写了一篇关于这个的文章，你可以通过点击下面的链接阅读。

[](/introducing-the-new-anthropic-token-counting-api-5afd58bad5ff?source=post_page-----0010657f595f--------------------------------) [## 介绍全新的Anthropic Token计数API

### 使用Claude时，更加密切地关注成本

towardsdatascience.com](/introducing-the-new-anthropic-token-counting-api-5afd58bad5ff?source=post_page-----0010657f595f--------------------------------)

另一个令人兴奋的功能，也是本文的主题，是Claude 3.5现在可以处理PDF，并理解PDF文档中的文本和视觉内容。

## PDF功能

Claude支持任何标准PDF文件，允许你查询文档中的文本、图片、图表和表格。以下是一些常见的使用案例：

+   分析财务报告，解读图表和表格

+   从法律文档中提取关键信息

+   协助文档翻译

+   将文档内容转换为结构化格式

## 限制
