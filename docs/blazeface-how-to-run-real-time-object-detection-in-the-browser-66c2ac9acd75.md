# BlazeFace：如何在浏览器中运行实时目标检测

> 原文：[https://towardsdatascience.com/blazeface-how-to-run-real-time-object-detection-in-the-browser-66c2ac9acd75?source=collection_archive---------2-----------------------#2024-07-17](https://towardsdatascience.com/blazeface-how-to-run-real-time-object-detection-in-the-browser-66c2ac9acd75?source=collection_archive---------2-----------------------#2024-07-17)

## 这是一份逐步指南，介绍如何训练BlazeFace模型，从Python训练管道到通过模型转换实现JavaScript演示。

[](https://medium.com/@vincent.vandenbussche?source=post_page---byline--66c2ac9acd75--------------------------------)[![Vincent Vandenbussche](../Images/b2febfc63ca0efbda0af5501f6080ab7.png)](https://medium.com/@vincent.vandenbussche?source=post_page---byline--66c2ac9acd75--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--66c2ac9acd75--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--66c2ac9acd75--------------------------------) [Vincent Vandenbussche](https://medium.com/@vincent.vandenbussche?source=post_page---byline--66c2ac9acd75--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--66c2ac9acd75--------------------------------) ·11分钟阅读·2024年7月17日

--

![](../Images/05b105d691b1a727b04e20b7e15caa57.png)

参考自[visuals](https://unsplash.com/@visuals?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)在[Unsplash](https://unsplash.com/photos/man-in-black-suit-jacket-smiling-Y4qzW3AsvqI?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)上的一张照片。

得益于[YOLO by Ultralytics](https://docs.ultralytics.com/)等库，今天我们可以通过几行代码轻松地创建稳健的目标检测模型。遗憾的是，这些解决方案在30帧每秒的视频实时流中，在任何设备上都不够快（通常认为30帧每秒是视频应用的实时限制）。在大多数情况下，它们在普通的移动设备上运行时，帧率通常低于10帧每秒。

目前在浏览器中最著名的实时目标检测解决方案是[Google的MediaPipe](https://ai.google.dev/edge/mediapipe/solutions/vision/object_detector)。这是一个非常方便且多功能的解决方案，可以轻松地在许多设备和平台上运行。但如果你想制作自己的解决方案呢？

在这篇文章中，我们提出构建一个自己的轻量级、快速且稳健的目标检测模型，基于BlazeFace模型，该模型在几乎所有设备上以超过30帧每秒的速度运行。所有用于此的代码都可以在我的[GitHub](https://github.com/vincent-vdb/medium_posts/tree/main/blazeface)上的*blazeface*文件夹中找到。

[BlazeFace](https://arxiv.org/abs/1907.05047)模型由 Google 提出，最初用于 MediaPipe 中的人脸检测，体积小且速度快，同时对人脸检测等简单物体检测任务具有足够的鲁棒性。不幸的是，据我所知，GitHub 上没有该模型的训练流程；我所能找到的只有[这个仅用于推理的模型架构](https://github.com/hollance/BlazeFace-PyTorch)。通过本文，我们将训练我们自己的 BlazeFace 模型，构建一个完整的工作流程，并使用能够运行 JavaScript 代码的浏览器。

更具体地说，我们将经过以下步骤：

+   使用 [PyTorch](https://pytorch.org/) 训练模型

+   将 PyTorch 模型转换为 TFLite 模型

+   在浏览器中运行物体检测，得益于 JavaScript 和 TensorFlow.js

让我们开始模型训练吧。

# 训练 PyTorch 模型

和往常一样，在训练模型时，训练流程中有几个典型的步骤：

+   数据预处理：为了简便，我们将使用一个公开可用的 Kaggle 数据集，但任何格式正确的标签数据集都可以使用

+   构建模型：我们将复用原论文中提出的架构和仅用于推理的 GitHub 代码

+   训练和评估模型：我们将使用一个简单的 Multibox 损失作为最小化的代价函数

让我们一起走过这些步骤。

## 数据预处理

我们将使用 Google 提出的 [Open Images Dataset](https://storage.googleapis.com/openimages/web/index.html) V7 数据集的一个子集。该数据集包含大约 900 万张图像，附有许多注释（包括边界框、分割掩膜等）。该数据集本身相当庞大，包含多种类型的图像。

对于我们的具体用例，我决定选择验证集中的图像，并满足两个特定条件：

+   包含人脸边界框标签

+   拥有适用于此类用例的宽松许可证，具体来说是[CC BY 2.0](https://creativecommons.org/licenses/by/2.0/)许可证

用于在这些严格条件下下载和构建数据集的脚本已提供在 GitHub 上，任何人都可以复现。通过这个脚本下载的数据集包含 YOLO 格式的标签（即框的中心、宽度和高度）。最终，下载的数据集由大约 3000 张图像和 8000 个面孔组成，我已将其分为训练集和验证集，比例为 80%-20%。

在这个数据集中，在能够训练模型之前，通常需要进行以下预处理。以下是我使用的数据预处理代码：

用于 PyTorch 模型训练的数据预处理类。为了简洁，部分代码已被省略：完整代码可在 GitHub 上找到。

如我们所见，数据预处理包括以下几个步骤：

+   它加载图像和标签

+   它将标签从 YOLO 格式（中心位置、宽度、高度）转换为框角格式（左上角位置、右下角位置）

+   它将图像调整为目标尺寸（例如 128 像素），如果需要，添加填充以保持原始图像的纵横比，并避免图像变形。最后，它会对图像进行归一化处理。

可选地，这段代码允许使用 [Albumentations](https://albumentations.ai/) 进行数据增强。在训练过程中，我使用了以下数据增强技术：

+   水平翻转

+   随机亮度对比度

+   从边界随机裁剪

+   仿射变换

这些增强操作将使我们拥有一个更强健、更具正则化的模型。经过这些转换和增强处理后，输入数据可能会呈现出以下样本：

![](../Images/f180793b42b7b04702cec440680ad18f.png)

经过数据增强的预处理图像，用于训练模型。图像由作者制作，素材来自 [Open Images 数据集](https://storage.googleapis.com/openimages/web/download_v7.html)。

如我们所见，经过预处理的图像由于增强（如旋转或平移）或填充（因为原始图像不是方形纵横比）而具有灰色边框。所有这些图像都包含面部，尽管背景可能因图像而异。

***重要提示:***

*人脸检测是一个高度敏感的任务，涉及重大的伦理和安全问题。数据集中的偏差，如某些面部特征的代表性不足或过度代表，可能导致假阴性或假阳性，从而可能造成伤害或冒犯。请参阅下文有关伦理考虑的专门章节。*

现在我们的数据已经可以加载和预处理了，接下来我们进入下一步：构建模型。

## 模型构建

在这一部分，我们将根据原始文章并从 [BlazeFace 仓库](https://github.com/hollance/BlazeFace-PyTorch)（仅包含推理代码）改编，构建原始 BlazeFace 模型的架构。

整个 BlazeFace 架构相当简单，主要由论文作者称之为 BlazeBlock 的结构组成，并且包含不同的参数。

BlazeBlock 可以通过 PyTorch 这样定义：

BlazeBlock 的实现，BlazeFace 由其构成。完整代码可在 GitHub 上获取。

从这段代码中我们可以看到，BlazeBlock 由以下几个层组成：

+   一个深度卷积 2D 层

+   一个批归一化 2D 层

+   一个 2D 卷积层

+   一个批归一化 2D 层

*注意：你可以阅读 PyTorch 文档了解更多关于这些层的信息：* [*Conv2D 层*](https://pytorch.org/docs/stable/generated/torch.nn.Conv2d.html) *和* [*BatchNorm2D 层*](https://pytorch.org/docs/stable/generated/torch.nn.BatchNorm2d.html)*.*

这个模块会被多次重复，使用不同的输入参数，将 128 像素的图像处理到最终阶段的典型物体检测预测中，最终通过张量重塑来完成。欢迎查看 GitHub 仓库中的完整代码，了解该架构的实现。

在进入关于训练模型的下一部分之前，请注意，实际上有两种架构：

+   128像素输入图像架构

+   256像素输入图像架构

正如你所想，256像素的架构略大，但仍然轻量且有时更具鲁棒性。该架构也已在提供的代码中实现，因此如果需要，你可以使用它。

*注意：原始BlazeFace模型不仅预测边界框，还预测六个大致的人脸关键点。由于我没有此类标签，我简化了模型架构，仅预测边界框。*

现在我们可以构建一个模型了，接下来让我们进入下一步：训练模型。

## 模型训练

对于熟悉PyTorch的人来说，训练这样的模型通常非常简单直接，如这段代码所示：

用于训练BlazeFace模型的代码。完整代码可在GitHub上获取。

正如我们所看到的，关键是对数据进行多次循环，每次一个批次，重复进行以下操作：

+   获取处理后的数据和相应的标签。

+   执行前向推理

+   计算推理结果与标签之间的损失

+   更新权重

为了保持文章的清晰度，我不会进入所有的细节，但如果需要，你可以通过浏览代码更好地了解训练部分。

在经过100个epoch的训练后，我在验证集上得到了以下结果：

![](../Images/47ff2c73041c624853d570fdc4a90a0e.png)

模型在验证集上经过50个epoch后的结果。绿色框是实际标签，红色框是模型预测。图像由作者提供，图片来源于[开放图像数据集](https://storage.googleapis.com/openimages/web/download_v7.html)。

正如我们在这些结果中看到的，即使物体检测不是完美的，它在大多数情况下表现得相当不错（可能是IoU阈值不理想，导致有时出现重叠框）。请记住，这是一个非常轻量的模型，它不能像YOLOv8那样展示相同的性能。

在进入关于转换模型的下一步之前，让我们简短地讨论一下伦理和安全的考量。

## 伦理与安全考虑

让我们讨论一下有关伦理和安全的几个要点，因为人脸检测可能是一个非常敏感的话题：

+   **数据集的重要性与选择：** 该数据集用于展示人脸检测技术，旨在教育目的。它因与主题的相关性而被选择，但可能并未充分代表无偏结果所需的多样性。

+   **偏差意识：** 数据集并未声称没有偏差，潜在的偏差尚未完全消除。请注意，潜在的偏差可能会影响人脸检测模型的准确性和公平性。

+   **风险：** 训练后的人脸检测模型可能会反映这些偏差，从而引发潜在的伦理问题。用户应批判性地评估结果，并考虑更广泛的影响。

为了应对这些问题，任何希望在这一领域构建产品的人都应该关注：

+   收集多样化和具有代表性的图像

+   确保数据没有偏差，并且每个类别都得到平等代表

+   持续评估人脸检测技术的伦理影响

*注意：一个有用的方式是查看Google在其自己的* [*人脸检测*](https://storage.googleapis.com/mediapipe-assets/MediaPipe%20BlazeFace%20Model%20Card%20(Short%20Range).pdf) *和* [*人脸关键点*](https://storage.googleapis.com/mediapipe-assets/Model%20Card%20MediaPipe%20Face%20Mesh%20V2.pdf) *模型上所做的工作。*

再次强调，使用的数据集仅用于教育目的。任何希望使用该数据集的人都应谨慎，并在解释结果时考虑其局限性。现在我们可以进入下一步，即模型转换。

# 模型转换

记住，我们的目标是使我们的目标检测模型在Web浏览器中工作。不幸的是，一旦我们训练好了PyTorch模型，就无法直接在浏览器中使用它。我们需要首先将其转换。

目前，据我所知，在Web浏览器中运行深度学习模型的最可靠方法是使用[TFLite](https://www.tensorflow.org/lite)模型与[TensorFlow.js](https://www.tensorflow.org/js)。换句话说，我们需要将PyTorch模型转换为TFLite模型。

*注意：一些替代方案正在出现，例如* [*ExecuTorch*](https://pytorch.org/executorch-overview)*，但它们似乎还不够成熟，无法用于Web。*

据我所知，目前没有直接可靠的方式来实现这一点。但有一些间接的方法，可以通过ONNX来实现。[ONNX](https://onnx.ai/)（即开放神经网络交换）是一个用于存储和运行（使用[ONNX Runtime](https://onnxruntime.ai/)）机器学习模型的标准。方便的是，已经有了从Torch到ONNX的转换库，以及从ONNX到TensorFlow模型的转换库。

总结来说，转换工作流程由以下三个步骤组成：

+   从PyTorch转换为ONNX

+   从ONNX转换为TensorFlow

+   从TensorFlow转换为TFLite

这正是以下代码所做的事情：

将PyTorch格式的模型转换为TFLite格式，通过ONNX。完整代码可在GitHub上获取。

这段代码可能比之前的代码稍显复杂，因为有一些特定的优化和参数用于确保其正常运行。你也可以尝试更进一步，对TFLite模型进行量化，使其更小。如果你有兴趣，可以查看[官方文档](https://www.tensorflow.org/lite/performance/post_training_quantization)。

*注意：转换代码对库的版本非常敏感。为了确保顺利转换，我强烈建议使用GitHub上的requirements.txt文件中指定的版本。*

在我这边，经过TFLite转换后，我终于得到了一个仅约400kB的TFLite模型，体积小巧，非常适合网页使用。下一步是实际在网页浏览器中进行测试，并确保它按预期工作。

顺便提一下，Google目前正在开发另一种解决方案，用于将PyTorch模型转换为TFLite格式：[AI Edge Torch](https://github.com/google-ai-edge/ai-edge-torch)。不幸的是，这个解决方案相当新，我没能使其在我的用例中工作。不过，任何关于这个库的反馈都非常欢迎。

# 运行模型

现在我们终于得到了一个TFLite模型，能够在网页浏览器中使用TensorFlow.js运行它。如果你不熟悉JavaScript（因为这通常不是数据科学家和机器学习工程师常用的语言），不用担心；所有代码都已提供，并且相当容易理解。

我不会在这里对所有代码进行注释，只注释最相关的部分。如果你查看[GitHub](https://github.com/vincent-vdb/medium_posts)上的代码，你会看到在*javascript*文件夹中有以下内容：

+   *index.html*：包含运行整个演示的主页

+   *assets*：包含我们刚刚转换的TFLite模型的文件夹

+   *js*：包含JavaScript代码的文件夹

如果我们退后一步来看，在JavaScript代码中我们需要做的就是遍历摄像头视频流的每一帧（无论是计算机上的网络摄像头还是手机上的前置摄像头），然后执行以下操作：

+   对图像进行预处理：将其调整为128像素的图像，进行填充和归一化

+   对预处理后的图像进行推理计算

+   对模型输出进行后处理：应用阈值化和非极大抑制来处理检测结果

我们不会评论图像预处理部分，因为这与Python预处理部分是重复的，但你可以随时查看代码。至于在JavaScript中进行TFLite模型推理，其实非常简单：

一个简单的代码示例，用于实例化TFLite模型并计算推理，假设图像形状正确。完整的可工作代码在GitHub上。

棘手的部分实际上是后处理。正如你所知，SSD目标检测模型的输出是不能直接使用的：这并不是边界框的位置。以下是我使用的后处理代码：

在JavaScript中对BlazeFace模型输出进行后处理。完整代码在GitHub上。

在上面的代码中，模型输出经过以下步骤的后处理：

+   使用锚点纠正框的位置

+   将框格式转换为获取左上角和右下角坐标

+   对带有检测分数的框应用非极大抑制，移除所有低于给定阈值的框，并去除与其他已存在框重叠的框

这正是Python中所做的操作，用于显示生成的边界框，如果这能帮助你更好地理解这一部分内容的话。

最后，以下是生成的网页浏览器演示的截图：

![](../Images/7531f025b2456d12dcd9b3b675f298ca.png)

网页浏览器中运行演示的截图，画中画由[Vitaly Gariev](https://unsplash.com/photos/a-man-sitting-at-a-table-in-front-of-a-laptop-wAUjHURVbDw)提供，图片来自Unsplash

如你所见，它正确地检测到了图像中的人脸。我决定使用一张来自[Unsplash的静态图片](https://unsplash.com/photos/a-man-sitting-at-a-table-in-front-of-a-laptop-wAUjHURVbDw)，但GitHub上的代码允许你在你的网络摄像头上运行它，因此请随时自己测试。

在总结之前，请注意，如果你在自己的电脑或智能手机上运行此代码，具体取决于你的设备，你可能无法达到30帧每秒（在我个人的笔记本电脑上，搭载的是一款较旧的2017年[Intel® Core™ i5–8250U](https://www.intel.fr/content/www/fr/fr/products/sku/124967/intel-core-i58250u-processor-6m-cache-up-to-3-40-ghz/specifications.html)，它的运行速度为36fps）。如果是这种情况，一些技巧可能会帮助你达到目标。最简单的办法是每N帧运行一次模型推理（N的值可以根据你的应用场景进行微调）。实际上，在大多数情况下，从一帧到下一帧变化不大，框的位置几乎可以保持不变。

# 结论

希望你喜欢阅读这篇文章，如果你看到这里，非常感谢。尽管现在做物体检测相对简单，但在资源有限的情况下做物体检测仍然非常具有挑战性。了解BlazeFace并将模型转换为网页浏览器应用，可以深入了解MediaPipe是如何构建的，并为其他有趣的应用打开了大门，例如在视频通话中实时模糊背景（如Google Meet或Microsoft Teams）。

# 参考文献

+   [Open Images数据集的发布](https://arxiv.org/abs/1811.00982)

+   [GitHub仓库](https://github.com/vincent-vdb/medium_posts)，其中包含在blazeface文件夹中的所有工作代码

+   [包含BlazeFace推理代码的原始GitHub](https://github.com/hollance/BlazeFace-PyTorch/blob/master/blazeface.py)

+   [BlazeFace论文](https://arxiv.org/abs/1907.05047)

+   [MediaPipe的人脸检测](https://ai.google.dev/edge/mediapipe/solutions/vision/face_detector) 和 [BlazeFace模型卡片](https://storage.googleapis.com/mediapipe-assets/MediaPipe%20BlazeFace%20Model%20Card%20(Short%20Range).pdf)

+   [ONNX](https://onnx.ai/) 和 [ONNX Runtime](https://onnxruntime.ai/)

+   [TFLite量化](https://www.tensorflow.org/lite/performance/post_training_quantization)
