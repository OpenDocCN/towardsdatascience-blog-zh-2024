# 使用 PaliGemma 对卫星图像中的水体进行分割

> 原文：[https://towardsdatascience.com/segmenting-water-in-satellite-images-using-paligemma-b172dc0cf55d?source=collection_archive---------2-----------------------#2024-12-29](https://towardsdatascience.com/segmenting-water-in-satellite-images-using-paligemma-b172dc0cf55d?source=collection_archive---------2-----------------------#2024-12-29)

## 一些关于使用 Google 最新视觉语言模型的见解

[](https://anamabo3.medium.com/?source=post_page---byline--b172dc0cf55d--------------------------------)[![Dr. Carmen Adriana Martínez Barbosa](../Images/caad66f044af1131e17dc28ea2f48863.png)](https://anamabo3.medium.com/?source=post_page---byline--b172dc0cf55d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b172dc0cf55d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b172dc0cf55d--------------------------------) [Dr. Carmen Adriana Martínez Barbosa](https://anamabo3.medium.com/?source=post_page---byline--b172dc0cf55d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b172dc0cf55d--------------------------------) ·9 分钟阅读·2024 年 12 月 29 日

--

![](../Images/e9d032d36ebb9f252e75c1a95d5b64cb.png)

澳大利亚的哈特湖。根据季节、时间和云层覆盖情况，这个湖泊的颜色会从红色变为粉色或紫色。来源：Google Maps。

多模态模型是一种架构，能够同时集成和处理不同类型的数据，如文本、图像和音频。一些例子包括 OpenAI 的 CLIP 和 DALL-E，这两者都在 2021 年发布。CLIP 可以联合理解图像和文本，使其能够执行零-shot 图像分类等任务。而 DALL-E 则根据文本描述生成图像，允许在游戏、广告和文学等领域自动化和增强创意过程。

视觉语言模型（VLMs）是多模态模型的一种特殊形式。VLMs 根据视觉输入生成语言。一个显著的例子是 PaliGemma，Google 于 2024 年 5 月推出。PaliGemma 可用于视觉问答、物体检测和图像分割。

一些博客文章探讨了 PaliGemma 在物体检测中的应用，比如 Roboflow 的这篇精彩文章：

[](https://blog.roboflow.com/how-to-fine-tune-paligemma/?source=post_page-----b172dc0cf55d--------------------------------) [## 使用自定义数据微调 PaliGemma 进行物体检测

### 学习如何微调 PaliGemma 多模态模型以检测自定义物体。

[blog.roboflow.com](https://blog.roboflow.com/how-to-fine-tune-paligemma/?source=post_page-----b172dc0cf55d--------------------------------)

然而，在我写这篇博客时，关于如何准备数据以使用 Paligemma 进行对象分割的现有文档非常模糊。这就是为什么我想评估使用 Paligemma 完成这个任务是否容易。在这里，我分享我的经验。

# Paligemma 简介

在详细讨论用例之前，让我们简要回顾一下 Paligemma 的内部工作原理。

![](../Images/e3f8adc404fa7c8dd20c873399e6032a.png)

Paligemma2 的架构。来源：[https://arxiv.org/abs/2412.03555](https://arxiv.org/abs/2412.03555)

Paligemma 将 [SigLIP-So400m 视觉编码器](https://arxiv.org/abs/2303.15343) 与 [Gemma 语言模型](https://developers.googleblog.com/en/gemma-explained-overview-gemma-model-family-architectures/) 结合，处理图像和文本（见上图）。在今年12月发布的 Paligemma 新版本中，视觉编码器可以以三种不同的分辨率对图像进行预处理：224px、448px 或 896px。视觉编码器对图像进行预处理并输出一系列图像标记，这些标记与输入文本标记线性结合。这个标记组合将被 Gemma 语言模型进一步处理，最终输出文本标记。Gemma 模型有不同的大小，从 2B 到 27B 参数不等。

以下图所示是模型输出的示例。

![](../Images/bb5b62fcee9bc621f472c12acb4655ca.png)

对象分割输出的示例。来源：[https://arxiv.org/abs/2412.03555](https://arxiv.org/abs/2412.03555)

Paligemma 模型在多个数据集上进行了训练，如 [WebLi](https://paperswithcode.com/dataset/webli)、[openImages](https://storage.googleapis.com/openimages/web/index.html)、[WIT](https://github.com/google-research-datasets/wit) 等（有关更多详情，请参见这篇 [Kaggle 博客](https://www.kaggle.com/models/google/paligemma)）。这意味着 Paligemma 能够在没有微调的情况下识别物体。然而，这种能力是有限的。因此，Google 推荐在特定领域的用例中对 Paligemma 进行微调。

## 输入格式

要微调 Paligemma，输入数据需要为 JSONL 格式。JSONL 格式的数据集中的每一行都是一个单独的 JSON 对象，就像一个个体记录的列表。每个 JSON 对象包含以下键：

**图像：** 图像的名称。

**前缀：** 这指定了您希望模型执行的任务。

**后缀：** 这是提供给模型学习的真实标签，用以进行预测。

根据任务的不同，您必须相应地更改 JSON 对象的前缀和后缀。以下是一些示例：

+   **图像标注：**

```py
{"image": "some_filename.png", 
 "prefix": "caption en" (To indicate that the model should generate an English caption for an image),
 "suffix": "This is an image of a big, white boat traveling in the ocean."
}
```

+   **问答：**

```py
{"image": "another_filename.jpg", 
 "prefix": "How many people are in the image?",
 "suffix": "ten"
}
```

+   **物体检测：**

```py
{"image": "filename.jpeg", 
 "prefix": "detect airplane",
 "suffix": "<loc0055><loc0115><loc1023><loc1023> airplane" (four corner bounding box coords)
}
```

如果您有多个类别需要检测，请在每个类别的前缀和后缀之间添加分号（;）。

关于如何为 Paligemma 准备物体检测数据的完整清晰解释，请参见 [这篇 Roboflow 文章](https://blog.roboflow.com/how-to-fine-tune-paligemma/)。

+   **图像分割：**

```py
{"image": "filename.jpeg", 
 "prefix": "detect airplane",
 "suffix": "<loc0055><loc0115><loc1023><loc1023><seg063><seg108><seg045><seg028><seg056><seg052><seg114><seg005><seg042><seg023><seg084><seg064><seg086><seg077><seg090><seg054> airplane" 
}
```

请注意，对于分割，除了对象的边界框坐标外，还需要指定16个额外的分割令牌，代表适合在边界框内的掩码。根据[谷歌的Big Vision库](https://github.com/google-research/big_vision/blob/main/big_vision/configs/proj/paligemma/README.md#tokenizer)，这些令牌是包含128个条目的代码词（<seg000>…<seg127>）。我们如何获取这些值？根据我的个人经验，在没有适当文档的情况下，获取它们非常具有挑战性和令人沮丧。不过，我稍后会提供更多细节。

如果你有兴趣了解更多关于PaliGemma的信息，我推荐以下博客：

[](https://huggingface.co/blog/paligemma2?source=post_page-----b172dc0cf55d--------------------------------) [## 欢迎使用PaliGemma 2 —— 谷歌的新视觉语言模型

### 我们正在通过开源和开放科学推动和普及人工智能的发展。

huggingface.co](https://huggingface.co/blog/paligemma2?source=post_page-----b172dc0cf55d--------------------------------) [](https://www.datature.io/blog/introducing-paligemma-googles-latest-visual-language-model?source=post_page-----b172dc0cf55d--------------------------------) [## 介绍PaliGemma：谷歌最新的视觉语言模型

### PaliGemma通过任务特定的微调推动了视觉语言模型在高效多模态方面的边界……

[www.datature.io](https://www.datature.io/blog/introducing-paligemma-googles-latest-visual-language-model?source=post_page-----b172dc0cf55d--------------------------------)

# 水体的卫星图像

如上所述，PaliGemma是在不同数据集上进行训练的。因此，预计该模型擅长分割“传统”物体，如汽车、人或动物。但在卫星图像中分割物体怎么样呢？这个问题促使我探索PaliGemma在卫星图像中分割水体的能力。

Kaggle的[水体卫星图像数据集](https://www.kaggle.com/datasets/franciscoescobar/satellite-images-of-water-bodies)非常适合此用途。该数据集包含2841张图像及其对应的掩码。

![](../Images/ad8e9458fd86cd6d05b75a1c60a1563f.png)

这是水体数据集的一个示例：左侧是RGB图像，右侧是相应的掩码。

数据集中的一些掩码是错误的，其他一些则需要进一步的预处理。错误的示例包括将所有值都设为水的掩码，而原始图像中只存在一小部分水域。还有些掩码与其RGB图像不对应。当图像旋转时，某些掩码会让这些区域看起来像有水。

![](../Images/b74083bafddf71b84cdbbc2f3cfd00f1.png)

旋转掩码的示例。当在Python中读取此图像时，图像外部区域会显示为有水的样子。在这种情况下，需要旋转图像来纠正掩码。图像由作者制作。

鉴于这些数据的限制，我选择了164张图像样本，其中的掩膜没有上述提到的任何问题。这一组图像被用来微调Paligemma。

## 准备JSONL数据集

如前一节所述，Paligemma需要输入表示物体边界框坐标的标准化图像空间坐标（<loc0000>…<loc1023>），以及额外的16个分割标记，代表128个不同的代码词（<seg000>…<seg127>）。由于有了[Roboflow的解释](https://blog.roboflow.com/how-to-fine-tune-paligemma/)，获取所需格式的边界框坐标变得很容易。那么，我们如何从掩膜中获取128个代码词呢？在Big Vision代码库中没有明确的文档或示例可以用于我的使用场景。我天真地以为创建分割标记的过程与创建边界框相似。然而，这导致了水体掩膜的错误表示，从而导致了错误的预测结果。

到我写这篇博客时（12月初），Google宣布了Paligemma的第二版。在此事件之后，Roboflow发布了[一篇很好的概述](https://blog.roboflow.com/fine-tune-paligemma-2/)，介绍了如何准备数据来微调Paligemma2，以适应不同的应用场景，包括图像分割。我使用了他们代码的一部分，最终获得了正确的分割代码词。我的错误是什么呢？首先，掩膜需要调整大小为形状为[None, 64, 64, 1]的张量，然后使用预训练的变分自编码器（VAE）将标注掩膜转换为文本标签。尽管在Big Vision代码库中简要提到过VAE模型的使用，但并没有提供如何使用它的解释或示例。

我用来准备数据以微调Paligemma的工作流程如下所示：

![](../Images/31298a3389159effa1b9eff062b5b8cb.png)

将一个原始掩膜从过滤后的[水体数据集](https://www.kaggle.com/datasets/franciscoescobar/satellite-images-of-water-bodies)转换为JSON对象的步骤。这个过程在训练集中的164张图像和测试集中的21张图像上重复，以构建JSONL数据集。

如观察所见，准备数据以适应Paligemma所需的步骤较多，因此我没有在这里分享代码片段。然而，如果你想探索代码，可以访问[这个GitHub仓库](https://github.com/anamabo/SegmentWaterWithPaligemma)。脚本*convert.py*包含了上述工作流程中的所有步骤。我还添加了选定的图像，方便你立即使用这个脚本进行操作。

在将分割代码词转换回分割掩膜的预处理过程中，我们注意到这些掩膜如何覆盖图像中的水体：

![](../Images/21e69594fd0053603c25be1b6e07aaba.png)

在训练集上解码分割代码词时生成的掩膜。此图由作者使用[此 Notebook](https://github.com/anamabo/SegmentWaterWithPaligemma/blob/main/finetune_paligemma_for_segmentation.ipynb)制作。

# Paligemma 在分割卫星图像中的水体表现如何？

在微调 Paligemma 之前，我尝试了它在 Hugging Face 上上传的模型的分割功能。该平台有一个[演示](https://huggingface.co/spaces/big-vision/paligemma)，你可以上传图片并与不同的 Paligemma 模型互动。

![](../Images/13a159f2e23495fa1218fc60692ec45e.png)

默认的 Paligemma 模型在分割卫星图像中的水体方面表现。

当前版本的 Paligemma 通常能较好地分割卫星图像中的水体，但它并不完美。让我们看看是否能改进这些结果！

有两种方法可以微调 Paligemma，分别是通过 [Hugging Face 的 Transformer 库](https://huggingface.co/blog/paligemma#using-transformers-1) 或使用 Big Vision 和 JAX。我选择了后一种方法。Big Vision 提供了一个 [Colab notebook](https://colab.research.google.com/github/google-research/big_vision/blob/main/big_vision/configs/proj/paligemma/finetune_paligemma.ipynb)，我根据自己的使用需求进行了修改。你可以通过访问我的[GitHub 仓库](https://github.com/anamabo/SegmentWaterWithPaligemma?tab=readme-ov-file)来打开它：

[](https://github.com/anamabo/SegmentWaterWithPaligemma/blob/main/finetune_paligemma_for_segmentation.ipynb?source=post_page-----b172dc0cf55d--------------------------------) [## SegmentWaterWithPaligemma/finetune_paligemma_for_segmentation.ipynb 位于主分支 ·…

### 使用 Paligemma 进行卫星图像中水体的分割……

github.com](https://github.com/anamabo/SegmentWaterWithPaligemma/blob/main/finetune_paligemma_for_segmentation.ipynb?source=post_page-----b172dc0cf55d--------------------------------)

我使用了 *batch size* 为 8，*learning rate* 为 0.003。我运行了两次训练循环，总共进行了 158 步训练。在 T4 GPU 机器上，整个运行时间为 24 分钟。

结果并未如预期那样。Paligemma 在一些图像中没有生成预测结果，在其他图像中，生成的掩膜与真实值相差甚远。我还在两张图像中获得了超过 16 个标记的分割代码词。

![](../Images/f9ba415d9a14a051d7c5168dee08e1d6.png)

微调后的结果中有预测的情况。此图由作者制作。

值得一提的是，我使用的是第一版 Paligemma。也许使用 Paligemma2，或者进一步调整批次大小或学习率，结果会有所改进。无论如何，这些实验超出了本文的讨论范围。

演示结果显示，默认的 Paligemma 模型在分割水体方面优于我微调过的模型。在我看来，如果目标是构建一个专门用于分割物体的模型，那么 UNET 是一个更好的架构。如需了解如何训练此类模型的更多信息，可以阅读我之前的博客文章：

[](/detecting-clouds-with-ai-b553e6576af6?source=post_page-----b172dc0cf55d--------------------------------) [## 使用AI检测云层

### 从随机森林到YOLO：比较不同算法在卫星图像中进行云层分割的效果。

[towardsdatascience.com](/detecting-clouds-with-ai-b553e6576af6?source=post_page-----b172dc0cf55d--------------------------------)

## 其他限制：

我想提一下在使用Big Vision和JAX微调Paligemma时遇到的其他一些挑战。

+   设置不同的模型配置是困难的，因为关于这些参数的文档仍然很少。

+   Paligemma的第一个版本已被训练处理不同长宽比的图像，并将其调整为224x224大小。请确保只将输入图像调整为此大小，这将防止引发异常。

+   在使用Big Vision和JAX进行微调时，你可能会遇到与JAX GPU相关的问题。克服此问题的方法有：

a. 减少训练集和验证集中的样本数量。

b. 将批量大小从8增加到16或更高。

+   微调后的模型大小约为5GB。确保你的驱动器有足够的空间来存储它。

# 重点信息

发现一个新的AI模型令人兴奋，尤其是在这个多模态算法正在转变我们社会的时代。然而，由于缺乏可用的文档，与最先进的模型合作有时会变得具有挑战性。因此，新AI模型的发布应伴随全面的文档，以确保其顺利且广泛的应用，特别是在那些仍然缺乏经验的专业人士中。

尽管我在微调Paligemma时遇到了困难，但当前的预训练模型在进行零-shot目标检测和图像分割时非常强大，可以应用于许多场景，包括辅助机器学习标注。

你在计算机视觉项目中使用Paligemma吗？在评论中分享你在微调该模型时的经验！

希望你喜欢这篇文章，再次感谢阅读！

你可以通过LinkedIn与我联系，地址是：

[https://www.linkedin.com/in/camartinezbarbosa/](https://www.linkedin.com/in/camartinezbarbosa/)

*致谢：我要感谢José Celis-Gil在数据预处理和建模方面的所有富有成果的讨论。*
