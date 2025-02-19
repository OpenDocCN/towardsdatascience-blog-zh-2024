# 如何赢得一场价值 10,000 美元的写作比赛

> 原文：[`towardsdatascience.com/how-i-won-a-10-000-essay-writing-contest-e0a77e26c765?source=collection_archive---------11-----------------------#2024-01-08`](https://towardsdatascience.com/how-i-won-a-10-000-essay-writing-contest-e0a77e26c765?source=collection_archive---------11-----------------------#2024-01-08)

## 数据科学中的沟通

[](https://medium.com/@rhyscook92?source=post_page---byline--e0a77e26c765--------------------------------)![Rhys Cook](https://medium.com/@rhyscook92?source=post_page---byline--e0a77e26c765--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e0a77e26c765--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e0a77e26c765--------------------------------) [Rhys Cook](https://medium.com/@rhyscook92?source=post_page---byline--e0a77e26c765--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e0a77e26c765--------------------------------) ·阅读时长：5 分钟·2024 年 1 月 8 日

--

最近，我很幸运地赢得了 Kaggle 的一个写作比赛，并将我的作品发表在他们的[2023 年 AI 报告](https://www.kaggle.com/AI-Report-2023)中，尽管我绝不认为自己是一个完美的沟通者，但我想分享一些我认为帮助我赢得奖项的经验教训！

我特别为这个成就感到自豪，因为在我的经验中，作为数据科学家，我们所做的最重要但常常被忽视的一件事就是沟通和分享我们的结果。

在这篇文章中，我们将讨论使用何种类型的语言、如何谈论抽象概念、如何利用图表传达模式，以及整体结构等内容。希望这些技巧和方法能在下次你进行沟通或演讲时对你有所帮助！

![](img/7f07e605e2c0cf8932ccf29069635475.png)

图片来源：作者。（AI 生成）

# **第一条：避免行话**

听起来很明显，对吧？但这件事尤其难——我们上大学/学院，或学习课程来提升我们的技能，这些课程向我们介绍了一系列的技术概念，每个概念都有一个特定的名称，我们逐渐理解它们。接下来，无论何时与其他学生交流，我们都使用这些术语，最终，等我们进入行业时，这些技术术语已经深深烙印在我们心中，我们不加思索地使用它们。

**这有什么问题吗？在现实世界中，这些词对大多数人来说毫无意义。**

让我给你举个例子——在表格数据领域，你可能正在训练一个模型，试图最小化一个基于缩略词的误差指标，比如平均预测误差（MAPE）。然而，通常我们与之沟通的人根本不理解这些概念。那么当我们告诉他们我们在最新的预测模型中如何降低 MAPE 时会发生什么呢？他们肯定不会感到印象深刻——最多他们会失去兴趣——最糟糕的情况是，他们可能会感到不安， wondering 是否应该知道我们在说什么。

那么解决方案是什么呢？关键是用更简单的日常语言来沟通复杂的概念。

在上面的例子中，当然我们仍然想突出我们的模型是准确的，但我们更好的做法是用简单的英语来解释，比如说我们已经提高了平均百分比误差。更好的做法是从模型的目标出发进行解释。例如，如果我们在预测天气，我们可能会说，我们把 12 小时预报的摄氏度误差从 5%降到了 1%。

# **第 2 点：使用例子**

让我们面对现实吧——数据科学中的许多概念都非常抽象。你曾试过向妈妈解释你整天在做什么吗？是的，我也试过，结果从来不太好。

好消息是，大多数抽象概念可以通过一个具体的现实世界例子来解释清楚。在我附上的文章中，我尝试通过至少一个例子，甚至多个例子来让每个观点变得清晰。这在口头解释概念时也是如此，尤其是在与技术背景较少的听众沟通时更为重要。

那么哪些例子效果最好呢？这里的黄金法则是，例子与受众的相关性越强，效果越好。例如，在我的文章中，我经常以 Kaggle 项目为例，因为我知道阅读这篇文章的每个人都在 Kaggle 上，并且能产生共鸣！如果你是在向老师们传达大语言模型（LLM）的价值，或许你会提到它们在总结教科书章节，或者提供定制写作反馈方面的应用。

**核心观点是：了解你的受众，内容自然就会跟随！**

# **第 3 点：使用（清晰的）视觉效果**

视觉效果很有趣！至少我觉得是...

一个巧妙的视觉效果或图形可以为任何演示增色不少。我个人认为，一个好的视觉效果是通过一种应该能让目标模式显而易见的方式来生成的。

以这个简单的注释表格为例——它展示了在 Kaggle 举办的简单表格回归和分类比赛中，获胜解决方案使用的模型类型。即便什么都不懂，我们的眼睛也能看到一个模式：LightGBM 和集成方法的列中有很多勾选标记，所以无论这些是什么，它们肯定很受欢迎。这是一个简单的见解，但通过将数据以恰当的方式排列，结果变得显而易见。

**清晰的视觉效果不仅能吸引注意力，还能使你的数据叙事更加有效且值得记住，并且能节省大量的解释时间。**

![](img/e587e6c4190089af8f3e16e5049bf02d.png)

图片由作者提供。

# **第 4 条：强调关键点**

我从小就不喜欢写文章——事实上，考虑到可能读这篇文章的人，我敢打赌你们大多数人也不喜欢！但有一个教训一直伴随着我，那就是我高中老师简单的文章结构——首先告诉他们，然后再告诉他们，然后再告诉他们一次，也就是说，介绍、正文和结论都在说同样的事情。

当时我并没有真正理解，但后来我一次又一次地看到了其中的智慧。如果你直接开始一个演示，那么人们就不会明白他们应该关注什么。如果你忘记总结演示，人们可能只会记住你的最后一个要点！为了避免这些陷阱，几乎总是从你的关键点开始，详细阐述它们，然后最后再总结一次，这样的结构无论在哪种媒介中都很有用——从文章到演示文稿。

这在数据科学中尤为重要，因为考虑到行业变化的速度，总有新的话题值得讨论，而且可能并不是每个人都像你一样理解你的专业领域，所以重复有助于突出主要观点。

所以，考虑到这一点，让我们总结一下我们的一些关键点……（看到了吗？ :) ）

# 结论

1.  避免使用行话——不要让观众费劲地去理解最新的流行词汇或缩略语。

1.  尽可能使用真实世界的例子——如果它们与目标观众相关，那就更棒了。

1.  包括简单的视觉效果——确保信息明确。

1.  也不要忘记清晰结构的重要性。

希望在考虑到这些想法后，你能够更好地吸引下一个观众的注意力。

感谢阅读，下次见！
