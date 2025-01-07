# 使用GroundingDino进行自动标注

> 原文：[https://towardsdatascience.com/automatic-labeling-of-object-detection-datasets-using-groundingdino-b66c486656fe?source=collection_archive---------3-----------------------#2024-02-06](https://towardsdatascience.com/automatic-labeling-of-object-detection-datasets-using-groundingdino-b66c486656fe?source=collection_archive---------3-----------------------#2024-02-06)

## 本文是一个实用指南，讲解如何使用GroundingDino算法标注物体检测数据集。包括代码。

[](https://medium.com/@lihigurarie?source=post_page---byline--b66c486656fe--------------------------------)[![Lihi Gur Arie, PhD](../Images/7a1eb30725a95159401c3672fa5f43ab.png)](https://medium.com/@lihigurarie?source=post_page---byline--b66c486656fe--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b66c486656fe--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b66c486656fe--------------------------------) [Lihi Gur Arie, PhD](https://medium.com/@lihigurarie?source=post_page---byline--b66c486656fe--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b66c486656fe--------------------------------) ·6分钟阅读·2024年2月6日

--

![](../Images/18f9e79654ab0663260df8535cdebe7f.png)

作者使用GroundingDino并输入“成熟番茄”提示进行标注。图像由[Markus Spiske](https://www.pexels.com/photo/green-and-red-oval-fruits-965740/)提供。

# 介绍

直到最近，物体检测模型执行的是特定任务，比如检测图像中的企鹅。然而，深度学习的最新进展催生了基础模型。这些模型是在庞大的数据集上以通用方式训练的大型模型，使它们能够适应各种任务。例如，像[CLIP](https://medium.com/towards-data-science/clip-creating-image-classifiers-without-data-b21c72b741fa)这样的模型用于图像分类，SAM用于分割，GroundingDino用于物体检测。基础模型通常较大且计算要求高。如果没有资源限制，它们可以直接用于零-shot推理。否则，它们可以用于标注数据集，以训练一个更小、更具体的模型，这一过程称为蒸馏。

在本指南中，我们将学习如何使用GroundingDino模型进行番茄图像的零-shot推理。我们将探索该算法的能力，并利用它标注整个番茄数据集。得到的数据集随后可以用于训练下游目标模型，如YOLO。

> 如果你没有付费的Medium账号，可以在[这里](/automatic-labeling-of-object-detection-datasets-using-groundingdino-b66c486656fe?sk=7c98df89b60ea49a6de9efd5278f645e)免费阅读。

# GroundingDino

***背景***

GroundingDino是由IDEA-Research在2023年开发的最先进（SOTA）算法[1]。它通过文本提示从图像中检测物体。名称“GroundingDino”结合了“grounding”（一个将视觉和语言理解连接在AI系统中的过程）和基于变换器的检测器“DINO”[2]。该算法是一个零-shot物体检测器，这意味着它可以识别那些它没有专门训练过的类别的物体，而无需看到任何示例（shot）。

***架构***

1.  模型接收图像和文本描述的配对作为输入。

1.  图像特征通过**图像骨干网络**（如Swin Transformer）提取，文本特征通过**文本骨干网络**（如BERT）提取。

1.  **特征增强器**模块通过多模态精细化结合文本和图像特征，使用交叉注意机制促进这两种模态之间的互动。

1.  接下来，‘**语言引导查询选择**’模块选择与输入文本最相关的特征作为解码器查询。

1.  然后，这些查询被输入到**解码器**中，以精细调整与文本信息最佳对齐的物体检测框的预测。它输出最终的边界框建议。

1.  该模型输出900个物体边界框及其与输入文本的相似度分数。相似度分数高于`box_threshold`的框会被选中，且相似度高于`text_threshold`的单词会作为预测标签。

![](../Images/216264702fee5eb326e11e64b8d85eda.png)

图像由Xiangyu等人制作，2023年[3]

***提示工程***

GroundingDino模型将文本提示编码为一个学习到的潜在空间。改变提示可以产生不同的文本特征，这会影响检测器的性能。为了增强预测性能，建议尝试多个提示，选择一个能提供最佳结果的提示。值得注意的是，在写这篇文章时，我必须尝试多个提示，才能找到理想的那个，有时还会遇到意外的结果。

# 代码实现

***开始使用***

首先，我们将从GitHub克隆[GroundingDino仓库](https://github.com/IDEA-Research/GroundingDINO)，通过安装必要的依赖项来设置环境，并下载预训练的模型权重。

```py
# Clone:
!git clone https://github.com/IDEA-Research/GroundingDINO.git

# Install
%cd GroundingDINO/
!pip install -r requirements.txt
!pip install -q -e .

# Get weights
!wget -q https://github.com/IDEA-Research/GroundingDINO/releases/download/v0.1.0-alpha/groundingdino_swint_ogc.pth
```

***在图像上的推理***

我们将通过将物体检测算法应用于一张番茄的图像来开始探索。我们的初步目标是检测图像中的所有番茄，因此我们将使用文本提示`tomato`。如果你想使用不同的类别名称，可以用点号`.`将它们分开。请注意，边界框的颜色是随机的，并没有特定的含义。

```py
python3 demo/inference_on_a_image.py \
    --config_file 'groundingdino/config/GroundingDINO_SwinT_OGC.py' \
    --checkpoint_path 'groundingdino_swint_ogc.pth' \
    --image_path 'tomatoes_dataset/tomatoes1.jpg'  \
    --text_prompt 'tomato' \
    --box_threshold 0.35 \
    --text_threshold 0.01 \
    --output_dir 'outputs'
```

![](../Images/f8837e49d2c89df34eedfa619d8021a4.png)

使用‘**tomato**’提示的注释。图片由[Markus Spiske](https://www.pexels.com/photo/green-and-red-oval-fruits-965740/)提供。

GroundingDino 不仅将物体检测为类别（如番茄），还能够理解输入文本，这一任务被称为指代表达理解（Referring Expression Comprehension，简称REC）。让我们将文本提示从`番茄`改为`成熟番茄`，并获得以下结果：

```py
python3 demo/inference_on_a_image.py \
    --config_file 'groundingdino/config/GroundingDINO_SwinT_OGC.py' \
    --checkpoint_path 'groundingdino_swint_ogc.pth' \
    --image_path 'tomatoes_dataset/tomatoes1.jpg'  \
    --text_prompt 'ripened tomato' \
    --box_threshold 0.35 \
    --text_threshold 0.01 \
    --output_dir 'outputs'
```

![](../Images/ffd779c03397578fc361f8935242b524.png)

使用‘**成熟番茄**’提示的标注。图片来自[Markus Spiske](https://www.pexels.com/photo/green-and-red-oval-fruits-965740/)。

值得注意的是，模型能够“理解”文本，并区分“番茄”和“成熟的番茄”。它甚至会标注部分成熟但还没有完全变红的番茄。如果我们的任务只需要标注完全成熟的红色番茄，我们可以将`box_threshold`从默认值0.35调整为0.5。

```py
python3 demo/inference_on_a_image.py \
    --config_file 'groundingdino/config/GroundingDINO_SwinT_OGC.py' \
    --checkpoint_path 'groundingdino_swint_ogc.pth' \
    --image_path 'tomatoes_dataset/tomatoes1.jpg'  \
    --text_prompt 'ripened tomato' \
    --box_threshold 0.5 \
    --text_threshold 0.01 \
    --output_dir 'outputs'
```

![](../Images/18f9e79654ab0663260df8535cdebe7f.png)

使用‘**成熟番茄**’提示和`**box_threshold = 0.5**`的标注。图片来自[Markus Spiske](https://www.pexels.com/photo/green-and-red-oval-fruits-965740/)。

***标注数据集的生成***

尽管 GroundingDino 拥有出色的能力，但它是一个大型且较慢的模型。如果需要实时物体检测，可以考虑使用像 YOLO 这样的更快模型。训练 YOLO 及类似模型需要大量标注数据，这可能既昂贵又耗时。然而，如果你的数据不是独特的，你可以使用 GroundingDino 来标注数据。欲了解更多关于高效 YOLO 训练的信息，请参阅我之前的文章 [[4]](https://medium.com/towards-data-science/the-practical-guide-for-object-detection-with-yolov5-algorithm-74c04aac4843)。

GroundingDino 仓库包含一个脚本，用于以**COCO格式**标注图像数据集，这对于 YOLOx 等模型非常适用。

```py
from demo.create_coco_dataset import main

main(image_directory= 'tomatoes_dataset',
    text_prompt= 'tomato',
    box_threshold= 0.35,
    text_threshold = 0.01,
    export_dataset = True,
    view_dataset = False,
    export_annotated_images = True,
    weights_path = 'groundingdino_swint_ogc.pth',
    config_path = 'groundingdino/config/GroundingDINO_SwinT_OGC.py',
    subsample = None
)
```

+   export_dataset — 如果设置为 True，COCO 格式的标注将保存在名为‘coco_dataset’的目录中。

+   view_dataset — 如果设置为 True，标注后的数据集将在 FiftyOne 应用中显示以进行可视化。

+   export_annotated_images — 如果设置为 True，标注后的图像将保存在名为‘images_with_bounding_boxes’的目录中。

+   subsample (int) — 如果指定，则仅从数据集中标注这个数量的图像。

不同的 YOLO 算法需要不同的标注格式。如果你计划训练 YOLOv5 或 YOLOv8，你需要将数据集导出为**YOLOv5格式**。尽管主脚本中硬编码了导出类型，但你可以通过调整`create_coco_dataset.main`中的`dataset_type`参数，将其从`fo.types.COCODetectionDataset`更改为`fo.types.YOLOv5Dataset`（第72行）。为了保持组织性，我们还将输出目录名从‘coco_dataset’更改为‘yolov5_dataset’。更改脚本后，重新运行`create_coco_dataset.main`。

```py
 if export_dataset:
      dataset.export(
          'yolov5_dataset',
          dataset_type=fo.types.YOLOv5Dataset
      )
```

# 结语

GroundingDino 通过使用文本提示在目标检测标注方面提供了显著的进展。在本教程中，我们探讨了如何使用该模型自动标注图像或整个数据集。然而，在将这些标注用于训练后续模型之前，手动审查和验证这些标注至关重要。

_________________________________________________________________

*为方便起见，附带了一个包含完整代码的用户友好型 Jupyter 笔记本：*

# 感谢阅读！

**想了解更多吗？**

+   [**探索**](https://medium.com/@lihigurarie) 我撰写的更多文章

+   [**订阅**](https://medium.com/@lihigurarie/subscribe)以便在我发布文章时收到通知

+   在 [**Linkedin**](https://www.linkedin.com/in/lihi-gur-arie/) 上关注我

# 参考文献

[0] Colab 笔记本中的代码：[link](https://gist.github.com/Lihi-Gur-Arie/90e07b06a0cd756df9b5d29b96f7523a)

[1] [Grounding DINO：将 DINO 与基础预训练结合用于开放集目标检测](https://arxiv.org/pdf/2303.05499.pdf)，2023年。

[2] [Dino：使用改进的去噪锚框进行端到端目标检测](https://arxiv.org/pdf/2203.03605.pdf)，2022年。

[3] [一个开放且全面的管道，用于统一目标定位和检测](https://arxiv.org/pdf/2401.02361v2.pdf)，2023年。

[4] [YOLOv5 算法的目标检测实用指南](https://medium.com/towards-data-science/the-practical-guide-for-object-detection-with-yolov5-algorithm-74c04aac4843)，作者：Dr. Lihi Gur Arie。
