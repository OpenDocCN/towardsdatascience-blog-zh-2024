# Bigram 词云动画展示你的数据故事

> 原文：[https://towardsdatascience.com/bigram-word-cloud-animates-your-data-stories-753649d5cf4d?source=collection_archive---------12-----------------------#2024-05-08](https://towardsdatascience.com/bigram-word-cloud-animates-your-data-stories-753649d5cf4d?source=collection_archive---------12-----------------------#2024-05-08)

## 实践教程，讲解如何创建 *Animated* *Word* *Cloud* 来显示大ram频率的文本数据集并导出为 MP4 视频

[](https://petrkorab.medium.com/?source=post_page---byline--753649d5cf4d--------------------------------)[![Petr Korab](../Images/9f3afb4b8985584981220e30f18e3b69.png)](https://petrkorab.medium.com/?source=post_page---byline--753649d5cf4d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--753649d5cf4d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--753649d5cf4d--------------------------------) [Petr Korab](https://petrkorab.medium.com/?source=post_page---byline--753649d5cf4d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--753649d5cf4d--------------------------------) ·阅读时长 5 分钟·2024年5月8日

--

![](../Images/2141fca2ea2b580e87671f132285a724.png)

来源：[AnimatedWordCloud](https://pypi.org/project/AnimatedWordCloud) 库。图片来源：作者。

**动画词云**展示了 n-gram 频率（文本语料库中的单词及其后续单词）随时间变化的图像序列。它赋予在源文本中出现频率较高的单词更高的重要性，同时调整数据集以适应不同的文本数据集。原始的可视化方法采用经典的 [词云](https://www.datacamp.com/tutorial/wordcloud-python) 直观逻辑，并向图形中添加了时间维度。该方法旨在探索在多个时期收集的文本数据集（即***“*时间序列文本数据”***）。

[Michael Kane](https://github.com/thisIsMikeKane) 开发了用于动画化单词频率的核心框架，并且 [*AnimatedWordCloud*](https://pypi.org/project/AnimatedWordCloud/)（AWC）库将这一可视化方法付诸实践。新版本带来了重要的更新：

+   ***数据缩放***：现在能够更好地处理不同大小和单词频率的文本数据集

+   扩展 *n_gram* 参数（= 2）以生成 ***bigram 词云***

+   ***效率提升***（现在每个周期节省了220帧，改进了Y轴等）。
