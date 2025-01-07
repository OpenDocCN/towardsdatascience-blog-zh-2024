# 一种真实世界中的新颖方法，用于增强推荐系统中的多样性

> 原文：[https://towardsdatascience.com/a-real-world-novel-approach-to-enhance-diversity-in-recommender-systems-7968655d4581?source=collection_archive---------10-----------------------#2024-03-01](https://towardsdatascience.com/a-real-world-novel-approach-to-enhance-diversity-in-recommender-systems-7968655d4581?source=collection_archive---------10-----------------------#2024-03-01)

## 推荐系统

## 解决长尾问题并提升Headspace应用中的用户推荐体验

[](https://medium.com/@christabellecp?source=post_page---byline--7968655d4581--------------------------------)[![Christabelle Pabalan](../Images/24187865b6e9d03ae1aabf873ce1e67c.png)](https://medium.com/@christabellecp?source=post_page---byline--7968655d4581--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7968655d4581--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7968655d4581--------------------------------) [Christabelle Pabalan](https://medium.com/@christabellecp?source=post_page---byline--7968655d4581--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7968655d4581--------------------------------) ·阅读时间：6分钟·2024年3月1日

--

![](../Images/a121f47f6d8f0130622fc81be4a485ce.png)

作者生成的DALLE图像

## 两种推荐系统的选择

在经过数周的辛勤工作后，你终于有一个值得的夜晚来享受一次愉快的餐厅体验。两个朋友都给出了餐厅推荐。一个朋友倾向于选择那些经过验证的地方，极少涉足未曾尝试的领域。一旦一个笑话奏效，他们会不断地用稍微变动的方式重复。虽然他们的品味可以依赖，但他们的推荐从未让你彻底印象深刻。第一个朋友就像一个受到**长尾问题**困扰的推荐系统；他们推荐的餐厅是那些大众熟知的、可靠且对大多数人适口的，但不一定是量身定制的。

相反，还有一个朋友总是充满冒险精神，随意地说出各种想法。你总是对他们的口味偏好感到困惑。尽管他们推荐了一些不错的餐厅，但也推荐了一些糟糕的地方，且每次推荐时他们的热情都一样高涨。第二个朋友……
