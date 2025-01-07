# 使用Ollama进行结构化LLM输出

> 原文：[https://towardsdatascience.com/structured-llm-output-using-ollama-73422889c7ad?source=collection_archive---------2-----------------------#2024-12-17](https://towardsdatascience.com/structured-llm-output-using-ollama-73422889c7ad?source=collection_archive---------2-----------------------#2024-12-17)

## 有效控制你的模型响应

[](https://medium.com/@thomas_reid?source=post_page---byline--73422889c7ad--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--73422889c7ad--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--73422889c7ad--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--73422889c7ad--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--73422889c7ad--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--73422889c7ad--------------------------------) ·阅读时间：9分钟·2024年12月17日

--

在0.5版本中，Ollama对其LLM API进行了重大增强。通过引入结构化输出，Ollama现在使得可以将模型输出约束为由JSON模式定义的特定格式。在背后，大多数系统使用Pydantic的功能来实现这一点。

![](../Images/e73944428fb10a700832454a6feffeab.png)

图片来自作者（Dalle-3）

结构化输出解决了许多开发者面临的一个棘手问题，即当一个系统或过程从LLM获取输出以进一步处理时。对于该系统来说，了解期望的输入格式并能够准确处理它是非常重要的，这样每次都能得到可重复的结果。

同样，你希望每次向用户展示模型输出时，使用相同的格式，以避免混淆和错误。

到目前为止，确保大多数模型输出格式一致一直是个痛点，但Ollama的新功能让这一过程变得非常简单，正如我在示例代码片段中展示的那样。

但在此之前，你需要安装Ollama的最新版本。这不是关于Ollama或如何运行它的教程。如果你想要这些信息，请点击我下面的文章，我会详细介绍所有相关内容。
