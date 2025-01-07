# 为什么计算机甚至使用二进制？

> 原文：[https://towardsdatascience.com/why-do-computers-even-use-binary-27e6d99f691d?source=collection_archive---------5-----------------------#2024-06-05](https://towardsdatascience.com/why-do-computers-even-use-binary-27e6d99f691d?source=collection_archive---------5-----------------------#2024-06-05)

## 一个新兴数据科学家的计算机硬件入门

[](https://medium.com/@cadenhamrick?source=post_page---byline--27e6d99f691d--------------------------------)[![W. Caden Hamrick](../Images/1674ac4b1c844f3aff37b8e3083764da.png)](https://medium.com/@cadenhamrick?source=post_page---byline--27e6d99f691d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--27e6d99f691d--------------------------------)[![数据科学前沿](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--27e6d99f691d--------------------------------) [W. Caden Hamrick](https://medium.com/@cadenhamrick?source=post_page---byline--27e6d99f691d--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--27e6d99f691d--------------------------------) ·阅读时间：8分钟·2024年6月5日

--

# 引言杂谈

数据科学家的主要工具是什么？一个公平（且显而易见）的答案是计算机，因为它处理数据的速度远远超过我们。试想一下，如果没有计算机，如何完成涉及数据的任务呢？你得依赖—*天哪*—铅笔和纸，在手工绘制大量表格、图表和计算的同时，你知道在计算机上这些任务不过需要几秒钟而已。可以说，今天的数据科学如果没有计算机，是根本不可能存在的。

既然计算机是进行数据科学的*核心*工具，你可能会认为这项工作需要理解计算机是如何工作的。毕竟，如果你不了解工具，做事情是很难做好。 然而，对于有志成为数据科学家的新人来说，往往容易忽视花时间建立与这一领域相关的基础知识。

![](../Images/bc4ff34fb6da896ec1c41e001e291ebc.png)

图片由[Malachi Brooks](https://unsplash.com/@mebrooks01?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/green-circuit-board-4qigzcWjVqc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

这也是可以理解的。数学、计算机科学和工程中的理论性话题，往往被 relegated（边缘化）为次要学习目标，因为它们不像像Python、Tensorflow或Amazon Web Services这样的应用类学习目标那样，能带来即时的回报（或者说就业机会）。那么，为什么…
