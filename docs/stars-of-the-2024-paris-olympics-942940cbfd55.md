# 2024年巴黎奥运会的明星运动员

> 原文：[https://towardsdatascience.com/stars-of-the-2024-paris-olympics-942940cbfd55?source=collection_archive---------10-----------------------#2024-08-12](https://towardsdatascience.com/stars-of-the-2024-paris-olympics-942940cbfd55?source=collection_archive---------10-----------------------#2024-08-12)

![](../Images/b3f615bf0ce8904d2f66fca3029160e1.png)

## 如何使用维基百科数据可视化顶级运动员和奥林匹克运动的受欢迎程度

[](https://medium.com/@janosovm?source=post_page---byline--942940cbfd55--------------------------------)[![米兰·亚诺索夫](../Images/b7ede67b165cdd368d96f13f46c68ccb.png)](https://medium.com/@janosovm?source=post_page---byline--942940cbfd55--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--942940cbfd55--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--942940cbfd55--------------------------------) [米兰·亚诺索夫](https://medium.com/@janosovm?source=post_page---byline--942940cbfd55--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--942940cbfd55--------------------------------) ·8分钟阅读·2024年8月12日

--

我们刚刚见证了为期三周的精彩体育赛事，2024年奥运会在巴黎展开，数百万观众观看了直播并为他们喜爱的运动员加油。同时，我们看到一些家喻户晓的名字再次大放异彩，而下一代明星运动员也逐渐崭露头角。

作为一个好奇的数据科学家，我开始思考这些集体观点是如何发展的——哪些运动最受欢迎，哪些运动员正在崛起？为了将数据落实，我决定从维基百科进行广泛的数据收集，内容包括维基百科的个人资料和浏览量信息，然后比较不同运动项目的受欢迎程度以及顶级运动员的情况。以下是我的结果以及重现这些结果所需的所有Python代码。

*所有图片均由作者创作。*

# 维基百科上的奥林匹克运动

首先，让我们访问2024年夏季奥运会的维基百科页面，并使用requests库将其下载。然后，我使用BeautifulSoup包从HTML中提取信息——即所有夏季项目的列表及其维基页面链接。为了提取这些运动项目，我还会按照我的手动方法进行操作……
