# 《思考，快与慢》、大语言模型（LLMs）与规划领域定义语言（PDDL）

> 原文：[https://towardsdatascience.com/thinking-fast-and-slow-with-llms-and-pddl-111699f9907e?source=collection_archive---------4-----------------------#2024-06-05](https://towardsdatascience.com/thinking-fast-and-slow-with-llms-and-pddl-111699f9907e?source=collection_archive---------4-----------------------#2024-06-05)

## ChatGPT从不害怕假装进行深思熟虑的推理，但——就像我们的大脑一样——可能需要额外的工具来进行准确的推理。

[](https://medium.com/@nikolaus.correll?source=post_page---byline--111699f9907e--------------------------------)[![Nikolaus Correll](../Images/948c44fe797b8057e20b39023c30027b.png)](https://medium.com/@nikolaus.correll?source=post_page---byline--111699f9907e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--111699f9907e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--111699f9907e--------------------------------) [Nikolaus Correll](https://medium.com/@nikolaus.correll?source=post_page---byline--111699f9907e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--111699f9907e--------------------------------) ·15分钟阅读·2024年6月5日

--

“ChatGPT可能会出错。请核实重要信息。”现在已写在提示下方，我们也都习惯了ChatGPT冷静地编造从日期到完整参考文献的任何内容。但那基本推理呢？通过回顾人工智能（AI）研究早期的一个简单塔楼重排任务，我们将展示大语言模型（LLM）如何达到其局限，并引入规划领域定义语言（PDDL）和符号求解器来弥补这一点。鉴于LLM本质上是概率性的，未来的AI代理版本很可能会内建此类工具，结合常识知识和精准推理。为了充分利用本文的内容，使用[VS Code的PDDL扩展](https://marketplace.visualstudio.com/items?itemName=jan-dolejsi.pddl)和[planutils](https://github.com/AI-Planning/planutils)规划器界面，搭建自己的PDDL环境，并与示例一起实践。

*如果你不是Medium的订阅者，可以* [*在这里*](https://medium.com/towards-data-science/thinking-fast-and-slow-with-llms-and-pddl-111699f9907e?sk=8792c884cc6498579bdd1cca6c5e00cb)* 阅读这篇文章*

在大语言模型（LLM）中，每个字符实际上是根据其响应中所有前面的字符以及用户的提示进行条件化的。经过几乎所有曾经写过的内容的训练，LLM不仅变得无所不知，甚至还具有幽默感。然而，通常不久后我们就会发现LLM非常懒惰，本质上…
