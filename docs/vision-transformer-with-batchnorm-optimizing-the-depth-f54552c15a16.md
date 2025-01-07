# 带有BatchNorm的视觉变换器（Vision Transformer）

> 原文：[https://towardsdatascience.com/vision-transformer-with-batchnorm-optimizing-the-depth-f54552c15a16?source=collection_archive---------3-----------------------#2024-11-08](https://towardsdatascience.com/vision-transformer-with-batchnorm-optimizing-the-depth-f54552c15a16?source=collection_archive---------3-----------------------#2024-11-08)

## 集成BatchNorm到标准视觉变换器架构中如何加速收敛并使网络更加稳定

[](https://medium.com/@anindya.hepth?source=post_page---byline--f54552c15a16--------------------------------)[![Anindya Dey, 博士](../Images/5045d6826256d80721b2615ae701d4b1.png)](https://medium.com/@anindya.hepth?source=post_page---byline--f54552c15a16--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f54552c15a16--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f54552c15a16--------------------------------) [Anindya Dey, 博士](https://medium.com/@anindya.hepth?source=post_page---byline--f54552c15a16--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f54552c15a16--------------------------------) ·14分钟阅读·2024年11月8日

--

![](../Images/2b832e2433fa32c38e35d881222b541f.png)

比较不同深度下，带有和不带有BatchNorm的视觉变换器。

## 介绍

[视觉变换器](https://arxiv.org/abs/2010.11929)（ViT）是首个完全基于自注意力机制的图像分类任务架构。尽管ViT在性能上优于基于卷积神经网络（CNN）的架构，但它们需要在非常大的数据集上进行预训练。为了寻找可能加速训练和推理的ViT改进方案——尤其是在中小规模输入数据的情况下——我在[上一篇文章](https://medium.com/towards-data-science/speeding-up-the-vision-transformer-with-batch-normalization-d37f13f20ae7)中探讨了将Batch Normalization（BatchNorm）集成到ViT型模型中的方法。BatchNorm已知能够加速深度神经网络的收敛——与基准模型相比，使用BatchNorm的网络在相同训练周期数下能获得更高的准确率。这反过来加快了训练速度。BatchNorm还作为一种有效的正则化方法，允许模型使用更高的学习率进行训练。本文的主要目标是研究引入BatchNorm是否能在视觉变换器中产生类似的效果。

为了具体化，我将重点讨论在ViT的Transformer编码器中的前馈网络(FFN)引入BatchNorm层，并省略FFN之前的LayerNorm层的模型。在Transformer的其他部分——包括自注意力模块——仍然使用LayerNorm。我将该版本的ViT称为**ViTBNFFN——在前馈网络中使用BatchNorm的视觉Transformer**。我将使用带有图像增强的MNIST数据集对该模型进行训练和测试，并在多个训练周期内比较该模型的Top-1准确率与标准ViT的准确率。我将为这两个模型选择相同的架构配置（即相同的宽度、深度、补丁大小等），以便有效隔离BatchNorm层的影响。

**以下是主要发现的快速总结：**

1.  **在合理选择的超参数（学习率和批量大小）下，ViTBNFFN比ViT收敛得更快，前提是Transformer的深度（即编码器中的层数）足够大。**

1.  **随着学习率的增加，ViTBNFFN比ViT更稳定，尤其是在更深的网络中。**

我将简要讨论深度神经网络中的BatchNorm，并通过具体示例说明上述提到的一些特性。接着，我将详细讨论ViTBNFFN模型的架构。最后，我将深入探讨研究BatchNorm在视觉Transformer中作用的数值实验。

你可以在github的[仓库](https://github.com/anindyahepth/BatchNorm_in_Transformers_CV)上分叉本文及上一篇文章中使用的代码并进行实验！

## 数据集：带有图像增强的MNIST

让我们从介绍增强版MNIST数据集开始，我将在本文描述的所有数值实验中使用该数据集。训练和测试数据集由函数*get_datasets_mnist()*提供，如代码块1所示。

代码块1\. 通过增强MNIST数据集得到的训练和验证数据集。

关键的代码行位于第5到第10行，列出了我将使用的图像增强的详细信息。我引入了三种不同的变换：

1.  [*随机旋转*](https://pytorch.org/vision/main/generated/torchvision.transforms.RandomRotation.html)*(degrees=20) :* 对图像进行随机**旋转**，旋转角度范围为(-20, 20)。

1.  [*RandomAffine*](https://pytorch.org/vision/main/generated/torchvision.transforms.RandomAffine.html)*(degrees = 0, translate = (0.2, 0.2)) :* 一个随机**仿射变换**，其中 *translate = (a, b)* 表示水平和垂直的平移分别在区间[- image_width × a, image_width × a] 和 [-image_height × b, image_height × b] 内随机采样。*degrees=0* 语句禁用旋转，因为我们已经通过随机旋转考虑了这一点。这里也可以包含缩放变换，但我们通过缩小操作实现了它。

1.  [*RandomZoomOut*](https://pytorch.org/vision/0.16/generated/torchvision.transforms.v2.RandomZoomOut.html)*(0,(2.0, 3.0), p=0.2) :* 一个随机**缩小变换**，它随机采样区间(2.0, 3.0)中的浮动 r，并输出一个图像，其输出宽度 = 输入宽度 × r，输出高度 = 输入高度 × r。浮动 p 是执行缩放操作的概率。该变换之后跟随一个 *Resize* 变换，确保最终图像的大小仍为 28 × 28。

## 深度神经网络中的批量归一化

让我们快速回顾一下 BatchNorm 如何提高深度神经网络的性能。假设 zᵃᵢ 表示深度神经网络中给定层的输入，其中 a 是批次索引，范围为 a=1,…, Nₛ，i 是特征索引，范围为 i=1,…, C。BatchNorm 操作包括以下步骤：

1.  对于给定的特征索引 i，首先计算批量大小为 Nₛ 的均值和方差，即：

![](../Images/47a755e9deaa520e8fe506951b467242.png)

2\. 使用上述计算的均值和方差对输入进行归一化（其中 ϵ 是一个小的正数）：

![](../Images/3456f9e44ca27e3de80da39c53e01c1f.png)

3\. 最后，对于每个特征 i，移动并重新缩放归一化后的输入：

![](../Images/de8cc1c858608a62b598167d7ff3ee23.png)

其中没有对索引 i 进行求和，参数（γᵢ，βᵢ）是可训练的。

考虑一个用于分类 MNIST 数据集的深度神经网络。我将选择一个由 3 个全连接隐藏层组成的网络，每个隐藏层有 100 个激活单元，每个隐藏层都配有 sigmoid 激活函数。最后一个隐藏层连接到一个分类层，分类层有 10 个激活单元，对应 MNIST 数据集的 10 个类别。该神经网络的输入是一个形状为 b × 28² 的二维张量——其中 b 是批次大小，每个 28 × 28 的 MNIST 图像被重塑为一个 28² 维的向量。在这种情况下，特征索引从 i=1,…, 28² 运行。

该模型类似于原始BatchNorm[论文](https://arxiv.org/abs/1502.03167)中讨论的模型——我将这个模型称为**DNN_d3**。可以考虑在每个隐藏层的sigmoid激活函数*之前*添加一个BatchNorm层的版本。我们将得到的模型称为**DNNBN_d3**。其目的是了解引入BatchNorm层如何影响网络的性能。

现在，为了实现这一点，让我们在上面描述的MNIST数据集上训练和测试这两个模型，使用**CrossEntropyLoss()**作为损失函数和**Adam**优化器，训练15个周期。对于学习率lr=0.01和训练批次大小为100（我们选择测试批次大小为5000），模型的测试准确率和训练损失如图1所示。

![](../Images/95d5acbe0592685ff489a3ef76a73860.png)

图1\. 两种模型在lr=0.01下训练15个周期的测试准确率（左）和训练损失（右）。

显然，引入BatchNorm使得网络收敛得更快——DNNBN达到了更高的测试准确率和更低的训练损失。因此，BatchNorm可以加速训练。

如果增加学习率会发生什么？一般来说，较高的学习率可能导致梯度爆炸或消失，从而使训练变得不稳定。特别是，较大的学习率会导致更大的层参数，这又会在反向传播过程中产生更大的梯度。然而，BatchNorm确保反向传播过程不会受到层参数尺度变换的影响（更多细节见[本文](https://arxiv.org/abs/1502.03167)的第3.3节）。这使得网络在面对较高学习率时，能显著提高对不稳定性的抗性。

为了明确地展示这一点，假设我们以更高的学习率lr=0.1训练这些模型——在这种情况下，模型的测试准确率和训练损失如图2所示。

![](../Images/80984a02e0fc04140736d9b65652f2f4.png)

图2\. 两种模型在lr=0.1下训练15个周期的测试准确率（左）和训练损失（右）。

高学习率显然使得DNN不稳定。然而，使用BatchNorm的模型表现得非常稳定！一种更具启发性的方式是，将两种学习率下的准确率曲线绘制在同一图中，如图3所示。

![](../Images/92043232fa809807ada1f98d06c3d56c.png)

图3\. DNN_d3（左）和DNNBN_d3（右）在两种不同学习率下的准确率曲线。

当模型DNN_d3在高学习率下停止训练时，DNNBN_d3的性能影响要轻得多。因此，BatchNorm允许在更高的学习率下训练模型，提供了一种加速训练的方式。

## 模型ViTBNFFN：前馈网络中的BatchNorm

让我们首先简要回顾标准 Vision Transformer（用于图像分类任务）的架构，如图 4 中的示意图所示。如需更多详细信息，请参见我之前的 [文章](https://medium.com/towards-data-science/speeding-up-the-vision-transformer-with-batch-normalization-d37f13f20ae7)，或阅读 *Towards Data Science* 中的许多精彩综述。

![](../Images/ff62cd273698d510f30253b47d4c3e59.png)

图 4. ViT 架构的示意图。

从功能上讲，Vision Transformer 的架构可以分为三个主要组件：

1.  **嵌入层：** 该层将图像映射为“句子”——一系列标记，其中每个标记是一个维度为 dₑ（嵌入维度）的向量。给定大小为 h × w 和 c 个颜色通道的图像，首先将其分割成大小为 p × p 的补丁并展平——这将得到 (h × w)/p² 个展平的补丁（或标记），每个补丁的维度为 dₚ = p² × c，接着通过可学习的线性变换将其映射为维度为 dₑ 的向量。然后，为这一系列标记添加一个可学习的标记——CLS 标记——并将其隔离在末尾用于分类任务。示意图如下所示：

![](../Images/1e0081a2272df502706560960328606e.png)

最后，为该标记序列添加一个相同形状的可学习张量，该张量编码位置嵌入信息。结果的标记序列被输入到 Transformer 编码器。因此，编码器的输入是一个形状为 b × N × dₑ 的 3d 张量——其中 b 是批量大小，N 是标记的数量（包括 CLS 标记），dₑ 是嵌入维度。

**2. Transformer 编码器：** Transformer 编码器将标记序列映射为另一个标记序列，二者的数量和形状相同。换句话说，它将形状为 b × N × dₑ 的输入 3d 张量映射为另一个形状相同的 3d 张量。编码器可以有 L 个不同的层（定义为 Transformer 的 *深度*），每一层由两个子模块组成，如图 5 所示——多头自注意力（MHSA）和前馈网络（FFN）。

![](../Images/9f26956bf51b004ca21b306cf1598991.png)

图 5. Transformer 编码器的子模块。

MHSA 模块在形状为 b × N × dₑ 的 3d 张量上实现非线性映射，映射结果是一个形状相同的 3d 张量，随后该张量被送入 FFN，如图 2 所示。这是信息通过自注意力映射在不同标记之间混合的地方。MHSA 模块的配置由头数 nₕ 和头维度 dₕ 决定。

FFN 是一个深度神经网络，包含两层线性层，中间有一个 GELU 激活函数，如图 6 所示。

![](../Images/713cf7a4319c22383efa9dd3e3329539.png)

图 6. Transformer 编码器层中的 FFN 模块。

该子模块的输入是形状为 b × N × dₑ 的 3d 张量。左侧的线性层将其转换为形状为 b × N × d_mlp 的 3d 张量，其中 d_mlp 是网络的隐藏维度。经过非线性 GELU 激活后，张量通过第二层被映射回原始形状的张量。

**3\. MLP 头：** MLP 头是一个全连接网络，将 transformer 编码器的输出 **—** 形状为 b × N × dₑ 的 3d 张量 — 映射到形状为 b × d_num 的 2d 张量，其中 d_num 是给定图像分类任务中的类别数。这通过首先从输入张量中提取 CLS 令牌，然后将其传递通过连接的网络来完成。

模型 ViTBNFFN 与上述描述的架构相同，主要有两个区别。首先，在编码器的 FFN 中引入了一个 BatchNorm 层，该层位于第一个线性层和 GELU 激活之间，如图 7 所示。其次，移除了标准 ViT 编码器中 FFN 之前的 LayerNorm（参见上面的图 5）。

![](../Images/faeca938d7ba5a5a4f0c3168f49e958b.png)

图 7\. ViTBNFFN 模型的 FFN 子模块。

由于线性变换作用于形状为 b × N × dₑ 的输入张量的第三维，我们应将 dₑ 视为 BatchNorm 的特征维度。新前馈网络的 PyTorch 实现见代码块 2。

代码块 2\. 带有 BatchNorm 的 FFN 模块，用于 ViTBNFFN。

PyTorch 中内置的 BatchNorm 类总是将张量的第一个索引视为批次索引，第二个索引视为特征索引。因此，我们需要将形状为 b × N × dₑ 的 3d 张量转换为形状为 b × dₑ × N 的张量，才能应用 BatchNorm，然后再将其转换回形状为 b × N × dₑ 的 3d 张量。此外，我使用了 2d BatchNorm 类（因为它比 1d BatchNorm 略快）。这要求将 3d 张量提升为形状为 b × dₑ × N × 1 的 4d 张量（第 16 行），并在第 18 行将其转换回形状为 b × N × dₑ 的 3d 张量。使用 1d BatchNorm 类也可以得到相同的结果，无需更改本节中展示的任何内容。

## 实验

在固定的学习率和批量大小下，我将在增强版 MNIST 数据集上训练和测试两个模型 — ViT 和 ViTBNFFN — 进行 10 轮训练，并比较验证数据集上的 Top-1 准确率。由于我们关注 BatchNorm 的效果，因此我们需要比较这两个具有相同配置的模型。该实验将在不同深度的 transformer 编码器上重复进行，其余模型配置保持不变。我在此实验中使用的两个模型的具体配置如下：

1.  嵌入层：MNIST 图像是一个 28× 28 的灰度图像。补丁大小为 p= 7，这意味着令牌的数量为 16 + 1 = 17，包括 CLS 令牌。嵌入维度为 dₑ = 64。

1.  变压器编码器：MHSA子模块有nₕ = 8个头，每个头的维度为dₕ=64。FFN的隐藏层维度为d_mlp = 128。编码器的深度将是该架构中唯一可变的参数。

1.  MLP头：MLP头将仅由一个线性层组成。

所有epoch的训练和测试批次大小分别固定为100和5000，损失函数使用**CrossEntropyLoss()**，优化器为**Adam**。在嵌入层和编码器中，丢弃率参数都设置为零。我使用了**NVIDIA L4 Tensor Core GPU**，并在**Google Colab**上进行所有实验，实验过程通过**MLFlow**的跟踪功能进行记录。

让我们从学习率lr=0.003开始训练和测试模型。下面的图8总结了四个图表，分别绘制了在d=4、5、6和7深度下，两个模型的准确率曲线。在这些图表中，符号ViT_dn（ViTBNFFN_dn）表示ViT（ViTBNFFN）模型，编码器的深度为d=n，其他模型配置与上述相同。

![](../Images/052efed3bfa9eae1b04622a9be394fdf.png)

图8\. 两个模型在lr=0.003时，深度为4、5、6和7的准确率曲线对比。

对于d=4和d=5（图表的顶部行），这两个模型的准确率相当——对于d=4（左上方），ViT稍微做得更好，而对于d=5（右上方），ViTBNFFN略微超过了ViT。在d < 4时，准确率保持相当。然而，对于d=6和d=7（图表的底部行），ViTBNFFN的表现显著优于ViT。可以验证，这一定性特征对于任何深度d ≥ 6都保持一致。

让我们在稍高的学习率lr=0.005下重复实验。两个模型在深度d=1、2、3和4时的准确率曲线总结在图9中。

![](../Images/7d54ee4eee9643d7f20683cbb02b6221.png)

图9\. 两个模型在lr=0.005时，深度为1、2、3和4的准确率曲线对比。

对于d=1和d=2（图表的顶部行），这两个模型的准确率相当——对于d=1，ViT稍微做得更好，而对于d=2，它们几乎没有区别。对于d=3（左下方），ViTBNFFN的准确率略高于ViT。而对于d=4（右下方），ViTBNFFN的表现明显优于ViT，这一质量特征对于任何深度d ≥ 4都保持一致。

因此，在合理选择学习率和批次大小的情况下，ViTBNFFN在变压器编码器的临界深度之后收敛速度显著快于ViT。在我考虑的超参数范围内，似乎随着学习率的增加，固定批次大小时该临界深度会变得更小。

对于深度神经网络的例子，我们看到高学习率对带有BatchNorm的网络的影响显著减轻。那么，Vision Transformer是否也存在类似的情况？这一问题在图10中得到了回答。这里的每一张图绘制了在给定深度下，对于两种不同学习率lr=0.003和lr=0.005的模型的准确率曲线。第一列图表对应于d=2、3和4的ViT（从上到下），而第二列图表则对应于相同深度下的ViTBNFFN。

![](../Images/923f5c93e8ee7158f126a62732ae8935.png)

图10\. 不同深度下，ViT和ViTBNFFN在两种学习率下的准确率曲线。

考虑d=2 —— 如上排图所示 —— ViT和ViTBNFFN在增加学习率时受到的影响相当相似。对于d=3 —— 如第二排图所示 —— 差异则显著。ViT在较高学习率下的准确率大幅下降——准确率从约91%下降到约78%，这是在第10个周期结束时的结果。另一方面，对于ViTBNFFN，第10个周期结束时的准确率从约92%下降到约90%。这个定性特征在更高深度下仍然保持不变——请参见对应d=4的底部图表。因此，对于足够深的Transformer编码器，高学习率对ViTBNFFN的影响显得明显温和。

## **结论**

在本文中，我研究了在Vision Transformer中的Transformer编码器内引入BatchNorm层的效果。在增强版MNIST数据集上比较这些模型时，有两个主要的结论可以得出。首先，对于足够深的Transformer，并且在合理的超参数选择下，带有BatchNorm的模型相比标准ViT能显著提高准确率。这种更快的收敛可以大大加速训练。其次，类似于我们对深度神经网络的直觉，如果编码器足够深，带有BatchNorm的Vision Transformer对于较高的学习率更具韧性。

感谢阅读！如果你已经读到文章的结尾并且喜欢这篇文章，请留下掌声和/或评论，并关注我获取更多内容！除非另有说明，本文中使用的所有图像和图表均由作者生成。
