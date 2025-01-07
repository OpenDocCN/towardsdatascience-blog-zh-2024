# 学习排序 — 针对用户对的情境项目推荐

> 原文：[`towardsdatascience.com/learning-to-rank-contextual-item-recommendations-for-user-pairs-dc4f56e24d94?source=collection_archive---------6-----------------------#2024-03-26`](https://towardsdatascience.com/learning-to-rank-contextual-item-recommendations-for-user-pairs-dc4f56e24d94?source=collection_archive---------6-----------------------#2024-03-26)

## 训练一个机器学习推荐引擎，学习一群人共同的偏好

[](https://medium.com/@franckjay?source=post_page---byline--dc4f56e24d94--------------------------------)![Jay Franck](https://medium.com/@franckjay?source=post_page---byline--dc4f56e24d94--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc4f56e24d94--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc4f56e24d94--------------------------------) [Jay Franck](https://medium.com/@franckjay?source=post_page---byline--dc4f56e24d94--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc4f56e24d94--------------------------------) ·阅读时长 6 分钟·2024 年 3 月 26 日

--

![](img/10746b4c52d60313deafae632ae91b48.png)

图片来自[Lucrezia Carnelos](https://unsplash.com/@ciabattespugnose?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 本文教程适合…

1.  任何对 DIY 推荐感兴趣的人

1.  对基本的 PyTorch 排序模型感兴趣的工程师

1.  咖啡迷

# 本文教程不适合…

1.  想要将代码复制粘贴到生产系统中的人

1.  想要一个 TensorFlow 模型的人

# 动机

想象一下，你正坐在沙发上，朋友或家人陪伴在侧。你打开了自己喜爱的游戏主机/流媒体服务/音乐应用，每一项都像是闪闪发光的可能性之宝，为你量身定制。但这些个性化的结果可能只是你独自一人的版本，并不能反映你在这群人中时的样子。

这个项目真的是从咖啡开始的。我迷恋自己烘焙来自 Sweet Maria's（无任何关联）的绿咖啡豆，因为它有各种美味的可能性。[哥伦比亚](https://www.sweetmarias.com/colombia-honey-aponte-hugo-agreda-7759.html)? [爪哇豆](https://www.sweetmarias.com/java-dry-process-kuningan-robusta-7286.html)? [肯尼亚圆粒](https://www.sweetmarias.com/kenya-kiambu-ngaita-peaberry-7241.html)? 每一种描述都比上一种更加诱人。即使是我自己作为个人，也很难做出选择。如果你是为家人或客人购买绿咖啡豆，情况会怎样呢？

我想创建一个“学习排名”（LTR）模型，可能解决这个咖啡难题。对于这个项目，我首先构建了一个简单的 [TensorFlow Ranking](https://www.tensorflow.org/ranking) 项目，来预测不同咖啡的用户对偶排名。我对 TFR 有一些经验，因此它似乎是一个自然的选择。

然而，我意识到我之前从未从零开始构建过一个排名模型！于是我着手构建了一个非常简陋的 PyTorch 排名模型，看看我能否快速搭建一个并在过程中学习一些东西。显然，这并不适用于生产系统，我在过程中做了很多捷径，但这的确是一次极好的教学体验。

# 数据

![](img/c39e6fe51959571e8377a161bee7b3cb.png)

照片由 [Pritesh Sudra](https://unsplash.com/@pritesh557?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们的终极目标如下：

+   开发一个学习用户对偶偏好的排名模型

+   将此应用于预测 `k` 个项目的按列表排名

用户和项目特征组合中可能包含的信号是什么，以为该用户对提供一组推荐？

为了收集这些数据，我不得不和我的妻子一起进行痛苦的咖啡品鉴研究。然后我们各自为这些咖啡打分，满分为 10 分。目标值只是我们两人分数的总和（最大为 20 分）。模型的目标是学习排名我们都喜欢的咖啡，而不仅仅是某一对中的一个成员。我们将使用的上下文数据如下：

+   一对用户的年龄

+   用户 ID，将转换为嵌入向量

SweetMarias.com 提供了大量的项目数据：

+   咖啡的来源

+   加工和培养笔记

+   品尝描述

+   专业评分（100 分制）

因此，对于每个训练示例，我们将把用户数据作为上下文信息，每个项目的特征集将被连接在一起。

TensorFlow Ranking 模型通常在 [ELWC](https://www.tensorflow.org/ranking/tutorials/ranking_dnn_distributed#elwc_data_formats_for_ranking) 格式的数据上进行训练：ExampleListWithContext。你可以把它想象成一个字典，包含两个键：CONTEXT 和 EXAMPLES（列表）。每个 EXAMPLE 内部是一个字典，包含每个你想排名的项目的特征。

例如，假设我正在寻找一款新的咖啡来尝试，系统展示了一个包含 k=10 种咖啡品种的候选池。一个 ELWC 将包括上下文/用户信息，以及一个包含 10 个项目的列表，每个项目都有自己的特征集。

由于我不再使用 TensorFlow Ranking，我自己做了一个简陋的排名/列表构建部分。我随机挑选了 k 个项目样本，并为它们评分，然后将它们添加到一个列表中。我把最初尝试的咖啡分成了训练集，之后的示例则成为了一个小的验证集，用来评估模型。

# 特征直觉

在这个玩具示例中，我们拥有一个相当丰富的数据集。从上下文角度来看，我们明显知道用户的年龄，并能学习到他们各自的偏好嵌入。通过 LTR 模型内部的后续层，这些上下文特征可以进行比较和对比。例如，是否一对用户中，一个喜欢浓郁的水果味，而另一个则喜欢杯中清新的柑橘和水果味呢？

![](img/bd27ef43ea5e42371aedad3d073eac48.png)

图片由[Nathan Dumlao](https://unsplash.com/@nate_dumlao?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

对于商品特征，我们拥有丰富的描述性文本，包括每种咖啡的品尝笔记、产地等。稍后会详细介绍，但总体来说，我们可以捕捉这些描述的含义，并将这些描述与上下文（用户对）数据进行匹配。最后，我们还有一些数值特征，比如每种商品的专家品尝评分，这些（应该）与现实有一定的相似性。

# 数据预处理

自从我刚开始从事机器学习行业以来，文本嵌入技术发生了惊人的变化。那些我曾用来尝试捕捉词语或短语语义的 GLOVE 和 Word2Vec 模型早已不再使用。如果你访问[`huggingface.co/blog/mteb`](https://huggingface.co/blog/mteb)，你可以轻松比较最新最强的嵌入模型，用于各种目的。

为了简化和熟悉，我们将使用[`huggingface.co/BAAI/bge-base-en-v1.5`](https://huggingface.co/BAAI/bge-base-en-v1.5)的嵌入模型，帮助我们将文本特征投射到 LTR 模型能够理解的格式。具体来说，我们将使用这个模型来处理 Sweet Marias 提供的产品描述和产品名称。

我们还需要将所有用户和商品的 ID 值转换为嵌入空间。PyTorch 通过[Embedding](https://pytorch.org/docs/stable/generated/torch.nn.Embedding.html)层很好地处理了这一点。

最后，我们对浮动特征进行简单的[RobustScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html)缩放。所有这些都可以在我们的 Torch 数据集类内部完成，然后将其输入到用于训练的 DataLoader 中。关键在于分离出不同的标识符，这些标识符将在 PyTorch 的`forward()`调用中传递。[Offir Inbar](https://www.linkedin.com/in/offir-inbar/)的这篇文章真的是帮我节省了不少时间！

# 模型构建与训练

关于 Torch 训练，唯一有趣的事情是确保两个用户嵌入（每个评分者一个）和训练列表中的`k`种咖啡具有正确的嵌入和维度，以便通过我们的神经网络。经过一些调整，我终于得到了一些结果：

这个前向传播将每个训练示例推入一个包含所有特征的单一连接列表中。

由于数据点如此之少（仅有 16 种咖啡被评分），训练一个强大的神经网络模型可能会很困难。我通常会并排构建一个简单的 `sklearn` 模型，以便比较结果。我们真的学到了什么吗？

使用相同的数据准备技术，我构建了一个 LogisticRegression 多类分类器模型，然后将 `.predict_proba()` 的得分输出，用作我们的排序。我们的指标能告诉我们这两个模型的表现如何吗？

# 结果

对于指标，我选择追踪两个：

1.  Top（`k=1`）准确率

1.  [NDCG](https://en.wikipedia.org/wiki/Discounted_cumulative_gain#Normalized_DCG)

目标，当然，是正确排序这些咖啡。NDCG 在这里非常适用。不过，我怀疑 LogReg 模型在排序方面可能会遇到困难，所以我考虑可能也加入一个简单的准确率指标。有时你只想要一杯真正好的咖啡，而不需要排序！

在我没有进行任何显著的参数调整的情况下，两个模型的结果非常相似。SKLearn 在（极小的）验证集上的 NDCG 稍微差一些（0.9581 对比 0.950），但准确率相似。我相信通过对 PyTorch 模型和 LogReg 模型进行一些超参数调优，结果在如此少的数据情况下会非常相似。不过至少它们的大致结论一致！

# 未来工作

我有一批新的 16 磅咖啡开始进行排名，并且故意将一些不太为人知的品种加入其中。我希望能稍微整理一下仓库，让它看起来不那么像一个临时的黑客作品。另外，我还需要为未见过的咖啡添加一个预测函数，以便我能搞清楚下次该买什么！

需要注意的一点是，如果你正在为生产环境构建推荐系统，通常使用一个专门为排序构建的真实库是个不错的主意。TensorFlow Ranking、XGBoost、LambdaRank 等在业界被广泛接受，并且解决了许多痛点。

请查看[这里](https://github.com/franckjay/UserPairRecommendationEngine)的仓库，并告诉我如果你发现任何 bug！希望你能受到启发，训练你自己的用户对排序模型。
