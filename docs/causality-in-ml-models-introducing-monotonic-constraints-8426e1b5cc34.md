# 机器学习模型中的因果关系：引入单调约束

> 原文：[`towardsdatascience.com/causality-in-ml-models-introducing-monotonic-constraints-8426e1b5cc34?source=collection_archive---------1-----------------------#2024-09-06`](https://towardsdatascience.com/causality-in-ml-models-introducing-monotonic-constraints-8426e1b5cc34?source=collection_archive---------1-----------------------#2024-09-06)

## 单调约束是使机器学习模型可操作的关键，但它们仍然被很少使用

[](https://medium.com/@mazzanti.sam?source=post_page---byline--8426e1b5cc34--------------------------------)![Samuele Mazzanti](https://medium.com/@mazzanti.sam?source=post_page---byline--8426e1b5cc34--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8426e1b5cc34--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8426e1b5cc34--------------------------------) [Samuele Mazzanti](https://medium.com/@mazzanti.sam?source=post_page---byline--8426e1b5cc34--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8426e1b5cc34--------------------------------) ·阅读时间：9 分钟·2024 年 9 月 6 日

--

![](img/5391cf5d3068c67d8d85f7d85e8e3df7.png)

[作者图片]

因果关系正迅速成为每个数据科学家工具包中的一个重要组成部分。

而且有充分的理由。

事实上，因果模型在商业中非常有价值，因为它们为“假设情境”提供了更可靠的估计，特别是在用于做出影响商业结果的决策时。

在这篇文章中，我将展示一个简单的变化——实际上只需要添加一行代码——如何将传统的机器学习模型（如随机森林、LightGBM、CatBoost 等）转变为一个可靠的工具，用于回答因果性问题。

# 因果机器学习模型与传统机器学习模型

> 你可以通过[这个笔记本](https://github.com/smazzanti/tds_this_line_of_code_will_turn_your_model_into_a_causal_model/blob/main/tds_this_line_of_code_will_turn_your_model_into_a_causal_model.ipynb)复制文章中展示的所有结果。使用的数据集是来自[Pycaret](https://github.com/pycaret/pycaret)（一个遵循[MIT 许可证](https://github.com/pycaret/pycaret/blob/master/LICENSE)的 Python 库）的“房屋”数据集。

假设我们为一家房地产公司工作。公司的业务包括购买房屋并以更高的价格转售。

我们收集了关于过去交易中涉及的房屋的数据。数据集包含三个变量：
