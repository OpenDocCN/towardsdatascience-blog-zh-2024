# 使用Python防护措施提高LLM输出可靠性

> 原文：[https://towardsdatascience.com/improve-llm-output-reliability-with-python-guardrails-cc8163b26b0c?source=collection_archive---------2-----------------------#2024-05-31](https://towardsdatascience.com/improve-llm-output-reliability-with-python-guardrails-cc8163b26b0c?source=collection_archive---------2-----------------------#2024-05-31)

## **利用验证函数防止你的LLM输出跌入深渊**

[](https://medium.com/@janekmajewski?source=post_page---byline--cc8163b26b0c--------------------------------)[![Jan Majewski](../Images/2d06418ffe9f14cb558ebaec7f871cf0.png)](https://medium.com/@janekmajewski?source=post_page---byline--cc8163b26b0c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cc8163b26b0c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cc8163b26b0c--------------------------------) [Jan Majewski](https://medium.com/@janekmajewski?source=post_page---byline--cc8163b26b0c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cc8163b26b0c--------------------------------) ·9分钟阅读·2024年5月31日

--

![](../Images/86a4d810908650517115e0e7c31cc280.png)

图片由作者生成

# 合适的IF语句可以让你的LLM输出更可靠

虽然LLM在创意和解决复杂任务方面表现出色，但它们往往难以遵循严格的规则，经常提供稍微超出设定边界的答案。在构建应用时，这个缺陷可能导致失败和荒谬的答案，从而让用户放弃使用。

好消息是，编写严格的规则是每个程序员的基本功，而且仅仅因为我们现在使用LLM，并不意味着我们忘记了如何使用if语句。

> **如果某些逻辑可以通过几行代码来强制执行，你可能不需要将其输入到一个万亿参数的模型中**

# LLM的最后一公里交付问题

控制输出在将LLM与应用的其他组件（如API）集成时尤为重要。在开发一个*text_to_params*工具时，我需要将用户的公寓需求描述转化为搜索参数。我意识到，尽管生成的参数通常是正确的，但它们往往会…
