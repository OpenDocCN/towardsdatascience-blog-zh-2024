# 数据最小化并不保证隐私

> 原文：[https://towardsdatascience.com/data-minimization-does-not-guarantee-privacy-544ca15c7193?source=collection_archive---------10-----------------------#2024-10-28](https://towardsdatascience.com/data-minimization-does-not-guarantee-privacy-544ca15c7193?source=collection_archive---------10-----------------------#2024-10-28)

## 仅仅因为数据被最小化了，并不意味着它是安全的！

[](https://medium.com/@prakhargannu?source=post_page---byline--544ca15c7193--------------------------------)[![Prakhar Ganesh](../Images/431b1f71bf5f85e3a5105a1e2eb84459.png)](https://medium.com/@prakhargannu?source=post_page---byline--544ca15c7193--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--544ca15c7193--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--544ca15c7193--------------------------------) [Prakhar Ganesh](https://medium.com/@prakhargannu?source=post_page---byline--544ca15c7193--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--544ca15c7193--------------------------------) ·阅读时长4分钟·2024年10月28日

--

*基于我们的论文* [*机器学习中的数据最小化原则*](https://arxiv.org/abs/2405.19471) *由Prakhar Ganesh、Cuong Tran、Reza Shokri和Ferdinando Fioretto撰写*

# 数据最小化原则

数据驱动系统和机器学习应用的激增加剧了许多隐私风险，包括**未授权访问敏感信息**的风险。为应对这些风险，国际数据保护框架，如*欧洲通用数据保护条例（GDPR）*、*加利福尼亚隐私权法案（CPRA）*、*巴西通用数据保护法（LGPD）*等，已将***数据最小化***作为缓解这些风险的关键原则。

![](../Images/ff3711017864ce681ddb75a5a6a1dea6.png)

来自全球六个不同数据保护法规的**数据最小化原则**摘录。图片来源：作者。

数据最小化原则的核心要求组织*仅收集、处理和保留足够、相关且仅限于为特定目标所必需的个人数据*。其基础是认为并非所有数据都是必要的，数据的过度收集反而会增加信息泄露的风险。数据最小化原则建立在两个核心支柱上，*目的限制*和*数据相关性*。

## 目的限制

数据保护法规要求收集数据的目的是为了*合法、具体且明确的目的*（LGPD，巴西），并且禁止将收集的数据用于与公开声明目的不兼容的任何其他目的（CPRA，美国）。因此，数据收集者必须在数据收集前定义明确的法律目标，并且仅将数据用于该目标。在机器学习（ML）环境中，这个*目的*可以被看作是为了训练模型以在特定任务上实现最佳表现而收集数据。

## 数据相关性

像GDPR这样的法规要求所有收集的数据*必须是充分的、相关的，并且仅限于为其收集目的所必需的数据*。换句话说，数据最小化旨在删除不符合上述目的的数据。在机器学习的环境中，这意味着仅保留对模型性能有贡献的数据。

![](../Images/121ab7da97782b47e5546b1fa9c831d6.png)

**数据最小化。** 作者提供的图像

# 通过最小化的隐私期望

正如你可能已经注意到的，数据保护法规通过最小化隐含地期望隐私保护。数据最小化原则甚至在公共讨论中被许多人推崇（[EDPS](https://www.edps.europa.eu/data-protection/data-protection/glossary/d_en)，[Kiteworks](https://www.kiteworks.com/risk-compliance-glossary/data-minimization/)，[The Record](https://therecord.media/lawmakers-set-sights-on-data-minimization-with-new-bills)，[Skadden](https://www.skadden.com/insights/publications/2024/04/cppas-first-enforcement-advisory)，[k2view](https://www.k2view.com/blog/data-minimization/)）作为保护隐私的原则。

欧盟《人工智能法案》在[第69条说明](https://www.euaiact.com/recital/69)中指出，“*隐私权和个人数据保护权必须在整个人工智能系统生命周期中得到保障。在这方面，数据最小化原则以及通过设计和默认方式进行的数据保护原则，按照欧盟数据保护法，适用于个人数据的处理*”。

***然而，这种通过最小化隐私期望忽视了现实数据中的一个关键方面——各种特征之间的内在关联性！*** 个人信息很少是孤立的，因此，仅仅进行数据最小化，仍然可能允许进行有信心的重构。这就造成了一个空白，在这个空白中，使用数据最小化操作化尝试的个人或组织，可能期望隐私得到改善，尽管使用的框架仅限于最小化。

# 正确的隐私讨论方式

隐私审计通常包括进行攻击来评估实际的信息泄露。这些攻击是揭示潜在漏洞的有力工具，通过模拟现实场景，审计员可以评估隐私保护机制的有效性，并识别可能泄露敏感信息的领域。

在这种情况下，可能相关的一些对抗性攻击包括重建攻击和重新识别攻击。*重建攻击旨在从目标数据集中恢复缺失的信息。* *重新识别攻击旨在使用部分或匿名化数据重新识别个体。*

![](../Images/b5f87e2934643a0718398b8744a28019.png)

**总体框架。** 图片由作者提供

# 数据最小化与隐私之间的差距

考虑一个示例：最小化图像中的数据，并去除那些对模型性能没有贡献的像素。解决这个优化问题将给你一个最小化后的数据，看起来像这样。

![](../Images/2d8b4f73bd7cd506af38c5992946720b.png)

图片由作者提供

这个例子中的趋势很有意思。如你所见，数字“1”的图像保留了中央的垂直线，而数字“0”则保留了外部曲线。换句话说，虽然去除了50%的像素，但似乎没有丢失任何信息。通过应用一种非常简单的数据填充重建攻击，甚至可以证明这一点。

![](../Images/b28845083eae017b06734d96b8d7b27c.png)

图片由作者提供

尽管数据集减少了50%，但仍然可以使用整体统计信息重建图像。这强烈表明存在隐私风险，并表明**最小化数据集并不等同于提高隐私保护！**

# 那么我们能做什么呢？

虽然数据保护法规旨在限制数据收集，并期望提供隐私保护，但当前的数据最小化实现不足以提供强有力的隐私保障。然而，需要注意的是，这并不是说最小化与隐私不兼容；而是强调需要**将隐私融入目标的方式，而不是将其视为事后的考虑**。

我们在我们的[论文](https://arxiv.org/abs/2405.19471)中提供了更深入的实证研究，探讨了数据最小化与隐私保护之间的错位，并提出了潜在的解决方案。我们试图回答一个关键问题：*“当前各种法规中的数据最小化要求是否真正符合法律框架中的隐私预期？”* 我们的评估结果表明，不幸的是，答案是**否**。
