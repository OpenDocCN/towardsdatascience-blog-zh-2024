# 如何在卫星数据上微调预训练的视觉变换器

> 原文：[`towardsdatascience.com/how-to-fine-tune-a-pretrained-vision-transformer-on-satellite-data-d0ddd8359596?source=collection_archive---------7-----------------------#2024-03-21`](https://towardsdatascience.com/how-to-fine-tune-a-pretrained-vision-transformer-on-satellite-data-d0ddd8359596?source=collection_archive---------7-----------------------#2024-03-21)

## PyTorch Lightning 中的逐步教程

[](https://medium.com/@caroline.arnold_63207?source=post_page---byline--d0ddd8359596--------------------------------)![Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--d0ddd8359596--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d0ddd8359596--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d0ddd8359596--------------------------------) [Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--d0ddd8359596--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d0ddd8359596--------------------------------) ·6 分钟阅读·2024 年 3 月 21 日

--

![](img/052cfa4a7466bc057f2271ab6f3aaa70.png)

图像由作者使用[Midjourney](https://www.midjourney.com/jobs/dc3e78a3-9782-4624-82dd-5d4df21bb17d?index=1)创建。

[Vision Transformer](https://arxiv.org/abs/2010.11929)是一个强大的图像分类 AI 模型。它于 2020 年发布，将高效的变换器架构引入了计算机视觉领域。

在预训练阶段，AI 模型会摄取大量数据并学习常见的模式。视觉变换器是在[ImageNet-21K](https://arxiv.org/abs/2104.10972)数据集上进行预训练的，该数据集包含 1400 万张图像和 21,000 个类别。

卫星图像不包含在 ImageNet-21K 数据集中，如果直接应用，视觉变换器的性能会很差。

在这里，我将向您展示如何在来自[EuroSat 数据集](https://zenodo.org/records/7711810#.ZAm3k-zMKEA)的 27,000 张卫星图像上微调一个预训练的视觉变换器。我们将预测土地覆盖类型，如森林、农田和工业区。

![](img/5dc989f9eba1a2ab016f8d1fb6985575.png)

来自[EuroSAT RGB 数据集](https://zenodo.org/records/7711810#.ZAm3k-zMKEA)的示例图像。根据欧盟法律，哨兵数据是免费的并且对公众开放。

我们将在[PyTorch Lightning](https://lightning.ai)中进行工作，这是一个基于 PyTorch 的深度学习库。Lightning 减少了需要编写的代码量，让我们可以专注于建模。

所有代码均可在[GitHub](https://github.com/crlna16/pretrained-vision-transformer/)上找到。

## 设置项目

预训练的 Vision Transformer 可以在 Huggingface 上找到。可以从 GitHub 安装模型架构和权重。我们还需要安装 PyTorch Lightning。我在本教程中使用了版本 2.2.1，但任何版本> 2.0 应该都可以使用。

```py
pip install -q git+https://github.com/huggingface/transformers
pip install lightning=2.2.1
```

我们可以将我们的项目分为四个步骤，下面我们将详细介绍：

+   预训练的 Vision Transformer：[Lightning 模块](https://lightning.ai/docs/pytorch/stable/common/lightning_module.html)

+   EuroSAT 数据集

+   在 EuroSAT 数据集上训练 Vision Transformer

+   计算测试集上的准确率

## 将 Vision Transformer 适应我们的数据集

来自 Huggingface 的 Vision Transformer 已针对具有 1,000 个类别的 ImageNet 子集进行了优化。

我们的数据集只有 10 个类别，分别代表不同类型的土地覆盖。因此，我们需要修改 Vision Transformer 的输出部分，换成一个具有正确类别数量的新分类头。

![](img/69ef618dc1fda224692e94b5a4d2833e.png)

Vision Transformer 架构。由作者根据原始论文修改。[ [arxiv]](https://arxiv.org/pdf/2010.11929.pdf)

从 Huggingface 实例化预训练模型的代码使这变得简单。我们只需通过*num_labels*指定新的类别数量，并告诉模型忽略我们更改输出大小的事实。

```py
from transformers import ViTForImageClassification
ViTForImageClassification.from_pretrained("google/vit-base-patch16-224", 
                                          num_labels=10, 
                                          ignore_mismatched_sizes=True
                                          )
```

模型提醒我们，现在需要重新训练：

> ViTForImageClassification 的一些权重未从 google/vit-base-patch16–224 模型检查点初始化，而是因为形状不匹配而重新初始化：
> 
> …
> 
> 你可能需要在下游任务上**训练**这个模型，以便能够用于预测和推理。

我们可以选择不同版本的 Vision Transformer，在这里我们使用的是*vit-base-patch16–224*，这是最小的模型，它使用从大小为*224 x 224*像素的图像中提取的*16 x 16*块。该模型有 8580 万个参数，需要 340 MB 的内存。

## 将 Vision Transformer 作为 Lightning 模块

在 PyTorch Lightning 中，深度学习模型被定义为一个[Lightning 模块](https://lightning.ai/docs/pytorch/stable/common/lightning_module.html)。我们只需要指定

+   模型设置：加载预训练的 Vision Transformer

+   前向步骤：将模型应用于一批数据

+   训练、验证和测试步骤

+   训练中使用的优化器

训练步骤必须返回损失，这里是交叉熵损失，用于量化预测类别与真实类别之间的差异。

日志记录非常方便。通过调用*self.log*，我们可以直接将训练和评估的指标记录到我们首选的日志记录器中——在我的情况下是[TensorBoard](https://www.tensorflow.org/tensorboard)。在这里，我们记录了训练损失和验证准确率。

请注意，为了访问 Huggingface 的 Vision Transformer 做出的预测，我们需要从模型输出中提取它们，作为*predictions.logits*。

## EuroSAT 数据集的 Lightning DataModule

你可以从[Zenodo](https://zenodo.org/records/7711810#.ZAm3k-zMKEA)下载 EuroSAT 数据集。确保选择 RGB 版本，它已经从原始卫星图像转换过来。我们将在[LightningDataModule](https://lightning.ai/docs/pytorch/stable/notebooks/lightning_examples/datamodules.html#Using-DataModules)中定义数据集。

设置阶段使用了[torchvision](https://pytorch.org/vision/0.17/)的变换函数。为了符合 Vision Transformer 期望的输入，我们需要将卫星图像放大到 224 x 224 像素，将图像转换为 torch 数据类型并进行标准化。

我们将数据集分割为 70%用于训练（微调），10%用于验证，20%用于测试。通过在类标签上进行分层抽样，我们确保三个子集中的类分布相等。

*train_dataloader*等函数对于在后续的运行脚本中设置数据加载器非常方便。

## 将所有内容整合起来：运行脚本

现在我们已经有了数据集和模型的构建块，可以编写执行微调的运行脚本了。

为了清晰起见，我为数据集(*eurosat_module*)和模型(*vision_transformer*)创建了单独的模块，需要在脚本中导入。

## 训练模型

[Lightning Trainer](https://lightning.ai/docs/pytorch/stable/common/trainer.html)接收一个模型和用于训练及验证的数据加载器。它提供了多种可以自定义的标志—在这里我们仅使用了其中的三个：

+   *devices*，用于仅使用一块 GPU 进行训练

+   *early stopping callback*，如果验证损失在六个 epoch 内没有改善，则停止训练

+   *logger*，用于将训练过程记录到 TensorBoard

PyTorch Lightning 的优点是训练现在只需一行代码：

```py
trainer.fit(model=model, train_dataloaders=train_dataloader, val_dataloaders=valid_dataloader)
```

在幕后，训练器使用反向传播和 Adam 优化器来更新模型权重。当验证准确率在指定的 epoch 数量内没有提高时，训练停止。

实际上，在 EuroSAT 数据集上的微调在几个 epoch 内就完成了。面板展示了训练损失，这是由 TensorBoard 记录的。经过两个 epoch 后，模型已经达到了 98.3%的验证准确率。

![](img/1b58b4a473a4f6791d71481a7d1b4de7.png)

作者创建的 TensorBoard 快照。

## 在测试集上进行评估

我们的数据集很小，微调速度很快，所以我们不需要单独存储训练好的模型，可以直接将其应用于测试集。

只需一行代码，我们就能计算测试集上的准确率：

```py
trainer.test(model=model, dataloaders=test_dataloader, verbose=True)
```

只进行了一次微调，我就达到了 98.4%的测试集准确率，这意味着几乎所有的卫星图像的地表类型都被正确分类了。

即使样本较少，我们也能获得很高的准确性。面板展示了在微调过程中只看到一次的不同训练样本数量下的测试集准确性。仅使用 320 张卫星图像，每类平均 32 张，测试集准确性已经达到 80%。

![](img/fe1672f643e4eeda9b56bd587156dbaf.png)

图片由作者创建。

## 关键要点

预训练模型是减少训练时间的一个好方法。它们已经在一般任务上表现得很好，只需要适应你的特定数据集。

在现实应用中，数据通常稀缺。EuroSAT 数据集仅包含 27,000 张图像，大约是 ImageNet-21K 数据量的 0.5%。预训练利用了更大的数据集，我们可以高效地使用特定应用数据集进行微调。

Lightning 非常适合训练深度学习模型，而无需担心所有技术细节。LightningModule 和 Trainer API 提供了方便的抽象，并且具有高性能。

如果你想在 Huggingface 生态系统内微调视觉变换器，我推荐[这个教程](https://medium.com/@supersjgk/fine-tuning-vision-transformer-with-hugging-face-and-pytorch-df19839d5396)。

完整代码，包括允许你添加自己数据集的配置文件，已在 GitHub 上发布：

[](https://github.com/crlna16/pretrained-vision-transformer?source=post_page-----d0ddd8359596--------------------------------) [## GitHub - crlna16/pretrained-vision-transformer: 使用 PyTorch 的预训练视觉变换器…

### 使用 PyTorch Lightning 的预训练视觉变换器 - crlna16/pretrained-vision-transformer

[github.com](https://github.com/crlna16/pretrained-vision-transformer?source=post_page-----d0ddd8359596--------------------------------)

## 参考文献

+   EuroSAT 数据集：[10.5281/zenodo.7711096](https://zenodo.org/doi/10.5281/zenodo.7711096)（[Copernicus Sentinel 数据许可](https://sentinel.esa.int/documents/247904/690755/Sentinel_Data_Legal_Notice)）

+   Dosovitskiy 等人，*图像值 16x16 个单词：用于大规模图像识别的变换器*，[`arxiv.org/abs/2010.11929`](https://arxiv.org/abs/2010.11929)（2020 年）

+   视觉变换器在[Huggingface](https://huggingface.co/docs/transformers/model_doc/vit#vision-transformer-vit)和[GitHub](https://github.com/huggingface/transformers)上的资料（Apache 2.0）
