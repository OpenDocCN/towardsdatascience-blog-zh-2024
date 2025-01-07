# 使用小狗特征 web 应用探索 Shiny for Python

> 原文：[https://towardsdatascience.com/learn-shiny-for-python-with-a-puppy-traits-dashboard-cc65f05e88c4?source=collection_archive---------9-----------------------#2024-05-08](https://towardsdatascience.com/learn-shiny-for-python-with-a-puppy-traits-dashboard-cc65f05e88c4?source=collection_archive---------9-----------------------#2024-05-08)

## 使用 Shiny for Python 创建“谁是最棒的小狗”应用的逐步指南，从基础到样式

[](https://medium.com/@menghani.deepsha?source=post_page---byline--cc65f05e88c4--------------------------------)[![Deepsha Menghani](../Images/56a6ed8597c36e8c76d8a29a449325a4.png)](https://medium.com/@menghani.deepsha?source=post_page---byline--cc65f05e88c4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cc65f05e88c4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cc65f05e88c4--------------------------------) [Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--cc65f05e88c4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cc65f05e88c4--------------------------------) ·阅读时间 11 分钟·2024年5月8日

--

![](../Images/11371edbe2ffc6c7ea1072ff44f916d8.png)

图像来自作者

Shiny 在 R 生态系统中已经受到高度推崇超过十年，最近它的所有优点也被引入到 Python 中。[Shiny](https://shiny.posit.co/) 是一个 web 应用框架，可以用来创建互动式 web 应用，并在后台运行代码。

我多年来一直是 R Shiny 的忠实用户，所以当它也被引入 Python 时，我自然感到非常兴奋。在本文中，我将介绍我创建“谁是最棒的小狗”应用的步骤，从基础到样式。让我们开始吧！

# 代码和数据可用性

重现本文中所有内容的代码可以在我的 [GitHub 仓库](https://github.com/deepshamenghani/shinypython_meetup/tree/main?tab=readme-ov-file) 中找到。

**数据**：本文使用的是我生成的虚拟数据，包含伪造的小狗特征评分。这些虚拟数据可以在链接的 GitHub 页面中找到，并且灵感来源于我在 TidyTuesday 上找到的数据，感谢 [KKakey](https://github.com/kkakey/dog_traits_AKC/blob/main/README.md) 提供，这些数据最初来源于 [American Kennel Club](https://www.akc.org/) 的真实观察。

**数据生成过程**：为了生成数据，我结合了独特的小狗和特征组合……
