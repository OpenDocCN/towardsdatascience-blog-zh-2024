# 欺诈检测中的机器学习：入门指南

> 原文：[`towardsdatascience.com/machine-learning-in-fraud-detection-a-primer-8005b8c88cde?source=collection_archive---------4-----------------------#2024-11-12`](https://towardsdatascience.com/machine-learning-in-fraud-detection-a-primer-8005b8c88cde?source=collection_archive---------4-----------------------#2024-11-12)

## 在不断发展的对抗性环境中，平衡自动化、准确性和客户体验

[](https://medium.com/@samuel.flender?source=post_page---byline--8005b8c88cde--------------------------------)![Samuel Flender](https://medium.com/@samuel.flender?source=post_page---byline--8005b8c88cde--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8005b8c88cde--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8005b8c88cde--------------------------------) [Samuel Flender](https://medium.com/@samuel.flender?source=post_page---byline--8005b8c88cde--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8005b8c88cde--------------------------------) ·阅读时间 8 分钟·2024 年 11 月 12 日

--

![](img/ef3f10edbebe3eded35d2dad5b98bddd.png)

（图片由作者使用 ChatGPT 生成）

欺诈检测是现代电子商务的基石，但它也是机器学习中最少被宣传的领域之一。这是有充分理由的：这是一个对抗性领域，欺诈者不断发明新的方法来绕过现有模型，而模型开发者则不断发明新的方法来抓住他们。

欺诈检测系统的目标是阻止欺诈交易，例如那些由伪造账户使用盗用的信用卡进行的交易，同时又不影响真实客户的购物体验。假阳性（真实交易被错误拦截）会导致客户体验不佳并引发流失，而假阴性（欺诈交易被误放行）则会导致货币损失，也就是由于实际信用卡持卡人发起的拒付而产生的“坏账”。

假设一个现代电子商务提供商每天可能处理数千万个订单，而欺诈率处于千分之一的水平，你就能理解为什么这是一个具有挑战性的领域。这是典型的“在干草堆里找针”问题，而这些干草堆巨大无比，…
