# 使用 SOLID 原则扩展你的机器学习项目

> 原文：[`towardsdatascience.com/scale-your-machine-learning-projects-with-solid-principles-824230fa8ba1?source=collection_archive---------1-----------------------#2024-03-12`](https://towardsdatascience.com/scale-your-machine-learning-projects-with-solid-principles-824230fa8ba1?source=collection_archive---------1-----------------------#2024-03-12)

## 如何编写可扩展且能加速你作为数据科学家或机器学习工程师工作流程的代码。

[](https://medium.com/@jeremyarancio?source=post_page---byline--824230fa8ba1--------------------------------)![Jeremy Arancio](https://medium.com/@jeremyarancio?source=post_page---byline--824230fa8ba1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--824230fa8ba1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--824230fa8ba1--------------------------------) [Jeremy Arancio](https://medium.com/@jeremyarancio?source=post_page---byline--824230fa8ba1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--824230fa8ba1--------------------------------) ·13 分钟阅读·2024 年 3 月 12 日

--

当我还是一名初级数据科学家时，我的目标只是编写能够正常工作的代码。

我曾经把 Python 仅仅看作是一个用来运行 Pandas、Numpy 或 Matplotlib 的框架。我像其他人一样从 Jupyter Notebook 开始，一行一行地处理数据和训练模型。

我记得我在公司里的第一份工作。

随着项目的推进，笔记本变得越来越庞大，尽管使用 markdown 提供了解释，代码开始变得凌乱。

第一个模型终于训练完毕，性能评估完成，并在开发人员的帮助下被部署到生产环境。

然而，像任何机器学习项目一样，部署模型并不是旅程的终点，而是开始…

几周后，我不得不从头开始并重新审视笔记本。说实话，几乎更容易创建一个新的笔记本。需求发生了变化，代码太混乱，根本无法进行任何修改。

此外，将处理算法部署到生产环境中是一个痛苦的任务。数据必须在笔记本、训练管道和推理管道中进行一致的处理。

需要写三遍代码意味着，笔记本中的任何修改都需要…
