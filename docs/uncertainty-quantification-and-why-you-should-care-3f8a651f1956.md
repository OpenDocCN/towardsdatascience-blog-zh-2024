# 不确定性量化及其重要性

> 原文：[https://towardsdatascience.com/uncertainty-quantification-and-why-you-should-care-3f8a651f1956?source=collection_archive---------1-----------------------#2024-04-24](https://towardsdatascience.com/uncertainty-quantification-and-why-you-should-care-3f8a651f1956?source=collection_archive---------1-----------------------#2024-04-24)

## 如何通过三行代码提升你的机器学习模型

[](https://medium.com/@jodancker?source=post_page---byline--3f8a651f1956--------------------------------)[![Jonte Dancker](../Images/29e37a1a1cabc15cfb90a860b2931f03.png)](https://medium.com/@jodancker?source=post_page---byline--3f8a651f1956--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3f8a651f1956--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3f8a651f1956--------------------------------) [Jonte Dancker](https://medium.com/@jodancker?source=post_page---byline--3f8a651f1956--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3f8a651f1956--------------------------------) ·8分钟阅读·2024年4月24日

--

![](../Images/9710ae12cfd78c9a979b1fcbdd323520.png)

将点预测转化为预测区域，以量化模型的不确定性，给我们提供更多信息（图片来自作者）。

预测模型经过训练，旨在做出准确预测并给出点预测结果。

假设我们想买一套房子。在此之前，我们希望验证广告中提到的40万欧元价格是否合理。为此，我们使用一个模型，根据房间数、房屋大小和位置来预测该房子价值500,232.12欧元。

我们应该买这套房子吗？看起来像是个不错的交易，不是吗？但如果模型预测的价格是340,021.34欧元，我们的决策会不会不同？可能会吧？

那么，我们在做出购买这套房子的决策时应该信任模型吗？这套房子价值500,232.12欧元或340,021.34欧元的概率有多大？

由于概率接近零，我们应该将预测视为对真实结果的最佳猜测。此外，当我们根据模型做出决策时，也存在风险。

在购房的例子中，我们可能支付过多，导致财务损失。如果一辆自动驾驶汽车没有将过马路的人识别为障碍物，结果可能是致命的。
