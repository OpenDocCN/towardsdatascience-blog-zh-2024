# 使用Python预测人口下降

> 原文：[https://towardsdatascience.com/predicting-population-decline-with-python-fffad6afc58c?source=collection_archive---------10-----------------------#2024-01-30](https://towardsdatascience.com/predicting-population-decline-with-python-fffad6afc58c?source=collection_archive---------10-----------------------#2024-01-30)

## 快速成功的数据科学

## 为什么灭霸应该打两次响指

[](https://medium.com/@lee_vaughan?source=post_page---byline--fffad6afc58c--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--fffad6afc58c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fffad6afc58c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fffad6afc58c--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--fffad6afc58c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fffad6afc58c--------------------------------) ·12分钟阅读·2024年1月30日

--

![](../Images/e0118baec4e82b5301647d2606f5a6f5.png)

Dall-E2绘制的地球解体图

在电影[*复仇者联盟：无限战争*](https://en.wikipedia.org/wiki/Avengers:_Infinity_War)中，超级反派灭霸打响指，*无限手套*随机消灭宇宙中一半的生命。他这么做是为了让剩下的一半繁荣，但他只是做对了一半。如果他想解决“问题”，他应该再打一次响指，确保新的人口不会随着时间恢复。

事情是这样的。灭霸将人类人口从大约*80亿*减少到*40亿*，大约是1974年的水平。但是，从40亿增长到80亿仅用了*49年*，也就是到2023年。这在宇宙时间尺度上，简直就是一个指尖一弹。

![](../Images/2dac5c6470e9bddb878046fe5f49c71c.png)

自公元前10,000年以来的世界人口（[维基百科](https://en.wikipedia.org/wiki/World_population) (CC BY-SA 4.0 Deed)）

灭霸需要第二次打响指，才能永久将[*总生育率*](https://en.wikipedia.org/wiki/Total_fertility_rate) *(TFR)* 设定为*生育替代率* (*FRR*)。

人口的TFR是一个女性一生中平均生育的孩子数量。FRR是每个女性需要平均生育的孩子数，以替代当前的世代并维持稳定的人口。
