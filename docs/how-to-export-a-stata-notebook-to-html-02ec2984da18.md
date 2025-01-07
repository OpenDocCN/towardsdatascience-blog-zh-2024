# 如何将 Stata “笔记本” 导出为 HTML

> 原文：[https://towardsdatascience.com/how-to-export-a-stata-notebook-to-html-02ec2984da18?source=collection_archive---------11-----------------------#2024-10-17](https://towardsdatascience.com/how-to-export-a-stata-notebook-to-html-02ec2984da18?source=collection_archive---------11-----------------------#2024-10-17)

## 创建一个可以分享的 HTML 文档，其中包含你的代码、输出结果和图表

[](https://medium.com/@brbaracancino?source=post_page---byline--02ec2984da18--------------------------------)[![Bárbara A. Cancino](../Images/075cc42c1455bd56773b39129966b1d5.png)](https://medium.com/@brbaracancino?source=post_page---byline--02ec2984da18--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--02ec2984da18--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--02ec2984da18--------------------------------) [Bárbara A. Cancino](https://medium.com/@brbaracancino?source=post_page---byline--02ec2984da18--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--02ec2984da18--------------------------------) ·阅读时长8分钟·2024年10月17日

--

在数据项目中进行合作时，如何有效地分享工作至关重要。虽然在某些情况下，发送带注释的代码可能足够了，但有时这远远不够。

我在这里会诚实地说，我不喜欢使用 Stata。从编程的角度来看，我觉得它的语法非常不直观。但我主要的问题是，如果不通过分享数据和 do 文件给对方，让他们自己运行，最好的分享方式并不明确。

如果你们两个人都在使用这段代码，那没问题。然而，很多时候，另一个人只是想查看结果，并理解你是如何得到这些结果的。让他们自己运行代码既耗时又在最坏情况下可能带来后勤上的挑战。

尽管如此，我仍然需要使用 Stata，因为它在经济研究中被广泛使用。

但现在我发现了解决这个问题的**最佳方法**。那就是使用*webdoc*命令。

阅读完这篇文章后，你将能够将你的代码，例如下面这个：

```py
* Stata code
* Name: Your Name
* Date: October 2024

clear all
cd "your/directory/"

sysuse auto

* Regression n°1
regress price mpg

* Graphs
	twoway (scatter price mpg) (lfit price mpg)
```
