# 用开源LLM应对开放书籍考试

> 原文：[https://towardsdatascience.com/battling-open-book-exams-with-open-source-llms-c2225f3002d0?source=collection_archive---------4-----------------------#2024-07-19](https://towardsdatascience.com/battling-open-book-exams-with-open-source-llms-c2225f3002d0?source=collection_archive---------4-----------------------#2024-07-19)

## 在每个人都在为工作和学校使用ChatGPT的时代，我正在利用它来帮助我学习大学课程。

[](https://medium.com/@jubayerhossainahad?source=post_page---byline--c2225f3002d0--------------------------------)[![Jubayer Hossain Ahad](../Images/e8decad2cc88586cfca4a5879999d114.png)](https://medium.com/@jubayerhossainahad?source=post_page---byline--c2225f3002d0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c2225f3002d0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c2225f3002d0--------------------------------) [Jubayer Hossain Ahad](https://medium.com/@jubayerhossainahad?source=post_page---byline--c2225f3002d0--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c2225f3002d0--------------------------------) ·8分钟阅读·2024年7月19日

--

![](../Images/43a04a20c65a74064e011c74d258f0de.png)

图片由Dall-E 3生成，由作者提供提示

***免责声明*** *这不是任何考试的作弊或黑客工具。这只是一个帮助你更好准备课程考试的工具。请明智地使用它。*

嗨，我是Jubayer Hossain，FAU-Erlangen的硕士生。我的电动出行专业包括机械学、人工智能和编程课程。本学期，我选修了两门课程，专注于开放书籍考试，所有的讲义内容都已提供。

由于我们可以自由使用任何资源，而大规模语言模型（LLMs）现在正变得非常流行，我计划在Langchain的帮助下实现一个基于RAG的开源LLM，帮助我进行内容搜索，并更好地为考试做准备。

所以不再赘述，接下来让我们开始项目计划。

## 项目计划

**首先**，我需要在课程门户网站中提供的所有讲义幻灯片。大约有16个PDF幻灯片，我手动下载了这些。当然，我可以写一个脚本，自动下载所有幻灯片，但只有16个，手动下载会更快。
