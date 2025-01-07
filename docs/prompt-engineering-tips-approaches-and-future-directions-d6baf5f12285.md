# 提示工程：技巧、方法与未来发展

> 原文：[`towardsdatascience.com/prompt-engineering-tips-approaches-and-future-directions-d6baf5f12285?source=collection_archive---------1-----------------------#2024-06-27`](https://towardsdatascience.com/prompt-engineering-tips-approaches-and-future-directions-d6baf5f12285?source=collection_archive---------1-----------------------#2024-06-27)

[](https://towardsdatascience.medium.com/?source=post_page---byline--d6baf5f12285--------------------------------)![TDS Editors](https://towardsdatascience.medium.com/?source=post_page---byline--d6baf5f12285--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d6baf5f12285--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6baf5f12285--------------------------------) [TDS Editors](https://towardsdatascience.medium.com/?source=post_page---byline--d6baf5f12285--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6baf5f12285--------------------------------) ·通过 Newsletter 发送 ·阅读时间：4 分钟 ·2024 年 6 月 27 日

--

> 想要激发写下你的第一篇 TDS 文章吗？[我们始终欢迎新作者的投稿](http://bit.ly/write-for-tds)。

当提示工程首次作为数据和机器学习专业人士的主流工作流程出现时，它似乎产生了两种常见的（且有些对立的）观点。

在 ChatGPT 轰动登场之后，一些评论者宣称，提示工程是一个必不可少的任务，很快将接管整个产品和机器学习团队；随之而来的是针对提示工程师的六位数职位发布。同时，怀疑者则认为这不过是填补大语言模型当前能力空白的中介方法，随着模型性能的提升，对专业提示知识的需求将逐渐消失。

近两年后，双方似乎都提出了有效的观点。提示工程仍然深深地存在于我们的工作中；它作为一种实践持续发展，并有越来越多的工具和技术支持从业者与强大模型的互动。然而，随着生态系统的成熟，优化提示可能不再是一个专门的技能，而是一种思维和解决问题的方式，融入到广泛的专业活动中。

为了帮助你评估提示工程的当前状态，了解最新的研究方法，并展望该领域的未来，我们汇集了近期一些关于该主题的精彩文章。祝你阅读愉快！

+   **领域适应介绍——动机、选项、权衡**对于任何刚开始动手操作 LLM 的人来说，[Aris Tsakpinis](https://medium.com/u/8ab3accce432?source=post_page---user_mention--d6baf5f12285--------------------------------)的三部分系列文章是一个探索如何使这些庞大、难以驾驭且有时不可预测的模型产生可靠结果的绝佳起点。第一部分尤其很好地介绍了提示工程：为什么它是必要的，它是如何工作的，以及它迫使我们考虑的权衡。

+   **我获得了人工智能认证。这是它让我学到的关于提示工程的知识。**“提示工程是一个简单的概念。它只是通过提供指令的方式要求大型语言模型（LLM）完成任务。” [Kory Becker](https://medium.com/u/9f206469e308?source=post_page---user_mention--d6baf5f12285--------------------------------)以一位希望与时俱进的资深软件开发者的角度，带我们了解人类和模型互动中那些有时反直觉的方式。

+   **使用 DSPy 和 Haystack 自动化提示工程**

    许多已经在提示设计和执行方面进行过尝试的机器学习专业人士迅速意识到，在提示设计和执行上有*大量*的空间可以进行简化和优化。[Maria Mestre](https://medium.com/u/7e4830fd2369?source=post_page---user_mention--d6baf5f12285--------------------------------)最近分享了一个清晰的、逐步的教程——专注于开源框架 DSPy——为任何希望自动化这部分工作流程的人提供了指导。

![](img/bbcbee5c82d24e5ade4c7e1270508ec5.png)

图片来源：[Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的照片

+   **理解解决生成性人工智能挑战的技术**我们往往关注提示工程的具体实现方面，但和其他 LLM 优化技术一样，它也为产品和业务利益相关者提出了一整套问题。[Tula Masterman](https://medium.com/u/ce8c2ab0804a?source=post_page---user_mention--d6baf5f12285--------------------------------)的新文章是一个方便的概述，很好地提供了“何时考虑不同方法以及如何将它们结合以获得最佳结果的指导”。

+   **简化你的提示以减少 LLM 的成本和延迟** 一旦你建立了一个有效的提示工程系统，就可以开始专注于使其更高效、更节省资源的方法。想要获取有关如何朝这个方向迈进的实用建议，别错过[Jan Majewski](https://medium.com/u/bf80a4208270?source=post_page---user_mention--d6baf5f12285--------------------------------)提供的五条优化提示使用令牌的建议（但不会牺牲准确性）。

+   **从提示工程到代理工程** 如果你想深入了解该领域未来可能的发展方向，希望你能看看[Giuseppe Scalamogna](https://medium.com/u/e039aa8b7221?source=post_page---user_mention--d6baf5f12285--------------------------------)的高层次分析：“似乎有必要开始从提示工程过渡到更广泛的领域，也就是代理工程，并建立适当的框架、方法论和思维模型来有效设计它们。”

本周准备拓展一些其他话题吗？以下是几篇值得一读的亮眼文章：

+   [Payal Patel](https://medium.com/u/609389be1ac2?source=post_page---user_mention--d6baf5f12285--------------------------------)邀请我们探索开源的 LIDA 库，它将 LLM 的强大功能与生成数据可视化的能力结合起来，并提供了如何入门的实用指导。

+   LLMs 在日益增长的自动驾驶汽车生态系统中能扮演什么角色？[Shu Ishida](https://medium.com/u/53b23b891950?source=post_page---user_mention--d6baf5f12285--------------------------------)分享了他们关于 LLMs 潜力的最新研究成果，旨在“编写自动驾驶的代码”。

+   在她的首篇 TDS 文章中，[Nicole Ren](https://medium.com/u/42a2dbebb150?source=post_page---user_mention--d6baf5f12285--------------------------------)带领我们了解了她参与的一个有前景的生成式 AI 项目，该项目旨在简化新加坡公共部门工作人员的报告写作。

+   别错过[Sachin Date](https://medium.com/u/b75b5b1730f3?source=post_page---user_mention--d6baf5f12285--------------------------------)最新的统计分析深度探讨，在这篇文章中，他转向了部分自相关系数（PACF），并聚焦其在配置自回归（AR）模型中的应用以处理时间序列数据集。

+   如果你是数据科学教育者或黑客马拉松组织者，你应该了解一下[路易斯·费尔南多·佩雷斯·阿尔马斯博士](https://medium.com/u/355ab04cdc6f?source=post_page---user_mention--d6baf5f12285--------------------------------)的详细创建私有 Kaggle 平台指南，在这个平台上，学习者可以分享、比较和讨论他们的工作。

+   从失业中恢复不仅仅是学习新技能或简单地提交简历。[Amy Ma](https://medium.com/u/d6d8df787b?source=post_page---user_mention--d6baf5f12285--------------------------------)分享了她在失业一年期间的一些有用见解和经验。

感谢您支持我们作者的工作！我们热衷于发布新作者的文章，因此，如果您最近写了一篇有趣的项目流程、教程或关于我们核心主题的理论思考，请毫不犹豫地[与我们分享](http://bit.ly/write-for-tds)。

直到下次的《Variable》。

TDS 团队
