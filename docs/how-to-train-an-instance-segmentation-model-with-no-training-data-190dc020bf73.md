# 如何在没有训练数据的情况下训练实例分割模型

> 原文：[https://towardsdatascience.com/how-to-train-an-instance-segmentation-model-with-no-training-data-190dc020bf73?source=collection_archive---------10-----------------------#2024-01-29](https://towardsdatascience.com/how-to-train-an-instance-segmentation-model-with-no-training-data-190dc020bf73?source=collection_archive---------10-----------------------#2024-01-29)

## 你只需要一点计算能力

[](https://medium.com/@vincent.vandenbussche?source=post_page---byline--190dc020bf73--------------------------------)[![Vincent Vandenbussche](../Images/b2febfc63ca0efbda0af5501f6080ab7.png)](https://medium.com/@vincent.vandenbussche?source=post_page---byline--190dc020bf73--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--190dc020bf73--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--190dc020bf73--------------------------------) [Vincent Vandenbussche](https://medium.com/@vincent.vandenbussche?source=post_page---byline--190dc020bf73--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--190dc020bf73--------------------------------) ·8分钟阅读·2024年1月29日

--

![](../Images/f3c91d0c17e4991f49a48ae9d09eb9f5.png)

图片来源：[Izzy Park](https://unsplash.com/@blue_jean?utm_source=medium&utm_medium=referral) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

你知道吗，对于大多数常见的物品类型，你不再一定需要数据来训练物体检测甚至是实例分割模型？

让我们以一个实际的例子为例。假设你被要求为以下类别构建一个实例分割模型：

+   狮子

+   马

+   斑马

+   老虎

可以说，类似的类别数据很容易找到：互联网上有大量这些动物的图像。然而，如果我们需要为实例分割构建一个具有商业可行性的产品，我们仍然需要两样东西：

+   确保我们收集到的图像具有商业使用许可

+   标注数据

这两项任务可能非常耗时和/或需要花费相当大的金额。

让我们探索另一条路径：使用免费且可用的模型。为此，我们将采用一个两步过程来生成数据及其相关标签：

+   首先，我们将使用[稳定扩散](https://en.wikipedia.org/wiki/Stable_Diffusion)，一个非常强大的生成型AI模型，来生成图像

+   然后，我们将使用Meta的[Segment Anything Model](https://segment-anything.com/)（SAM）生成并策划标签

*请注意，在本文发布之时，使用稳定扩散生成的图像处于灰色地带，且可以用于商业用途。但相关规定未来可能会发生变化。*

本文中使用的所有代码都可以在[这个仓库](https://github.com/vincent-vdb/medium_posts)中找到。

# 使用 Stable Diffusion 生成数据

我使用 Stable Diffusion 生成了数据。在实际生成数据之前，先简要介绍一下 Stable Diffusion 以及如何使用它。

## 如何使用 Stable Diffusion

为此，我使用了以下仓库：[https://github.com/AUTOMATIC1111/stable-diffusion-webui](https://github.com/AUTOMATIC1111/stable-diffusion-webui)

它功能非常完整且经常更新，允许使用大量工具和插件。按照 readme 中的说明，任何发行版都可以非常容易地安装。你还可以找到一些非常有用的教程，教你如何有效地使用 Stable Diffusion：

+   对于初学者，我建议查看[这个教程](https://stable-diffusion-art.com/beginners-guide/)

+   对于更高级的用法，我建议查看[这个教程](https://stable-diffusion-art.com/prompt-guide/)

不深入讨论 Stable Diffusion 模型的训练和工作原理（这方面有很多优秀的资源），但值得知道的是，实际上有不止一个模型。

有几个由 Stability AI 发布的“官方”版本模型，例如 Stable Diffusion 1.5、2.1 或 XL。这些官方模型可以轻松地在[Stability AI 的 HuggingFace](https://huggingface.co/stabilityai)上下载。

但由于 Stable Diffusion 是开源的，任何人都可以训练自己的模型。在网站[Civitai](https://civitai.com/models/)上有大量可用的模型，有时是为特定目的训练的，比如幻想图像、朋克风格图像或真实图像。

## 生成数据

对于我们的需求，我将使用两个模型，其中一个是专门为真实图像生成训练的，因为我想生成真实的动物图像。

使用的模型和超参数如下：

+   模型：[JuggernautXL](https://civitai.com/models/133005/juggernaut-xl)和[Realistic Vision V6.0 B1](https://civitai.com/models/4201/realistic-vision-v60-b1)

+   采样：Euler a，20次迭代

+   CFG 规模：2（数值越低，产生的输出越随机）

+   负面提示：“*差质量、差解剖学、最差质量、低质量、低分辨率、模糊、模糊不清、丑陋、比例错误、水印、图像伪影、低分辨率、丑陋、JPEG伪影、变形、噪点图像、形变、数字艺术、不真实、绘画、油画*”

+   提示：“*一只坐在草地上的狮子的真实照片*”

为了自动化生成不同设置的图像，我使用了一个名为 X/Y/Z 图与提示 S/R 的特性脚本，适用于每个轴。

“提示 S/R”指的是搜索和替换，允许在原始提示中搜索一个字符串并将其替换为其他字符串。通过在每个轴上使用 X/Y/Z 图和提示 S/R，可以生成任何可能给定值组合的图像（就像超参数网格搜索一样）。

以下是我在每个轴上使用的参数：

+   *狮子，斑马，老虎，马*

+   *坐着，睡觉，站立，跑步，走路*

+   *在草地上，在野外，在城市中，在丛林里，从背后，从侧面看*

使用这个方法，我可以一次性轻松生成如下提示的图像：“一张真实的<动物> <动作> <位置>”的图片，所有参数值都由此生成。

总的来说，它会为4种动物、5个动作和6个位置生成图像：所以一共有120种可能性。再加上我使用了批量计数为2和2个不同的模型，使得生成的图像数量增加到480，从而创建了我的数据集（每个动物类别120个）。下面是一些生成图像的示例。

![](../Images/54cbcd00b8d086177b3e4aaf3705e836.png)

使用Stable Diffusion生成的图像示例。图像由作者提供。

正如我们所看到的，大多数图片都足够真实。接下来，我们将获取实例遮罩，以便训练一个分割模型。

# 获取标签

为了获取标签，我们将使用SAM模型生成遮罩，然后手动过滤掉不够好的遮罩，以及不现实的图像（通常称为幻觉）。

## 生成原始遮罩

为了生成原始遮罩，我们将使用SAM模型。SAM模型需要输入提示（不是文本提示）：可以是一个边界框或一些点位。这允许模型根据这些输入提示生成遮罩。

在我们的情况下，我们将使用最简单的输入提示：中心点。事实上，在Stable Diffusion生成的大多数图像中，主要对象都是居中的，这使得我们可以高效地使用SAM，始终使用相同的输入提示，并且完全不需要标注。为此，我们使用以下功能：

使用SAM生成遮罩的功能。完整代码可以在代码库中找到。

这个功能将首先实例化一个SAM预测器，给定模型类型和检查点（可以在[这里](https://github.com/facebookresearch/segment-anything#model-checkpoints)下载）。然后，它将遍历输入文件夹中的图像，进行以下操作：

+   加载图像

+   利用SAM计算遮罩，使用*multimask_output*设置为*True*和*False*的两种选项。

+   在将遮罩写成图像之前，先对其应用闭运算。

需要注意几点：

+   我们同时使用*multimask_output*设置为*True*和*False*的两种选项，因为没有哪种选项能 consistently 提供更优的结果。

+   我们对遮罩应用闭运算，因为原始遮罩有时会有一些小孔。

以下是一些带有遮罩的图像示例：

![](../Images/4495d22c8066eb188e3a74e1a7fc8508.png)

一些图像，显示了生成的SAM遮罩，作为黄色叠加层。图像由作者提供。

正如我们所看到的，一旦选择了，遮罩非常准确，几乎不需要时间来标注。

## 选择遮罩

不是所有的遮罩在上一小节中都正确计算出来。事实上，有时对象并没有居中，因此遮罩预测不准确。有时，出于某种原因，遮罩就是错误的，需要更多的输入提示来使其生效。

一种简单的解决方法是直接选择两个计算出的掩码中最好的一个，或者如果没有合适的掩码，直接将图片从数据集中删除。我们可以通过以下代码来实现：

用于选择最佳掩码，或直接拒绝图片的函数。完整代码在代码库中可用。

这段代码会遍历所有使用Stable Diffusion生成的图片，并对每张图片执行以下操作：

+   加载两个生成的SAM掩码

+   将图片显示两次，每张图片分别叠加一个掩码，并并排显示

+   等待键盘事件以进行选择

预期的键盘事件如下：

+   使用键盘的左箭头选择左侧掩码

+   使用右箭头选择左侧掩码

+   向下箭头以丢弃这张图片

运行这个脚本可能需要一些时间，因为你必须处理所有图片。假设每张图片处理时间为1秒，对于600张图片，大约需要10分钟。这仍然比实际为每张图片标注掩码要快得多，后者通常需要至少30秒才能为每个高质量掩码标注。此外，这还能够有效地筛选出任何不真实的图片。

运行这个脚本处理生成的480张图片，我只用了不到5分钟。我选择了合适的掩码并过滤了不真实的图片，最终得到了412张掩码。下一步是训练模型。

# 训练模型

在训练YOLO分割模型之前，我们需要正确创建数据集。让我们逐步完成这些步骤。

## 创建数据集

用于创建数据集的函数。完整代码在代码库中可用。

这段代码遍历所有图片，并执行以下操作：

+   随机选择训练集或验证集

+   将掩码转换为多边形，以适应YOLO期望的输入标签

+   将图片和标签复制到正确的文件夹中

这段代码中的一个难点是在掩码到多边形的转换部分，这由*mask2yolo*函数完成。它利用了[shapely](https://pypi.org/project/shapely/)和[rasterio](https://pypi.org/project/rasterio/)库来高效地进行转换。当然，你可以在代码库中找到完整的实现。

最后，你将在*datasets*文件夹中看到以下结构：

![](../Images/5cd3f4d34f280df4c7fe5b85b946013b.png)

创建数据集后的文件夹结构。图片来源：作者。

这是使用YOLOv8库训练模型的预期结构：终于可以开始训练模型了！

## 训练模型

我们现在可以训练模型了。让我们使用一个YOLOv8 nano分割模型。训练模型只需要两行代码，使用[Ultralytics库](https://pypi.org/project/ultralytics/)，正如我们在下面的代码片段中看到的：

用于训练YOLO分割模型的函数。完整代码在代码库中可用。

在之前准备好的数据集上训练了15个周期后，结果如下：

![](../Images/c1df48de7b65b8e0337edb9843d485dc.png)

经过15个周期后，由YOLOv8库生成的结果。

如我们所见，指标相当高，mAP50–95接近1，表明性能良好。当然，由于数据集多样性相对有限，这些良好的表现很可能是由于某种程度上的过拟合所导致。

为了更为真实的评估，接下来我们将在几张真实图像上评估模型。

## 在真实数据上评估模型

我从[Unsplash](https://unsplash.com/)上获取了每个类别的一些图像，并在这些数据上测试了模型。结果如下：

![](../Images/fb175cb46b3fc0e02c2113040b890191.png)

来自Unsplash的真实图像上的分割和类别预测结果。

在这8张真实图像上，模型表现得相当不错：动物类别成功预测，且遮罩似乎相当准确。当然，为了正确评估该模型，我们需要一个合适的标注数据集图像和每个类别的分割遮罩。

# 结论

在完全没有图像和标签的情况下，我们可以为四个类别训练一个分割模型：马、狮子、老虎和斑马。为此，我们利用了三个令人惊叹的工具：

+   使用稳定扩散生成逼真的图像

+   使用SAM计算物体的准确遮罩

+   YOLOv8高效训练实例分割模型

虽然我们因为缺乏标注测试数据集而无法正确评估训练好的模型，但在一些图像上它看起来很有前景。请不要将这篇文章当作训练任何实例分割的独立方法，而应视为一种加速和提升你下一个项目性能的方法。根据我的经验，使用合成数据和像SAM这样的工具可以大大提高你在构建生产级计算机视觉模型时的生产力。

当然，所有的代码都可以在[这个仓库](https://github.com/vincent-vdb/medium_posts/tree/main)中找到，并希望能帮助你在下一个计算机视觉项目中！
