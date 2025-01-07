# 一年的持续Kaggle实践：它教会了我什么？

> 原文：[https://towardsdatascience.com/1-year-of-continuous-kaggling-what-did-it-taught-me-d267c222cfa3?source=collection_archive---------1-----------------------#2024-05-11](https://towardsdatascience.com/1-year-of-continuous-kaggling-what-did-it-taught-me-d267c222cfa3?source=collection_archive---------1-----------------------#2024-05-11)

## 竞赛比其他组件更有价值

[](https://medium.com/@geremieyeo?source=post_page---byline--d267c222cfa3--------------------------------)[![Geremie Yeo](../Images/d218b1be15725937f7e70b6db49cb184.png)](https://medium.com/@geremieyeo?source=post_page---byline--d267c222cfa3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d267c222cfa3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d267c222cfa3--------------------------------) [Geremie Yeo](https://medium.com/@geremieyeo?source=post_page---byline--d267c222cfa3--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d267c222cfa3--------------------------------) ·阅读时长5分钟·2024年5月11日

--

[Kaggle](https://www.kaggle.com/)是一个让用户在实践数据科学和机器学习方面获得动手经验的平台。它有四个不同的进阶组件，分别是竞赛、数据集、笔记本和讨论。使用这个平台并开始学习不需要数据科学方面的先前经验。

我的背景：我在2023年初在我的本科学位（数学与计算机科学）课程中完成了第一个Kaggle项目，这个项目是机器学习课程的一部分。从那时起，我就迷上了这个平台，将其作为**最喜欢的消遣**。到目前为止，我参加了20场竞赛。开始使用Kaggle之前，我没有作为数据科学家的工作或实习经验。

在一年时间里，我在Kaggle旅程中取得了（我认为）显著的进展，包括赢得了2枚金奖竞赛奖牌，其中一项我获得了第一名，并且在竞赛类别中跻身前116名，同时几乎没有错过任何一天的活动。

[我的Kaggle个人主页](https://www.kaggle.com/yeoyunsianggeremie)

![](../Images/e919c5318c1a0d336047ed28f21b0232.png)![](../Images/6d05e765021b51e73713d913d93bc87c.png)

现在，让我们深入探讨我在Kaggle旅程中至今得到的三大关键学习。

1.  **你的团队不能仅仅依赖公开的笔记本在竞赛中获得成功**

标准的Kaggle竞赛只会颁发金奖给**前10名+ floor(NumTeams / 500)支队伍**！例如，在一个有2500支队伍的竞赛中，只有15支队伍能够获得金奖。要想晋升到竞赛中的**Master**等级，这**是强制性**要求，你需要**五枚金奖（其中至少一枚为个人赛金奖）**才能晋升到**Grandmaster**等级。

你的团队不太可能通过简单地修改公共工作（如集成公共笔记本）而获得金奖名次。你的团队将与顶尖的数据科学家和大师们竞争，他们有很多创造性的想法来解决问题。

简单修改公共工作是即使是机器学习初学者也能做到的事情，并且使用这种方法不太可能让你的团队的解决方案脱颖而出。很可能，稍微改进一个公共笔记本会获得铜奖，或者如果运气好，获得银奖。

在我们团队赢得金奖的两场比赛中：

+   1/2048（冠军）[PII检测](https://www.kaggle.com/competitions/pii-detection-removal-from-educational-data/overview)：我们使用了多种Deberta架构和后处理策略，其中大多数没有在公共论坛中分享。我们的最终集成模型中没有使用公共模型。

+   14/4436 [Optiver — 临近收盘交易](https://www.kaggle.com/competitions/optiver-trading-at-the-close/overview)：我们使用了在线训练，以确保模型在做出预测之前已经适配了最新的数据。编写在私有排行榜上有效的在线训练管道并不容易，据我所知，这个想法没有在公共论坛中分享。我们没有使用[流行的公共训练方法](https://www.kaggle.com/code/verracodeguacas/fold-cv)，因为我们认为它会对训练数据过拟合，尽管它的公共排行榜成绩非常好。

相比之下，这是我们团队获得铜奖的一个比赛：

+   185/2664 [LLM科学考试](https://www.kaggle.com/competitions/kaggle-llm-science-exam)：我们简单修改了一个[公共训练管道](https://www.kaggle.com/code/cdeotte/how-to-train-open-book-model-part-1)和一个[公共推理管道](https://www.kaggle.com/code/mbanaei/86-2-with-only-270k-articles)，例如更改了嵌入模型、超参数和集成方法。我们的最终方案中没有创意。

总结：在我看来，花更多时间分析基准，并进行研究以思考如何改进是更好的做法。可能从一个小模型（例如deberta-v3-xsmall）开始，以快速评估想法是一个不错的选择。目标是从一开始就建立一个稳健的交叉验证策略。

2\. **相比数据集/笔记本/讨论，你从比赛类别中学到的更多。**

我所学到的一些**真实世界**技能。

+   我是大部分比赛的队长，其中包括我们团队赢得金奖的两场比赛。这大大提升了我的沟通和领导能力。

+   与来自不同国家和时区的其他数据科学家/工程师合作，并向他们学习最佳实践。

+   使用[Wandb](https://wandb.ai/site)跟踪和记录实验。

+   定制化变换器模型的架构

+   使用LLM生成特定用例的合成数据集。

+   从数据科学角度建模真实世界的用例。

+   编写简洁、易于理解的代码。

+   如何利用多GPU训练

+   更好的时间管理

+   评估并减轻模型错误

相比之下，在数据集/笔记本/讨论中进展要容易得多，而无需深入学习数据科学。在讨论中，用户可以通过在Kaggle论坛发布自己的成就来获得金奖讨论奖牌。我怀疑如果没有参与竞赛，我大多数技能是无法学到的。在我看来，在数据集/笔记本/讨论中的进展不一定说明一个人对数据科学充满热情。

3\. **游乐场竞赛是初学者入门的好方式**

游乐场系列模拟了特色竞赛，只不过它更加适合初学者，并且不颁发奖牌/奖品。在游乐场中，你需要在表格数据集上做预测，这让你能够学习如何编写机器学习管道的基础。游乐场中有很多共享的笔记本，既有表格数据方法也有神经网络（NN）方法，所以如果你卡住了，那些公共笔记本是一个很好的参考。

每个游乐场系列的竞赛大约持续1个月。

根据我的经验，游乐场竞赛教会了我：

+   如何建立一个稳健的交叉验证策略，避免在公共排行榜上过拟合

+   如何选择提交内容进行评估

+   如何进行特征工程和特征选择

+   如何设计Jupyter笔记本的样式

+   （更多关于数据工程方面的内容）如何使用[Polars](https://pola.rs/)。这是一种比Pandas更快的DataFrame库，更适用于大数据场景

总结来说，我觉得做Kaggle最有收获的部分是竞赛中的实战经验，以及与全球数据专业人士合作的机会。我得以解决各种问题，从表格数据到更高级的自然语言处理任务。期待在数据科学领域继续提高自己！
