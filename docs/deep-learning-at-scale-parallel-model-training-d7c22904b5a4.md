# 大规模深度学习：并行模型训练

> 原文：[https://towardsdatascience.com/deep-learning-at-scale-parallel-model-training-d7c22904b5a4?source=collection_archive---------9-----------------------#2024-04-05](https://towardsdatascience.com/deep-learning-at-scale-parallel-model-training-d7c22904b5a4?source=collection_archive---------9-----------------------#2024-04-05)

## 概念与Pytorch Lightning示例

[](https://medium.com/@caroline.arnold_63207?source=post_page---byline--d7c22904b5a4--------------------------------)[![Caroline Arnold](../Images/fb13ba36e302d8161b67c4888d0601e4.png)](https://medium.com/@caroline.arnold_63207?source=post_page---byline--d7c22904b5a4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d7c22904b5a4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d7c22904b5a4--------------------------------) [Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--d7c22904b5a4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d7c22904b5a4--------------------------------) ·阅读时间7分钟·2024年4月5日

--

![](../Images/9889f32b020f8d3fbe4ebe73db1d32fe.png)

图片由作者使用Midjourney创建。

在大量GPU上进行并行训练是深度学习的前沿技术。开源图像生成算法Stable Diffusion[在256个GPU集群上进行了训练](https://the-decoder.com/training-cost-for-stable-diffusion-was-just-600000-and-that-is-a-good-sign-for-ai-progress/)。Meta的[AI研究超级集群](https://www.siliconrepublic.com/machines/meta-is-using-two-nvidia-gpu-clusters-to-train-llama-3)包含超过24,000个NVIDIA H100 GPU，用于训练像Llama 3这样的模型。

通过使用多个GPU，机器学习专家减少了训练过程的*墙时*。训练Stable Diffusion[花费了150,000个GPU小时](https://the-decoder.com/training-cost-for-stable-diffusion-was-just-600000-and-that-is-a-good-sign-for-ai-progress/)，相当于超过17年。并行训练将其减少到25天。

存在两种类型的[并行深度学习](https://arxiv.org/abs/1802.09941)：

+   *数据并行性*，将大型数据集分布到多个GPU上。

+   *模型并行性*，将过大无法在单个GPU上运行的深度学习模型分布到多个设备上。

我们在这里将重点讨论数据并行性，因为模型并行性[仅在非常大的模型（超过500M参数）时才相关](https://lightning.ai/docs/pytorch/stable/advanced/model_parallel/fsdp.html)。

除了减少壁钟时间外，进行并行训练还有一个经济学上的理由：云计算提供商如[AWS提供单台机器](https://docs.aws.amazon.com/batch/latest/userguide/gpu-jobs.html)，配备最多16个GPU。并行训练可以利用所有可用的GPU，从而让你用更少的钱获得更多的价值。

## 并行计算

[并行性是高性能计算中的主流范式](https://en.wikipedia.org/wiki/Parallel_computing)。程序员识别出可以独立执行的任务，并将它们分配到大量设备上。程序的串行部分负责分发任务并收集结果。

并行计算可以减少计算时间。一程序如果在四个设备上并行运行，相比于在单个设备上运行，理想情况下可以快四倍。实际上，通信开销限制了这种扩展。

*打个比方，想象一群画家在画一面墙。通信开销出现在工头告诉每个人使用什么颜色、涂哪里时。涂画的过程可以并行进行，只有最后的收尾工作是串行的任务。*

## 训练深度学习模型

训练深度学习算法需要数据和一个优化程序，如[随机梯度下降](/stochastic-gradient-descent-math-and-python-code-35b5e66d6f79)。

训练数据被打乱并分成固定大小的批次。每批次，训练程序都会计算实际标签和预测标签之间的损失。模型参数会根据损失相对于模型参数的梯度进行调整。

![](../Images/9f2d4393572ca20cfa46824bd8c9d985.png)

随机梯度下降。图片由作者制作。

当模型已经遍历过所有训练数据批次一次时，*epoch*就完成了。

## 分布式数据并行

下图描述了使用3个GPU进行深度学习模型训练的数据并行。未训练的模型副本被复制到每个GPU上。数据集被分成3个部分，每个部分在不同的GPU上并行处理。

![](../Images/2f7994943c709cc560310884b328f8f5.png)

分布式数据，相同的模型。图片由作者制作。

请注意，每个模型副本看到的训练数据子集是不同的。完成一个epoch后，每个模型副本中的权重和偏差都会有所不同。

通过在所有模型实例之间平均权重来协调这三个模型副本。更新后的模型会广播到所有3个GPU，训练继续进行到下一个epoch。

分布式训练将有效批次大小改变为`设备数量 * 原始批次大小`。这可能会影响训练、收敛性和模型表现。

## 在Pytorch Lightning中的实现

数据并行需要以下几个要素：

+   一个可以处理分布式训练的数据加载器

+   一个*all-reduce*函数，用于协调模型副本

+   一个用于不同并行部分之间相互通信的框架

在 PyTorch Lightning 中，[Lightning Trainer](https://lightning.ai/docs/pytorch/stable/common/trainer.html) 处理整个训练过程。通过指定以下关键字参数，它可以*开箱即用*地执行分布式数据并行训练：

+   节点数量——你想要使用的云计算集群中的机器数量

+   每个节点的设备数量（GPU）*每个节点*

+   训练策略，这里是分布式数据并行。对于分布式训练，有不同的[策略](https://lightning.ai/docs/pytorch/stable/api_references.html#strategies)可供选择，它们负责调整训练过程。

对于在 2 个节点，每个节点有 2 个 GPU 的分布式数据并行（*ddp*）训练，请使用：

```py
import lightning as L

trainer = L.Trainer(nodes=2, devices=2, strategy='ddp')
trainer.fit(model, dataloader)
```

默认情况下，Lightning Trainer 会在计算环境中使用所有可用的 GPU。这意味着，如果你只想在多 GPU 机器上使用单个 GPU，则需要明确指定。

## 实验：使用 Country211 数据集进行并行训练

[Country211 数据集](https://github.com/openai/CLIP/blob/main/data/country211.md)包含来自 211 个国家的地理标签图像。在我的实验中，我使用了一个预训练的视觉 Transformer，并在 Country211 数据集上微调此模型。代码可在[GitHub](https://github.com/crlna16/pretrained-vision-transformer)上获取。

[](/how-to-fine-tune-a-pretrained-vision-transformer-on-satellite-data-d0ddd8359596?source=post_page-----d7c22904b5a4--------------------------------) [## 如何在卫星数据上微调预训练的视觉 Transformer

### PyTorch Lightning 中的逐步教程

towardsdatascience.com](/how-to-fine-tune-a-pretrained-vision-transformer-on-satellite-data-d0ddd8359596?source=post_page-----d7c22904b5a4--------------------------------)

共有 31,650 张训练图像。我在整个过程中使用了批量大小为 256，并且[实验了 GPU 数量](https://lightning.ai/docs/fabric/stable/guide/multi_node/cloud.html)，从单个节点上的单个 GPU 到分布在四个节点上的八个 GPU。我应用了提前停止条件，如果验证准确率在五个轮次内没有改善，则停止训练。

训练的墙时间，即训练模型所需的总时间，随着使用的 GPU 数量增加而减少。在单个 GPU 上，墙时间为 45 分钟，而在使用 8 个 GPU 时，墙时间降至 11 分钟。

理想的扩展性应为 45 分钟 / 8 个 GPU = 5.6 分钟，但在更多 GPU 上训练时，由于通信开销和训练轮次增多，我们无法达到这一最优值。

![](../Images/e288058bf56d71bc57ef28cd0c8acbe3.png)

不同数量设备的墙时间。图像由作者创建。

为了比较泛化能力，我计算了所有训练模型的测试集准确率。在单一设备上的单个GPU训练时，获得了最佳的测试集准确率，但即便如此，模型在泛化方面仍然存在问题：测试集准确率仅为15%。

面板展示了不同数量GPU下准确率的下降，相对于最佳结果。使用的设备越多，应用到测试集时模型的准确性就越差。

![](../Images/28b987cf516ae749486725807cc19f5d.png)

测试集准确率。图像由作者创建。

准确率的变化可能是由于有效批次大小的变化——请注意，依据训练策略，我们不会得到完全相同的模型。

在本次实验中，泛化准确性与速度之间存在权衡。使用8个GPU训练比单设备训练快了四倍，但准确性比单设备训练低了11%。

## 平行训练概述

在训练日志文件中，你将看到像这样的行：

```py
Initializing distributed: GLOBAL_RANK: 2, MEMBER: 3/4
Initializing distributed: GLOBAL_RANK: 0, MEMBER: 1/4
Initializing distributed: GLOBAL_RANK: 3, MEMBER: 4/4
Initializing distributed: GLOBAL_RANK: 1, MEMBER: 2/4
----------------------------------------------------------------------------------------------------
distributed_backend=nccl
All distributed processes registered. Starting with 4 processes
----------------------------------------------------------------------------------------------------
```

```py
LOCAL_RANK: 0 - CUDA_VISIBLE_DEVICES: [0,1]
LOCAL_RANK: 1 - CUDA_VISIBLE_DEVICES: [0,1]
LOCAL_RANK: 0 - CUDA_VISIBLE_DEVICES: [0,1]
LOCAL_RANK: 1 - CUDA_VISIBLE_DEVICES: [0,1]
```

并行训练过程必须能够互相通信。这里的通信后端是NVIDIA集体通信库(*nccl*)，根据平台会自动初始化。

我们请求了两个节点，每个节点有两个GPU，共四个并行进程。它们通过*全局排名*（0–3）进行枚举。在每个节点上，*本地排名*标识了使用的GPU（0或1）。

当模型很大时，[*all-reduce* 操作](https://en.wikipedia.org/wiki/Collective_operation#All-Reduce)可能会花费很长时间。计算集群通过高效的网络连接GPU，但发送整个模型副本会加重通信负担。

## 模型并行

[Lightning 提供了模型并行的策略](https://lightning.ai/docs/pytorch/stable/advanced/model_parallel.html)。请注意，这仅应在具有数十亿参数的非常大模型中探索，其中模型参数和梯度超过了GPU内存。

## 总结

并行训练可以加速训练过程，帮助你充分利用计算资源。

我们区分了数据并行，其中模型的副本在训练过程中看到数据的子集，以及模型并行，其中模型本身分布在多个设备上。

使用PyTorch Lightning，数据并行训练就像指定*分布式数据并行*策略以及每个节点和设备（GPU）的数量一样简单。

数据并行训练减少了壁钟时间并改变了有效批次大小。使用不同数量设备训练的模型可能表现不同，因此建议进行仔细评估。

模型并行训练应该仅在非常大的模型（超过5亿参数）中考虑。

重现实验的代码可以在我的GitHub上找到：

[](https://github.com/crlna16/pretrained-vision-transformer?source=post_page-----d7c22904b5a4--------------------------------) [## GitHub - crlna16/pretrained-vision-transformer: 使用 PyTorch 的预训练 Vision Transformer…

### 使用 PyTorch Lightning 的预训练 Vision Transformer - crlna16/pretrained-vision-transformer

[github.com](https://github.com/crlna16/pretrained-vision-transformer?source=post_page-----d7c22904b5a4--------------------------------)

## 参考文献

+   Tal Ben-Nun *等*，“揭秘并行和分布式深度学习：深入的并发分析”，2018，[https://arxiv.org/abs/1802.09941](https://arxiv.org/abs/1802.09941)

+   OpenAI Country211 数据集：[source](https://github.com/openai/CLIP/blob/main/data/country211.md)（[YFCC100M](https://dx.doi.org/10.1145/2812802) 的子集）和 [license](https://multimediacommons.wordpress.com/yfcc100m-core-dataset/#yfcc100m)

+   Vision Transformer 模型：Dosovitskiy 等，*一张图片值 16x16 个词：用于大规模图像识别的变换器*，ICLR，2021。 [Paper](https://arxiv.org/pdf/2010.11929.pdf) 和代码

+   用于数据并行训练的 PyTorch Lightning 项目：[docs](https://lightning.ai/docs/pytorch/stable/accelerators/gpu_intermediate.html)

+   Will Falcon 讲解分布式训练 [(TDS, 2019)](/9-tips-for-training-lightning-fast-neural-networks-in-pytorch-8e63a502f565)
