# 今天别洗衣服，明天会更便宜

> 原文：[https://towardsdatascience.com/dont-do-laundry-today-it-will-be-cheaper-tomorrow-84a6baa4e626?source=collection_archive---------5-----------------------#2024-10-21](https://towardsdatascience.com/dont-do-laundry-today-it-will-be-cheaper-tomorrow-84a6baa4e626?source=collection_archive---------5-----------------------#2024-10-21)

## 通过因果推断分析伦敦的电价变化

[](https://medium.com/@acorralescano?source=post_page---byline--84a6baa4e626--------------------------------)[![Alvaro Corrales Cano](../Images/988209a9b396fb3b0b287880eb9bb05a.png)](https://medium.com/@acorralescano?source=post_page---byline--84a6baa4e626--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--84a6baa4e626--------------------------------)[![数据科学前沿](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--84a6baa4e626--------------------------------) [Alvaro Corrales Cano](https://medium.com/@acorralescano?source=post_page---byline--84a6baa4e626--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--84a6baa4e626--------------------------------) ·阅读时长16分钟·2024年10月21日

--

![](../Images/ac6acf07d4dba9adf4aa592d801b92bb.png)

图片来源：[Arthur Lambillotte](https://unsplash.com/@artlambi?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在[Unsplash](https://unsplash.com/photos/a-person-holding-a-device-YG19gb9jW0I?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

有时候，我们不得不进行实验。无论是公司改进产品，还是公共官员追求政策目标，他们都面临着类似的问题：

+   新的定价方案能否提高我们电信公司的客户保持率？

+   新的入职流程能否提高应用程序转化率？

+   提醒系统能否减少医院的缺席率？

+   如果能，能提高多少？

这些问题很棘手。未能解决它们的成本可能很严重，但错误的解决方案可能更昂贵。为了降低风险，许多人转向实验：在将新的提醒系统推广到整个地区的医疗保健网络之前，我们可以先在一所医院进行测试；在为平台上的所有用户部署新的入职系统之前，我们可能只对部分用户展示；在全国范围内调整光纤收费标准之前，我们可以先在一个城市进行测试。这些都是计划解决方案的小规模版本实验，但我们如何知道它们是否有效呢？这就是因果分析派上用场的地方。

在本文中，我提出了一个因果框架，用于分析2013年伦敦发生的一项实验：[低碳伦敦](https://innovation.ukpowernetworks.co.uk/projects/low-carbon-london)……
