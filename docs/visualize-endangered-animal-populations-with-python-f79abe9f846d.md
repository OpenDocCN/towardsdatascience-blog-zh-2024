# 使用Python可视化濒危动物种群

> 原文：[https://towardsdatascience.com/visualize-endangered-animal-populations-with-python-f79abe9f846d?source=collection_archive---------5-----------------------#2024-02-04](https://towardsdatascience.com/visualize-endangered-animal-populations-with-python-f79abe9f846d?source=collection_archive---------5-----------------------#2024-02-04)

## 快速成功的数据科学

## 数据新闻的最佳实践

[](https://medium.com/@lee_vaughan?source=post_page---byline--f79abe9f846d--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--f79abe9f846d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f79abe9f846d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f79abe9f846d--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--f79abe9f846d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f79abe9f846d--------------------------------) ·9分钟阅读·2024年2月4日

--

![](../Images/a96ae1300f1b443d96f99245b97d9a08.png)

原始图片由Mathias Appel提供（[CC0 1.0通用](https://commons.wikimedia.org/wiki/File:Red_Panda_(31350780004).jpg)）

*数据新闻*是一个使用数据分析、可视化和解读来讲述引人入胜且富有信息的故事的新闻领域。像数据科学家一样，数据记者利用数据和统计技术，发掘数据集中的趋势、模式和洞察。当做得好时，他们的工作为新闻报道提供了背景和深度。

2008年，*世界自然基金会日本*开展了一项[获奖](https://www.artofit.org/2020/11/24/populations-of-endangered-species-depicted-by-the-number-of-pixels/)印刷广告活动，使用[*图像像素化*](https://en.wikipedia.org/wiki/Pixelation)来描绘濒危物种的种群。每张濒危动物的图片都被逐渐降像素，直到其像素数量等于该物种现存个体的数量。图像像素化程度越高，画面越模糊，意味着剩余的动物越少。

这是一个很好的方式，能够引起人们对许多物种困境的关注，[其他](https://www.boredpanda.com/endagered-animals-pixels-extinction/?utm_source=bing&utm_medium=organic&utm_campaign=organic)人也复制了这一广告活动。在这个*快速成功的数据科学*项目中，我们将编写代码，让你使用[*Pillow*](https://pypi.org/project/pillow/)（*Python Imaging Library (PIL)*的分支）重现这一获奖技术。

我们还将完善这个过程，展示动物的*原始*图片和*修改后*的图片。当（遗憾地）动物在修改后的图片中已经无法辨认时，这种设计非常有用。下面是一个…
