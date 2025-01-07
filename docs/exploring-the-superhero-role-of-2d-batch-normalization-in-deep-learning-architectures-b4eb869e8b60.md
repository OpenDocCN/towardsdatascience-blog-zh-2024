# 探索二维批量归一化在深度学习架构中的超级英雄角色

> 原文：[https://towardsdatascience.com/exploring-the-superhero-role-of-2d-batch-normalization-in-deep-learning-architectures-b4eb869e8b60?source=collection_archive---------9-----------------------#2024-01-12](https://towardsdatascience.com/exploring-the-superhero-role-of-2d-batch-normalization-in-deep-learning-architectures-b4eb869e8b60?source=collection_archive---------9-----------------------#2024-01-12)

## 通过简单的例子来解释内部工作原理和直觉

[](https://murali-kashaboina.medium.com/?source=post_page---byline--b4eb869e8b60--------------------------------)[![Murali Kashaboina](../Images/ff1118f3c317dab87fe4b625a614fb93.png)](https://murali-kashaboina.medium.com/?source=post_page---byline--b4eb869e8b60--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b4eb869e8b60--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b4eb869e8b60--------------------------------) [Murali Kashaboina](https://murali-kashaboina.medium.com/?source=post_page---byline--b4eb869e8b60--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4eb869e8b60--------------------------------) ·10分钟阅读·2024年1月12日

--

![](../Images/4e13dca13cb4875088d4c47bef20965f.png)

图像由作者创建

深度学习（DL）在卷积神经网络（CNN）和生成式人工智能（Gen AI）的发展中起到了革命性的作用。这样的深度学习模型能够从多维空间数据（如图像）中提取复杂的模式和特征，并进行预测。输入数据中的模式越复杂，模型架构也可以越复杂。有很多方法可以加速模型训练的收敛速度并提升模型推理性能，但二维批量归一化（BN2D）已经成为该领域的“超级英雄”。本文旨在展示将BN2D集成到深度学习架构中如何带来更快的收敛速度和更好的推理效果。

## 了解BN2D

BN2D是一种归一化技术，应用于批量处理的多维空间输入（如图像），通过归一化它们的维度（通道）值，使得这些批次中的各维度具有均值为0，方差为1的分布。

引入BN2D组件的主要目的是防止输入数据在网络中前一层的维度或通道之间出现内部协变量偏移。维度之间的内部协变量偏移发生在训练周期中，由于网络参数的更新，维度数据的分布发生变化。例如，卷积层中的N个滤波器会产生N维的激活作为输出。该层为其滤波器维护权重和偏差参数，这些参数会在每个训练周期中逐步更新。

由于这些更新，来自一个滤波器的激活可能与来自同一卷积层的另一个滤波器的激活有明显不同的分布。这种分布差异表明，一个滤波器的激活与另一个滤波器的激活处于完全不同的尺度。当这样的维度数据以相差悬殊的尺度输入到网络的下一个层时，网络该层的可学习性会受到影响，因为尺度较大的维度在梯度下降时需要较大的更新，而尺度较小的维度则需要较小的更新。

另一种可能的后果是，尺度较小的权重梯度可能会消失，而尺度较大的权重梯度可能会爆炸。当网络遇到这种学习障碍时，梯度下降将在较大尺度的维度之间震荡，严重阻碍学习收敛和训练稳定性。BN2D通过将维度数据标准化为均值为0、标准差为1的标准尺度，有效缓解了这种现象，并促进了训练期间的更快收敛，减少了达到最佳性能所需的训练周期数。因此，通过简化网络的训练过程，该技术确保网络可以集中精力学习更复杂、更抽象的特征，从输入数据中提取更丰富的表示。

在标准实践中，BN2D实例被插入在卷积层后，但在激活层之前，如图1所示的示例深度学习网络中。

![](../Images/cfa49a7a52a210f3ec43e17f7ad888cd.png)

图1：一个示例深度卷积神经网络（图片由作者创建）

## BN2D的内部工作原理

图2展示了一个简单的多维空间数据批次（如3通道图像），用来说明BN2D技术的内部工作原理。

![](../Images/e9409d2f31f38493938b0732c1cc2d97.png)

图2：BN2D的内部工作原理（图片由作者创建）

如图2所示，BN2D通过在每个维度或通道上处理一个批次来工作。如果输入批次有N个维度或通道，则该BN2D实例将具有N个BN2D层。示例中对红色、绿色和蓝色通道的独立处理意味着相应的BN2D实例具有3个BN2D层。

![](../Images/71f2e87fc6285cac9df0f5a7a429c310.png)

图3：BN2D使用的公式（图片由作者创建）

在训练过程中，BN2D 为每个批次维度计算均值和方差，并按照图 2 所示的方式进行归一化，使用图 3 所示的训练时公式。预设的 epsilon（ε）是分母中的常数，用于避免除以零的情况。BN2D 实例为每个维度或 BN2D 层维护可学习的缩放（γ）和平移（β）参数，这些参数在训练优化过程中进行更新。BN2D 实例还维护每个 BN2D 层的移动平均和方差，如图 2 所示，这些值在训练过程中根据图 3 所示的公式进行更新。预设的动量（α）作为指数平均因子使用。

在推理过程中，使用图 3 所示的推理时公式，BN2D 实例会为每个维度使用维度特定的移动平均、移动方差以及学习到的缩放（γ）和平移（β）参数来归一化数值。图 2 显示了每个批次输入维度的示例训练时批量归一化计算。图 2 中的示例还展示了 BN2D 实例的输出，包含整个批次的独立维度或通道归一化。用于演示图 2 中示例的 PyTorch Jupyter Notebook 可通过以下 GitHub 仓库访问。

[https://github.com/kbmurali/hindi_hw_digits/blob/main/how_batch_norm2d_works.ipynb](https://github.com/kbmurali/hindi_hw_digits/blob/main/how_batch_norm2d_works.ipynb)

## BN2D 的应用

为了检查在深度学习网络架构中引入 BN2D 实例后的预期性能提升，使用了一个简单的（类似玩具的）图像数据集来构建相对简单的深度学习网络，分别使用和不使用 BN2D 来预测类别。以下是期望通过 BN2D 实现的深度学习模型性能提升：

1.  改进的泛化能力：BN2D 引入的归一化预计能够改善深度学习模型的泛化能力。在示例中，当在网络中引入 BN2D 层时，推理时分类准确率预计会得到提升。

1.  更快的收敛：引入 BN2D 层预计会促进训练过程中的更快收敛，减少达到最佳性能所需的训练轮次。在示例中，预计在引入 BN2D 层后的早期轮次，训练损失将会降低。

1.  更平滑的梯度下降：由于 BN2D 将数据标准化到均值为 0，标准差为 1 的标准尺度，预计可以最小化梯度下降在较大尺度维度上的震荡，梯度下降预计会平稳进行。

## 示例数据集

印地语手写数字（0–9）数据由 Kaggle 发布，数据链接为 [https://www.kaggle.com/datasets/suvooo/hindi-character-recognition/data](https://www.kaggle.com/datasets/suvooo/hindi-character-recognition/data)（GNU 许可证），用于训练和测试包含和不包含 BN2D 的卷积深度学习模型。请参考本文顶部的横幅图片，查看印地语数字的书写方式。深度学习模型的网络使用 PyTorch 深度学习模块构建。选择印地语手写数字而非英语手写数字，是因为前者的复杂度高于后者。由于印地语数字中的曲线比直线更多，因此在印地语数字中的边缘检测比在英语数字中更具挑战性。此外，由于书写风格的不同，同一个数字的书写方式可能会有更多变化。

开发了一个实用的 Python 函数，使得访问数字数据更加符合 PyTorch 数据集/数据加载器的标准，如下代码片段所示。训练数据集包含 17000 个样本，测试数据集包含 3000 个样本。请注意，在加载图像为 PyTorch 张量时，应用了 PyTorch 灰度图像转换器。一个名为‘ml_utils.py’的实用模块专门用于封装函数，以使用 PyTorch 张量操作运行迭代周期，训练和测试深度学习模型。训练和测试函数还会捕获模型的指标，以帮助评估模型的表现。Python 笔记本和实用模块可以在作者的公开 GitHub 仓库中找到，链接如下所示。

[https://github.com/kbmurali/hindi_hw_digits](https://github.com/kbmurali/hindi_hw_digits)

```py
import torch
import torch.nn as nn
from torch.utils.data import *
import torchvision
from torchvision import transforms
from sklearn.metrics import accuracy_score
import matplotlib.pyplot as plt
import seaborn as sns

from ml_utils import *
from hindi.datasets import Digits

set_seed( 5842 )

batch_size = 32

img_transformer = transforms.Compose([
                        transforms.Grayscale(),
                        transforms.ToTensor()
                    ])

train_dataset = Digits( "./data", train=True, transform=img_transformer, download=True )

test_dataset = Digits( "./data", train=False, transform=img_transformer, download=True )

train_loader = DataLoader( train_dataset, batch_size=batch_size, shuffle=True )

test_loader = DataLoader( test_dataset, batch_size=batch_size )
```

## 示例深度学习模型

第一个深度学习（DL）模型包含三个卷积层，每个卷积层有 16 个滤波器，卷积核大小为 3，填充为 1，从而实现‘Same’卷积。每个卷积层的激活函数是修正线性单元（ReLU）。最大池化层的池大小为 2，位于全连接层之前，最终通过 softmax 层输出 10 类结果。该模型的网络架构如图 4 所示。相应的 PyTorch 模型定义如下代码片段所示。

![](../Images/79421bd55a1a4a869202dca57b2c0c89.png)

图 4：没有 BN2D 的卷积网络（图片由作者创建）

```py
device = torch.device( 'cuda' if torch.cuda.is_available() else 'cpu' )
loss_func = nn.CrossEntropyLoss()
input_channels = 1
classes = 10
filters = 16
kernel_size = 3
padding = kernel_size//2
pool_size = 2
original_pixels_per_channel = 32*32

three_convs_model = nn.Sequential(
                                    nn.Conv2d( input_channels, filters, kernel_size, padding=padding ), # 1x32x32 => 16x32x32
                                    nn.ReLU(inplace=True), #16x32x32 => 16x32x32
                                    nn.Conv2d(filters, filters, kernel_size, padding=padding ), # 16x32x32 => 16x32x32
                                    nn.ReLU(inplace=True), #16x32x32 => 16x32x32
                                    nn.Conv2d(filters, filters, kernel_size, padding=padding ), # 16x32x32 => 16x32x32
                                    nn.ReLU(inplace=True), #16x32x32 => 16x32x32
                                    nn.MaxPool2d(pool_size), # 16x32x32 => 16x16x16

                                    nn.Flatten(), # 16x16x16 => 4096
                                    nn.Linear( 4096, classes) # 1024 => 10
                                )
```

第二个深度学习（DL）模型与第一个模型结构类似，但在卷积后和激活前引入了 BN2D 实例。该模型的网络架构如图 5 所示。相应的 PyTorch 模型定义如下代码片段所示。

![](../Images/4c7125aa3003cee916a07be5148fd8e8.png)

图 5：带 BN2D 的卷积网络（图片由作者创建）

```py
three_convs_wth_bn_model = nn.Sequential(
                                            nn.Conv2d( input_channels, filters, kernel_size, padding=padding ), # 1x32x32 => 16x32x32
                                            nn.BatchNorm2d( filters ), #16x32x32 => 16x32x32
                                            nn.ReLU(inplace=True), #16x32x32 => 16x32x32
                                            nn.Conv2d(filters, filters, kernel_size, padding=padding ), # 16x32x32 => 16x32x32
                                            nn.BatchNorm2d( filters ), #16x32x32 => 16x32x32
                                            nn.ReLU(inplace=True), #16x32x32 => 16x32x32
                                            nn.Conv2d(filters, filters, kernel_size, padding=padding ), # 16x32x32 => 16x32x32
                                            nn.BatchNorm2d( filters ), #16x32x32 => 16x32x32
                                            nn.ReLU(inplace=True), #16x32x32 => 16x32x32
                                            nn.MaxPool2d(pool_size), # 16x32x32 => 16x16x16

                                            nn.Flatten(), # 16x16x16 => 4096
                                            nn.Linear( 4096, classes) # 4096 => 10
                                       )
```

这两个深度学习（DL）模型在示例印地语数字数据集上进行了训练，使用了如下代码片段所示的实用函数。请注意，捕获了最后一个卷积层中两个维度/通道的滤波器的两个样本权重，用于可视化训练损失的梯度下降过程。

```py
three_convs_model_results_df = train_model( 
                                            three_convs_model,
                                            loss_func, 
                                            train_loader, 
                                            test_loader=test_loader, 
                                            score_funcs={'accuracy': accuracy_score}, 
                                            device=device, 
                                            epochs=30,
                                            capture_conv_sample_weights=True, 
                                            conv_index=4, 
                                            wx_flt_index=3, 
                                            wx_ch_index=4, 
                                            wx_ro_index=1, 
                                            wx_index=0,
                                            wy_flt_index=3,
                                            wy_ch_index=8, 
                                            wy_ro_index=1, 
                                            wy_index=0
                                         )

three_convs_wth_bn_model_results_df = train_model( 
                                                    three_convs_wth_bn_model,
                                                    loss_func, 
                                                    train_loader, 
                                                    test_loader=test_loader, 
                                                    score_funcs={'accuracy': accuracy_score}, 
                                                    device=device, 
                                                    epochs=30,
                                                    capture_conv_sample_weights=True, 
                                                    conv_index=6, 
                                                    wx_flt_index=3, 
                                                    wx_ch_index=4, 
                                                    wx_ro_index=1, 
                                                    wx_index=0,
                                                    wy_flt_index=3,
                                                    wy_ch_index=8, 
                                                    wy_ro_index=1, 
                                                    wy_index=0
                                                 )
```

## 发现1：更高的测试准确度

使用BN2D实例时，DL模型的测试准确度更高，如图6所示。使用BN2D的模型在训练轮次中测试准确度逐渐提高，而没有BN2D的模型则在训练轮次中出现波动。在第30个训练轮次结束时，使用BN2D的模型测试准确度为99.1%，而没有BN2D的模型为92.4%。这些结果表明，加入BN2D实例对模型的表现有积极影响，显著提高了测试准确度。

```py
sns.lineplot( x='epoch', y='test accuracy', data=three_convs_model_results_df, label="Three Convs Without BN2D Model" )
sns.lineplot( x='epoch', y='test accuracy', data=three_convs_wth_bn_model_results_df, label="Three Convs Wth BN2D Model" )
```

![](../Images/50762ac4b67a6cc01a89757113068d2e.png)

图6：测试准确度与训练轮次的关系（作者制作的图像）

## 发现2：更快的收敛

如图7所示，使用BN2D实例时，DL模型的训练损失显著较低。在大约第3个训练轮次时，使用BN2D的模型比没有BN2D的模型表现出较低的训练损失。较低的训练损失表明，BN2D有助于训练过程中更快的收敛，可能减少了合理收敛所需的训练轮次。

```py
sns.lineplot( x='epoch', y='train loss', data=three_convs_model_results_df, label="Three Convs Without BN2D Model" )
sns.lineplot( x='epoch', y='train loss', data=three_convs_wth_bn_model_results_df, label="Three Convs Wth BN2D Model" )
```

![](../Images/6f6fc394b8216181c525f7b720591c9f.png)

图7：训练损失与训练轮次的关系（作者制作的图像）

## 发现3：更平滑的梯度下降

如图8所示，使用BN2D的模型在最后一个卷积层的两个样本权重上的损失函数呈现出比没有BN2D时更平滑的梯度下降。没有BN2D的模型的损失函数则表现为较为锯齿状的梯度下降。BN2D带来的更平滑的梯度下降表明，将维度数据归一化为均值为0、标准差为1的标准尺度，使得不同维度的权重可能处于类似的尺度，从而减少梯度下降过程中的波动。

```py
fig1 = draw_loss_descent( three_convs_model_results_df, title='Three Convs Model Without BN2D Training Loss' )
fig2 = draw_loss_descent( three_convs_wth_bn_model_results_df, title='Three Convs With BN2D Model Training Loss' )
```

![](../Images/9094c7646a7ecb72dd92a28bb0925bc3.png)

图8：样本权重上的损失函数梯度下降（作者制作的图像）

## 实际考虑

虽然BN2D的好处显而易见，但其实现需要谨慎考虑。权重的正确初始化、合适的学习率以及BN2D层在DL网络中的位置都是最大化其效果的关键因素。尽管BN2D通常能防止过拟合，但在某些情况下，它可能反而促成过拟合。例如，当BN2D与另一种叫做Dropout的技术一起使用时，根据具体配置和数据集的不同，二者的组合可能对过拟合产生不同的影响。同样，在小批量数据的情况下，批量的均值和方差可能无法准确代表整个数据集的统计特性，这可能导致噪声归一化，从而无法有效防止过拟合。

## 结论

本文旨在展示在深度学习网络中使用 BN2D 的直觉。使用玩具图像数据的卷积模型示例仅用于展示将 BN2D 实例融入深度学习网络架构中的预期性能提升。BN2D 在空间和通道维度上的归一化带来了训练稳定性、更快的收敛性和更强的泛化能力，最终有助于深度学习模型的成功。希望本文能够让读者很好地理解 BN2D 的工作原理及其背后的直觉。这种理解和直觉在开发更复杂的深度学习模型时非常有用。

参考文献：

[## 印地语字符识别](https://www.kaggle.com/datasets/suvooo/hindi-character-recognition/data?source=post_page-----b4eb869e8b60--------------------------------)

### 解决分类天城文字符号（Devanagari script）的问题。

[www.kaggle.com](https://www.kaggle.com/datasets/suvooo/hindi-character-recognition/data?source=post_page-----b4eb869e8b60--------------------------------) [## BatchNorm2d - PyTorch 2.1 文档](https://pytorch.org/docs/stable/generated/torch.nn.BatchNorm2d.html?source=post_page-----b4eb869e8b60--------------------------------)

### 加入 PyTorch 开发者社区，贡献、学习并解答你的问题。

[pytorch.org](https://pytorch.org/docs/stable/generated/torch.nn.BatchNorm2d.html?source=post_page-----b4eb869e8b60--------------------------------) [## 为什么在特征中使用 2D 批归一化，在分类器中使用 1D 批归一化？](https://discuss.pytorch.org/t/why-2d-batch-normalisation-is-used-in-features-and-1d-in-classifiers/88360/3?source=post_page-----b4eb869e8b60--------------------------------)

### BatchNorm2d 和 BatchNorm1d 有什么区别？为什么在特征中使用 BatchNorm2d，在分类器中使用 BatchNorm1d？

[Keras 文档：BatchNormalization 层](https://keras.io/api/layers/normalization_layers/batch_normalization/?source=post_page-----b4eb869e8b60--------------------------------)

### Keras 文档

: BatchNormalization 层 Keras 文档 [keras.io](https://keras.io/api/layers/normalization_layers/batch_normalization/?source=post_page-----b4eb869e8b60--------------------------------)
