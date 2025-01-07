# Apple M2 Max GPU vs Nvidia V100（第二部分）：大模型与能效

> 原文：[https://towardsdatascience.com/apple-m2-max-gpu-vs-nvidia-v100-part-2-big-models-and-energy-efficiency-3377000472d2?source=collection_archive---------0-----------------------#2024-02-07](https://towardsdatascience.com/apple-m2-max-gpu-vs-nvidia-v100-part-2-big-models-and-energy-efficiency-3377000472d2?source=collection_archive---------0-----------------------#2024-02-07)

## 比较 Apple Silicon M2 Max GPU 在训练大规模 CNN 模型时的性能和能效，并与 Nvidia V100 进行对比。

[](https://fabrice-daniel.medium.com/?source=post_page---byline--3377000472d2--------------------------------)[![Fabrice Daniel](../Images/c13598211944934b5565fad97f152700.png)](https://fabrice-daniel.medium.com/?source=post_page---byline--3377000472d2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3377000472d2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3377000472d2--------------------------------) [Fabrice Daniel](https://fabrice-daniel.medium.com/?source=post_page---byline--3377000472d2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3377000472d2--------------------------------) ·阅读时间16分钟·2024年2月7日

--

在我的 [**上一篇文章**](https://medium.com/towards-data-science/apple-m2-max-gpu-vs-nvidia-v100-p100-and-t4-8b0d18d08894) 中，我将 M2 Max GPU 与 Nvidia V100、P100 和 T4 在 MLP、CNN 和 LSTM 训练中的表现进行了比较。结果显示，M2 Max 在小型模型训练中表现非常好，甚至超过了 Nvidia GPU。但是，正如文章中所述：

> […] 这些指标只能用于与本次测试中使用的神经网络类型和深度相似的情况。

因此，第二部分测试了更大的模型，专注于仅 CNN，并将 M2 Max 与之前测试过的最强 GPU：Nvidia V100 进行了比较。

另一个在本次测试中考虑的因素是**内存管理**。虽然 Nvidia GPU 在内存传输上浪费了大量时间，M2 Max GPU 由于可以直接访问统一内存，因此在训练模型之前无需任何延迟。由于正如上一篇文章中所示，这对在少量周期上训练的小型模型有很大影响，因此为了比较纯粹的训练时间，我们去除了这一影响，并测试了更大的模型。

为此，我们对模型进行十个周期的训练，但不是使用总的训练时间，而是从第二个周期到最后一个周期，捕捉并平均每步的训练时长。这样可以去除初始化和内存传输的开销……
