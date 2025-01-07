# 掌握GenAI机器学习系统设计面试：原则与解决方案概述

> 原文：[https://towardsdatascience.com/mastering-genai-ml-system-design-interview-principles-solution-outline-71a4664511a7?source=collection_archive---------0-----------------------#2024-05-16](https://towardsdatascience.com/mastering-genai-ml-system-design-interview-principles-solution-outline-71a4664511a7?source=collection_archive---------0-----------------------#2024-05-16)

## 面试桌两边的策略与见解

[](https://medium.com/@jacklingenai?source=post_page---byline--71a4664511a7--------------------------------)[![Jack Chih-Hsu Lin](../Images/044c46619ba339417278ec485677c945.png)](https://medium.com/@jacklingenai?source=post_page---byline--71a4664511a7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--71a4664511a7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--71a4664511a7--------------------------------) [Jack Chih-Hsu Lin](https://medium.com/@jacklingenai?source=post_page---byline--71a4664511a7--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--71a4664511a7--------------------------------) ·13分钟阅读·2024年5月16日

--

![](../Images/e57726e5bac6c44e04b076973dff6aba.png)

解决机器学习设计就像完成一块拼图（图像由作者使用ChatGPT制作）

> 2024年9月21日备注：我正在与一家著名出版社合作出版《设计生成式AI系统》一书。请关注我的[Medium](https://medium.com/@jacklingenai)和[LinkedIn](https://www.linkedin.com/in/jacklingenai/)获取最新动态。
> 
> 2024年6月1日编辑：新增[如何设计ChatGPT记忆功能](https://medium.com/towards-data-science/mastering-genai-ml-system-design-interview-2-design-chatgpt-memory-feature-fe908517d76c)的链接
> 
> 2024年5月19日编辑：新增向量存储的考虑

我仍然清晰记得那个时刻：在我与亚马逊的机器学习设计面试中，面试官在聊天中简单地写了几句关于预测两个对象（文本或图像）相关性的内容，除了这些句子没有进一步的解释。唯一的口头指令是阅读文本。我愣住了，瞬间呆住，不知道该如何继续。尽管我尽全力克服困难并完成了这一轮面试，但由于缺乏明确的指导和初始的震惊，我的状态受到影响。正如预期的那样，我没有获得这份工作。

三年过去了，现在我已经是一名首席数据科学家，自己也参与了无数次面试。站在面试桌的两边让我对面试过程有了独特的看法，也更清楚面试官真正关心的是什么。在本文中，我将带你了解……
