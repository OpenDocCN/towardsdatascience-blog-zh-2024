# 线性编程简介 — 第二部分

> 原文：[https://towardsdatascience.com/introduction-to-linear-programming-part-ii-4fa9521ac3a7?source=collection_archive---------2-----------------------#2024-07-02](https://towardsdatascience.com/introduction-to-linear-programming-part-ii-4fa9521ac3a7?source=collection_archive---------2-----------------------#2024-07-02)

## 使用R进行生产优化

[](https://medium.com/@rlohne?source=post_page---byline--4fa9521ac3a7--------------------------------)[![Robert Lohne](../Images/2ca257a9ad0280b4c9ddd44735565c20.png)](https://medium.com/@rlohne?source=post_page---byline--4fa9521ac3a7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4fa9521ac3a7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4fa9521ac3a7--------------------------------) [Robert Lohne](https://medium.com/@rlohne?source=post_page---byline--4fa9521ac3a7--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4fa9521ac3a7--------------------------------) ·14分钟阅读·2024年7月2日

--

去年，我的一位朋友找到了我，他在一家小型的家族钢铁和金属公司工作。他想知道是否可以创建某种工具，帮助他解决切割钢梁时最小化浪费的问题。听起来像是线性编程的问题！

当我开始的时候，关于如何在R中使用线性编程的初学者文章并不多，而且很难找到对没有太多数学基础的人有意义的内容。到2023年初，线性编程与R的结合也是ChatGPT不擅长的领域，所以我常常希望有一本指南。

这个系列是我尝试制作这样的指南。希望它对某些人有所帮助。

这是**第二部分**，如果你正在寻找R中线性编程的介绍，请查看[**第一部分**](https://medium.com/towards-data-science/optimize-production-with-r-part-i-25497d29e06e)**。**

# 可是我不懂数学呀..!?

如果你读过线性编程或线性优化背后的理论，你会发现有大量的数学内容。如果你没有数学背景，这可能会让人却步（我没有）。对我来说，这主要是因为我从未上过足够的数学课程来理解许多符号。最初，这使得理解与线性编程相关的教程比应该的更加困难。然而，你不需要理解理论背后的数学，就能应用本文中代码的原则。
