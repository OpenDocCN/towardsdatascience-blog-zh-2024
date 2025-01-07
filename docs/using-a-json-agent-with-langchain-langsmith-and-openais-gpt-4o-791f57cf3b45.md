# 使用LangChain、LangSmith和OpenAI的GPT-4o的JSON代理

> 原文：[https://towardsdatascience.com/using-a-json-agent-with-langchain-langsmith-and-openais-gpt-4o-791f57cf3b45?source=collection_archive---------2-----------------------#2024-07-01](https://towardsdatascience.com/using-a-json-agent-with-langchain-langsmith-and-openais-gpt-4o-791f57cf3b45?source=collection_archive---------2-----------------------#2024-07-01)

## 开发一个聊天机器人来回答关于JSON数据集的问题

[](https://medium.com/@ben_olney?source=post_page---byline--791f57cf3b45--------------------------------)[![Ben Olney](../Images/8651634da3b08f8ee1280b0e7636cde4.png)](https://medium.com/@ben_olney?source=post_page---byline--791f57cf3b45--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--791f57cf3b45--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--791f57cf3b45--------------------------------) [Ben Olney](https://medium.com/@ben_olney?source=post_page---byline--791f57cf3b45--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--791f57cf3b45--------------------------------) ·阅读时长8分钟·2024年7月1日

--

![](../Images/5ed9db195474193bb9bbb69eabb5b560.png)

图片来源：OpenAI，作者编辑

# 挑战

最近，我面临了一个挑战：开发一个能够使用LLM和预定义的学生数据（JSON格式）回答关于JSON数据集问题的聊天机器人。故意地，这些JSON数据结构混乱，在某些情况下非常嵌套，可能代表来自旧系统的数据库调用。

# 为什么不直接将JSON上传到ChatGPT？

简单地在数据上调用ChatGPT可能无法产生最佳结果，而且也不具备可扩展性。由于JSON数据是一种高度结构化和嵌套的数据形式，关于数据点之间复杂关系的提问更容易产生虚假回答。我开始研究一个能够使用专门工具执行任务并处理这些复杂性高级代理。

# 选择工具

最受欢迎的LLM框架之一是LangChain，它提供了先进的提示工具。它还通过LangSmith提供了扩展功能，LangSmith是一个服务（用最简单的术语来说），提供一个仪表盘来监控LLM调用和代理运行——并详细列出行动链。
