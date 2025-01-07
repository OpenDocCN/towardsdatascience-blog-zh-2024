# 使用 Depth Anything V2 进行单目深度估计

> 原文：[https://towardsdatascience.com/monocular-depth-estimation-with-depth-anything-v2-54b6775abc9f?source=collection_archive---------4-----------------------#2024-07-24](https://towardsdatascience.com/monocular-depth-estimation-with-depth-anything-v2-54b6775abc9f?source=collection_archive---------4-----------------------#2024-07-24)

## 神经网络是如何从二维图像中学习估计深度的？

[](https://medium.com/@neural.avb?source=post_page---byline--54b6775abc9f--------------------------------)[![Avishek Biswas](../Images/6feb591069f354aa096f6108f1a70ea7.png)](https://medium.com/@neural.avb?source=post_page---byline--54b6775abc9f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--54b6775abc9f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--54b6775abc9f--------------------------------) [Avishek Biswas](https://medium.com/@neural.avb?source=post_page---byline--54b6775abc9f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--54b6775abc9f--------------------------------) ·阅读时间：10分钟·2024年7月24日

--

## 什么是单目深度估计？

![](../Images/53c47ae75f37e0bef19e2c77b3169255.png)

Depth Anything V2 算法（作者插图）

单目深度估计（MDE）是训练神经网络从单幅图像中提取深度信息的任务。这是一个激动人心且充满挑战的机器学习与计算机视觉领域，因为预测深度图要求神经网络仅凭二维图像来形成三维的理解。

在本文中，我们将讨论一个新的模型叫做*Depth Anything V2*及其前身*Depth Anything V1*。Depth Anything V2 在深度估计领域超越了几乎所有其他模型，在处理复杂图像时展现了令人印象深刻的结果。

![](../Images/26476d8feb1529cf50ab27a0b30729f2.png)

Depth Anything V2 演示（来源：作者从 Depth Anything V2 [演示页面](https://huggingface.co/spaces/depth-anything/Depth-Anything-V2)录制的屏幕录像）

**本文基于我制作的同主题视频。** [**这里是视频链接，适合喜欢视觉媒介的学习者。**](https://youtu.be/sz30TDttIBA) 对于喜欢阅读的朋友，继续往下看！

## 为什么我们要关心 MDE 模型？

良好的 MDE 模型有许多实际应用，如[帮助机器人](https://natesimon.github.io/mononav/)导航和避障、无人机以及自动驾驶汽车。它们还可以用于视频和图像编辑、背景替换、物体移除和创建三维效果。此外，它们在[增强现实（AR）和虚拟现实（VR）头戴设备中也非常有用，可以为用户创造交互式三维空间](https://arxiv.org/abs/2202.08010)。

## 进行单目深度估计（MDE）有两种主要方法（本文仅介绍其中一种）

训练MDE模型已经出现了两种主要的方法：一种是判别方法，网络试图将深度作为监督学习目标进行预测；另一种是生成方法，如条件扩散，其中深度预测是一个迭代的图像生成任务。**Depth Anything属于第一类判别方法，今天我们将讨论的是这一部分。** 欢迎来到Neural Breakdown，让我们深入探讨深度估计！

# **传统数据集与MiDAS论文**

为了全面理解Depth Anything，让我们首先回顾一下2019年的MiDAS论文，它为Depth Anything算法提供了前身。

![](../Images/c35232fe74fc443615630d9de12c652e.png)

来源：[MIDAS](https://arxiv.org/abs/1907.01341)论文的截图（许可证：免费）

MiDAS通过结合不同的数据集来训练MDE模型，这些数据集包含标注的深度信息。例如，[KITTI](https://www.cvlibs.net/datasets/kitti/)数据集用于自动驾驶，提供户外图像，而[NYU-Depth V2](https://cs.nyu.edu/~fergus/datasets/nyu_depth_v2.html)数据集则提供室内场景。了解这些数据集是如何收集的非常重要，因为像Depth Anything和Depth Anything V2这样的新模型解决了数据收集过程中固有的多个问题。

## 如何收集现实世界中的深度数据集

这些数据集通常是通过立体摄像头收集的，两个或更多的摄像头以固定距离放置，从略微不同的视角同时拍摄图像，从而实现深度信息的提取。NYU-Depth V2数据集使用RGB-D摄像头，它不仅捕捉像素颜色，还记录深度值。一些数据集利用激光雷达（LiDAR），通过投射激光束来捕捉场景的三维信息。

**然而，这些方法存在一些问题。** 由于获取这些数据集的高运营成本，标注数据的数量有限。此外，标注可能会有噪音且分辨率较低。立体摄像头在各种光照条件下表现不佳，无法可靠地识别透明或高度反射的表面。激光雷达昂贵，并且激光雷达与RGB-D摄像头的范围有限，生成的深度图分辨率低且稀疏。

## 我们能否使用未标注图像来学习深度估计？

利用未标注的图像来训练深度估计模型将是非常有益的，因为网上有大量这样的图像。2023年原始Depth Anything论文提出的主要创新是将这些未标注数据集整合进训练流程。接下来的部分，我们将探讨这一点是如何实现的。

# **Depth Anything架构**

原始的[Depth Anything (V1)模型，发布于2023年](https://depth-anything.github.io/)采用了三步训练过程。在深入每个部分之前，让我们先对该算法进行一个高层次的概览。

![](../Images/b59c38e97f10cfa343bac2b3e428fc20.png)

Depth Anything V1算法（作者插图）

## 步骤1：教师模型训练

首先，名为TEACHER的神经网络模型被训练用于监督式深度估计，使用了五个不同的公开数据集。

***从深度到视差空间的转换***

TEACHER模型初始化时使用了[预训练的Dino-V2编码器](https://arxiv.org/abs/2304.07193)，然后在合并后的标注数据集上进行训练。训练多个数据集时的一个主要挑战是绝对深度的差异。为了解决这个问题，深度值被转化为视差空间**（d = 1 / t）**，并在每个深度图中进行归一化处理——离最近像素为1，离最远像素为0。通过这种方式，所有数据集共享相同的输出空间，从而使得模型可以预测视差。

![](../Images/feb773be7a55da3e9a247bc018362171.png)

不同的深度估计数据集提供的深度值具有不同的尺度。我们需要将它们对齐以使输出空间相同。视差可以让我们将所有深度值归一化到0和1之间（作者插图）

这两个损失函数用于训练这些模型：**尺度位移不变损失和梯度匹配损失**，这两个损失函数也在2019年的MiDAS论文中得到了应用。

1.  ***尺度位移不变损失***

使用简单的均方误差损失来比较预测图像与真实图像之间的差异时存在一个问题。假设图像中三个像素的真实深度值分别是*1、0.5 和 0.1*，而我们的网络预测的是*0.9、0.6 和 0.3*。尽管预测值不完全相同，但预测深度与真实深度之间的关系是相似的，只有一个乘法和加法因子的差异。**我们不希望这种尺度和位移影响我们的损失函数——在应用均方误差损失之前，我们需要对两个图像进行对齐。**

![](../Images/84703dc474e959b2a4de1549e58249df.png)

尺度位移不变损失（作者插图）

MiDaS论文建议将真实深度和预测深度进行归一化，以确保零平移和单位尺度。计算中位数和标准差，然后相应地调整深度图的尺度和位移。对齐后，应用均方误差损失。

![](../Images/8a6dd80e0cf700348801e9c879cb35a4.png)![](../Images/84a74de5ee9b690b8f57509e828181af.png)

SSI损失（来源：[MiDAS论文](https://arxiv.org/abs/1907.01341)）（许可证：免费）

***2. 梯度匹配损失***

![](../Images/5c5a5575969a4594c1abe8a4a352f364.png)

如果没有梯度匹配损失，深度图可能会变得过于模糊，失去锐利感（作者插图）

仅使用SSI损失可能会导致平滑的深度图，无法捕捉相邻像素之间的明显区别。梯度匹配损失通过将预测深度图的梯度与真实深度图的梯度对齐，帮助保留这些细节。

首先，我们计算预测的和地面真实深度图在x轴和y轴上的梯度，然后在梯度层面应用损失。MiDaS还使用了一个具有四个尺度级别的多尺度梯度匹配损失。预测和地面真实深度图被下采样四次，并在每个分辨率上应用损失。

![](../Images/b0ff56b585ba50ea0d90f590e51805e9.png)

梯度匹配损失。该损失应用于多个下采样的深度图（未显示）。(插图作者提供)

最终损失是尺度不变损失和多尺度梯度匹配损失的加权和。虽然SSI损失鼓励模型学习一般的相对深度关系，但梯度匹配损失有助于保持场景中的锐利边缘和精细信息。

![](../Images/e2e92b97e1ef6e0478d89fd6f1aa09c7.png)

在MIDAS和Depth Anything V1中训练深度估计模型所使用的损失函数（插图作者提供）

## 步骤2 — 伪标签无标签数据集

使用我们训练好的教师模型，我们现在可以为数百万张无标签图像添加注释，创建一个庞大的伪深度标签数据集。这些标签被称为伪标签，因为它们是AI生成的，可能并不代表实际的地面真实深度。我们现在拥有大量（伪）标签图像来训练一个新的网络。

![](../Images/caede365e9a8d6d752dbe1cad7b4c9db.png)

伪标签图像（请注意，这张屏幕截图实际上来自Depth Anything V2论文，而非V1）来源：[Depth Anything V2 Paper](https://arxiv.org/abs/2406.09414)（许可证：免费）

## 步骤3 — 训练学生网络

![](../Images/b59c38e97f10cfa343bac2b3e428fc20.png)

回顾Depth Anything V1算法。我们现在处于步骤3。（插图由作者制作）

我们将在标签数据集和伪标签数据集的结合上训练一个新的神经网络（学生网络）。**然而，仅仅在教师网络提供的注释上训练网络并不会使模型超越基础教师模型的能力。**为了使学生网络更强大，采用了两种策略：使用图像增强进行重度扰动，并引入辅助语义保持损失。

***重度扰动***

使用的一个有趣的扰动是Cut Mix操作。它涉及通过二进制掩膜将一对随机的无标签图像结合起来，用图像B替换图像A中的一个矩形区域。最终的损失是来自两个地面真实深度图的两部分的合成SSI和梯度匹配损失。这些空间失真还与颜色失真结合，帮助学生网络应对开放世界图像的多样性。

![](../Images/d248f0a505eebf438fa29e5c540cde53.png)

Cut Mix操作（插图作者提供）

***辅助语义保持损失***

网络还通过一个辅助任务进行训练，称为语义辅助感知。使用像Dino-V2这样的强大预训练计算机视觉模型，它已经通过自监督方式在数百万张图像上进行了训练。给定一张图像，我们的目标是减少由新学生模型生成的嵌入与预训练Dino-V2编码器之间的余弦距离。这使得我们的学生模型能够捕捉到更大、更通用的Dino-V2模型的一些语义感知能力，并利用这些能力来预测深度图。

![](../Images/3240ebbb1b601d8cf1aec0cd5932a074.png)

语义辅助感知（作者插图）

通过结合空间畸变、语义辅助感知以及标注和未标注数据集的力量，学生网络能够更好地泛化，并在深度估计上超过原始的教师网络！以下是来自Depth Anything V1模型的一些令人难以置信的结果！

# **Depth Anything V2**

**尽管Depth Anything V1的结果令人印象深刻，但它在处理透明物体和捕捉细粒度细节方面仍存在困难。** **Depth Anything V2的作者认为，模型性能的最大瓶颈不是架构本身，而是数据的质量。** 大多数使用传感器捕获的标注数据集可能会有噪声，忽略细粒度细节，生成低分辨率的深度图，并且在光照条件以及反射/透明物体的处理上存在困难。

![](../Images/94bb158d8593a17797ed2214b00abd67.png)

真实世界传感器数据集的问题（作者插图）

Depth Anything V2舍弃了来自真实世界传感器（如立体相机、激光雷达和RGB-D相机）的标注数据集，而仅使用合成数据集。合成数据集是通过图形引擎生成的，而非通过设备捕获的。例如，使用Unity游戏引擎创建渲染图像和深度图的[虚拟KITTI数据集](https://www.cvlibs.net/datasets/kitti/eval_depth.php?benchmark=depth_prediction)，用于自动驾驶。还有一些室内数据集，如IRS和Hyper-sim。Depth Anything V2使用了五个包含接近595K张逼真图像的合成数据集。

## ***合成数据集 vs 真实世界传感器数据集***

合成图像确实有其优缺点。它们非常准确，具有高分辨率输出，能够捕捉最细微的细节，并且透明和反射表面的深度可以轻松获得。**合成数据集直接获取所有所需的3D信息，因为图形引擎本身创建了场景。**

从缺点方面来看，这些图像可能无法完全捕捉我们在现实场景中会遇到的图像。这些数据集的场景覆盖面也不够多样，且仅为现实世界图像的一个较小子集。Depth Anything 2结合了合成图像和数百万未标注图像的优势，训练出了一个MDE模型，其性能超越了我们迄今为止见过的大多数模型。

![](../Images/217dc74172e0dee404d98a287ddc4508.png)

合成或计算机生成数据集的优缺点（图示：作者提供）

和V1类似，V2中的教师模型首先在标注数据集上进行训练。然而，在V2中，它仅在合成数据集上进行训练。在第二步，教师模型为所有未标注图像分配伪深度标签。最后，在第三步，学生模型仅在伪标注图像上进行训练——不使用任何真实标注数据集，也不使用合成数据集。在这一阶段，由于之前提到的分布偏移，合成数据集没有被使用。学生网络在由教师模型标注的真实世界图像上进行训练。就像V1中一样，辅助的语义保留损失与尺度-平移不变性损失和梯度匹配损失一同使用。

![](../Images/53c47ae75f37e0bef19e2c77b3169255.png)

Depth Anything V2架构（图示：作者提供）

# 视频链接，形象地解释这些概念

这里有一个视频，逐步解释了本视频中讨论的所有概念。

你也可以以视频形式学习本文中涉及的主题

# Depth Anything V1 vs Depth Anything V2

原版的Depth Anything强调了在MDE训练流程中使用未标注图像的重要性。它引入了知识蒸馏流程，包括教师模型训练、伪标签化未标注图像，然后在标注和未标注图像的结合上训练学生网络。强空间和颜色扭曲的使用，以及语义辅助感知损失，有助于创建更通用和鲁棒的嵌入。这导致了为复杂场景生成高效且高质量的深度图。然而，Depth Anything V1仍然在处理反射表面和细节时存在问题，因为来自现实传感器的深度标签噪声大且分辨率低。

Depth Anything V2通过忽略真实世界传感器数据集，专门使用通过图形引擎生成的合成图像来训练教师网络，从而提升了性能。然后，教师网络为数百万未标注图像进行标注，学生网络仅在这些伪标注数据集上进行训练，并使用真实世界图像。通过这些技术，Depth Anything V2现在能够预测精细级别的深度图，并更有效地处理透明和反射表面。

# **相关链接**

MiDAS: [https://arxiv.org/abs/1907.01341](https://arxiv.org/abs/1907.01341)

Depth Anything: [https://depth-anything.github.io/](https://depth-anything.github.io/)

Depth Anything V2: [https://depth-anything-v2.github.io/](https://depth-anything-v2.github.io/)

KITTI 数据集: [https://www.cvlibs.net/datasets/kitti/](https://www.cvlibs.net/datasets/kitti/)

NYU V2: [https://cs.nyu.edu/~fergus/datasets/nyu_depth_v2.html](https://cs.nyu.edu/~fergus/datasets/nyu_depth_v2.html)

虚拟 KITTI: [https://datasetninja.com/virtual-kitti](https://datasetninja.com/virtual-kitti)

Youtube 视频: [https://youtu.be/sz30TDttIBA](https://youtu.be/sz30TDttIBA)
