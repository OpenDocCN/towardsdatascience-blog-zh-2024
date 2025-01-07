# Florence-2：通过单一VLM模型推动多个视觉任务的进展

> 原文：[https://towardsdatascience.com/florence-2-mastering-multiple-vision-tasks-with-a-single-vlm-model-435d251976d0?source=collection_archive---------2-----------------------#2024-10-14](https://towardsdatascience.com/florence-2-mastering-multiple-vision-tasks-with-a-single-vlm-model-435d251976d0?source=collection_archive---------2-----------------------#2024-10-14)

## Florence-2零样本能力的引导性探索：图像说明、物体检测、分割与OCR。

[](https://medium.com/@lihigurarie?source=post_page---byline--435d251976d0--------------------------------)[![Lihi Gur Arie, PhD](../Images/7a1eb30725a95159401c3672fa5f43ab.png)](https://medium.com/@lihigurarie?source=post_page---byline--435d251976d0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--435d251976d0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--435d251976d0--------------------------------) [Lihi Gur Arie, PhD](https://medium.com/@lihigurarie?source=post_page---byline--435d251976d0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--435d251976d0--------------------------------) ·阅读时长7分钟·2024年10月14日

--

![](../Images/d06dac442af9954dd35b05fe0f06a790.png)

图像注释由作者提供。原图来自 [Pexels](https://www.pexels.com/)。

## 介绍

近年来，计算机视觉领域见证了基础模型的崛起，这些模型无需训练定制模型即可进行图像注释。我们已经看到像[CLIP](/clip-creating-image-classifiers-without-data-b21c72b741fa?sk=88fdd2c1a132538015968df3f49b64b1) [2]这样的分类模型，[GroundingDINO](/automatic-labeling-of-object-detection-datasets-using-groundingdino-b66c486656fe?sk=7c98df89b60ea49a6de9efd5278f645e) [3]用于物体检测，和SAM [4]用于图像分割——每个模型在各自领域中表现优异。但是，如果我们有一个能够同时处理所有这些任务的单一模型呢？

> 如果你没有付费的Medium账户，你可以在[这里]( /florence-2-mastering-multiple-vision-tasks-with-a-single-vlm-model-435d251976d0?sk=e25bdee736a9aa9ace1ca80b98a036a4)免费阅读。

在本教程中，我们将介绍Florence-2 [1]——一个新颖的开源视觉语言模型（VLM），旨在处理多种视觉和多模态任务，包括图像说明、物体检测、分割和光学字符识别（OCR）。

配套的Colab笔记本中，我们将探索Florence-2在零样本条件下标注一张老式相机图像的能力。

# Florence-2

**背景**

Florence-2 由微软于 2024 年 6 月发布。它被设计为在单个模型中执行多个视觉任务。它是一个开源模型，遵循宽松的 MIT 许可，可以在 [Hugging Face](https://huggingface.co/microsoft/Florence-2-large) 上获取。

尽管其模型大小相对较小，版本有 0.23B 和 0.77B 参数，Florence-2 仍然达到了最先进（SOTA）的性能。其紧凑的大小使得它能够高效地部署在计算资源有限的设备上，同时确保快速的推理速度。

该模型在一个庞大且高质量的数据集 FLD-5B 上进行了预训练，包含了 54 亿个标注，涉及 1.26 亿张图像。这使得 Florence-2 在许多任务中能够实现零样本性能，而无需额外训练。

Florence-2 模型的原始开源权重支持以下任务：

可以通过微调模型添加额外的、不被支持的任务。

**任务格式**

受到大规模语言模型（LLMs）的启发，Florence-2 被设计为一个序列到序列的模型。它接受图像和文本指令作为输入，并输出文本结果。输入或输出的文本可以表示普通文本或图像中的区域。区域格式根据任务的不同而有所变化：

+   **边界框**：`'<X1><Y1><X2><Y2>’` 用于物体检测任务。标记表示框的左上角和右下角的坐标。

+   **四边形框**：`'<X1><Y1><X2><Y2><X3><Y3><X4><Y4>’` 用于文本检测，使用封闭文本的四个角的坐标。

+   **多边形**：`'<X1><Y1>...,<Xn><Yn>’` 用于分割任务，其中坐标表示多边形的顶点，按照顺时针顺序排列。

**架构**

Florence-2 基于标准的编码器-解码器 Transformer 架构构建。以下是该过程的工作原理：

1.  输入图像通过 DaViT 视觉编码器 [5] 嵌入。

1.  文本提示通过 BART [6] 嵌入，利用扩展的分词器和词嵌入层。

1.  视觉和文本的嵌入被拼接在一起。

1.  这些拼接后的嵌入通过基于 Transformer 的多模态编码器-解码器进行处理，以生成响应。

1.  在训练过程中，模型最小化交叉熵损失，类似于标准语言模型。

![](../Images/83b1b171555b8ba0c1e9513a11913d8f.png)

Florence-2 架构的示意图。来源：[link](https://arxiv.org/abs/2311.06242)*.*

# 代码实现

**加载 Florence-2 模型和示例图像**

在安装并导入必要的库（如随附的 Colab 笔记本所示）后，我们首先加载 Florence-2 模型、处理器和摄像头的输入图像：

```py
#Load model:
model_id = ‘microsoft/Florence-2-large’
model = AutoModelForCausalLM.from_pretrained(model_id, trust_remote_code=True, torch_dtype='auto').eval().cuda()
processor = AutoProcessor.from_pretrained(model_id, trust_remote_code=True)

#Load image:
image = Image.open(img_path)
```

**辅助函数**

在本教程中，我们将使用几个辅助函数。最重要的函数是 `run_example` 核心函数，它从 Florence-2 模型生成响应。

`run_example` 函数将任务提示与任何附加的文本输入（如果有的话）合并成一个单一的提示。通过 `processor`，它生成文本和图像嵌入，这些作为模型的输入。在 `model.generate` 步骤中，模型生成响应。以下是一些关键参数的拆解：

+   **max_new_tokens=1024**：设置输出的最大长度，允许生成详细的响应。

+   **do_sample=False**：确保响应是确定性的。

+   **num_beams=3**：使用束搜索，每一步选择最可能的 3 个令牌，探索多个潜在的序列，以找到最佳的整体输出。

+   **early_stopping=False**：确保束搜索在所有束达到最大长度或生成结束序列标记之前继续进行。

最后，模型的输出会通过 `processor.batch_decode` 和 `processor.post_process_generation` 解码和后处理，生成最终的文本响应，这些响应由 `run_example` 函数返回。

```py
def run_example(image, task_prompt, text_input=''):

    prompt = task_prompt + text_input

    inputs = processor(text=prompt, images=image, return_tensors=”pt”).to(‘cuda’, torch.float16)

    generated_ids = model.generate(
        input_ids=inputs[“input_ids”].cuda(),
        pixel_values=inputs[“pixel_values”].cuda(),
        max_new_tokens=1024,
        do_sample=False,
        num_beams=3,
        early_stopping=False,
    )

    generated_text = processor.batch_decode(generated_ids, skip_special_tokens=False)[0]
    parsed_answer = processor.post_process_generation(
        generated_text,
        task=task_prompt,
        image_size=(image.width, image.height)
    )

    return parsed_answer
```

此外，我们利用辅助函数来可视化结果（`draw_bbox`、`draw_ocr_bboxes` 和 `draw_polygon`）并处理边界框格式之间的转换（`convert_bbox_to_florence-2` 和 `convert_florence-2_to_bbox`）。这些内容可以在附带的 Colab 笔记本中探索。

# 任务

Florence-2 可以执行多种视觉任务。让我们从图像标题生成开始，探索它的一些功能。

## ***1\. 标题生成相关任务:***

**1.1 生成标题**

Florence-2 可以根据 `'<CAPTION>'`、`'<DETAILED_CAPTION>'` 或 `'<MORE_DETAILED_CAPTION>'` 任务提示生成不同细节级别的图像标题。

```py
print (run_example(image, task_prompt='<CAPTION>'))
# Output: 'A black camera sitting on top of a wooden table.'

print (run_example(image, task_prompt='<DETAILED_CAPTION>'))
# Output: 'The image shows a black Kodak V35 35mm film camera sitting on top of a wooden table with a blurred background.'

print (run_example(image, task_prompt='<MORE_DETAILED_CAPTION>'))
# Output: 'The image is a close-up of a Kodak VR35 digital camera. The camera is black in color and has the Kodak logo on the top left corner. The body of the camera is made of wood and has a textured grip for easy handling. The lens is in the center of the body and is surrounded by a gold-colored ring. On the top right corner, there is a small LCD screen and a flash. The background is blurred, but it appears to be a wooded area with trees and greenery.'
```

该模型能够准确描述图像及其周围环境，甚至识别相机的品牌和型号，展示了其 OCR 能力。然而，在 `'<MORE_DETAILED_CAPTION>'` 任务中存在一些小的不一致，这在零-shot 模型中是可以预期的。

**1.2 为给定的边界框生成标题**

Florence-2 可以为图像中特定区域（由边界框定义）生成标题。为此，它需要边界框的位置作为输入。你可以通过 `'<REGION_TO_CATEGORY>'` 提取类别，或通过 `'<REGION_TO_DESCRIPTION>'` 提取描述。

为了方便起见，我在 Colab 笔记本中添加了一个小部件，允许你在图像上绘制边界框，并提供代码将其转换为 Florence-2 格式。

```py
task_prompt = '<REGION_TO_CATEGORY>'
box_str = '<loc_335><loc_412><loc_653><loc_832>'
results = run_example(image, task_prompt, text_input=box_str)
# Output: 'camera lens'
```

```py
task_prompt = '<REGION_TO_DESCRIPTION>'
box_str = '<loc_335><loc_412><loc_653><loc_832>'
results = run_example(image, task_prompt, text_input=box_str)
# Output: 'camera'
```

在这种情况下，`'<REGION_TO_CATEGORY>'` 识别了镜头，而 `'<REGION_TO_DESCRIPTION>'` 则不够具体。然而，这种表现可能会随着不同图像的变化而有所不同。

## 2\. 物体检测相关任务：

**2.1 生成物体的边界框和文本**

Florence-2 可以识别图像中密集的区域，并提供它们的边界框坐标以及相关的标签或标题。要提取带标签的边界框，请使用 `'<OD>'` 任务提示：

```py
results = run_example(image, task_prompt='<OD>')
draw_bbox(image, results['<OD>'])
```

要提取带有标题的边界框，请使用 `'<DENSE_REGION_CAPTION>'` 任务提示：

```py
task_prompt results = run_example(image, task_prompt= '<DENSE_REGION_CAPTION>')
draw_bbox(image, results['<DENSE_REGION_CAPTION>'])
```

![](../Images/0efcbdf15dc83e62153b6a752144f800.png)

左侧的图像展示了‘<OD>’任务提示的结果，而右侧的图像展示了‘<DENSE_REGION_CAPTION>’的结果。

**2.2 文本基础的物体检测**

Florence-2还可以执行文本基础的物体检测。通过提供特定的物体名称或描述作为输入，Florence-2能够检测到围绕指定物体的边界框。

```py
task_prompt = '<CAPTION_TO_PHRASE_GROUNDING>'
results = run_example(image,task_prompt, text_input=”lens. camera. table. logo. flash.”)
draw_bbox(image, results['<CAPTION_TO_PHRASE_GROUNDING>'])
```

![](../Images/4a18ac8e5ecfafaded830f3246ee3953.png)

CAPTION_TO_PHRASE_GROUNDING任务，文本输入为：“镜头。相机。桌子。标志。闪光。”

## 3. 分割相关任务：

Florence-2也可以生成由文本（`'<REFERRING_EXPRESSION_SEGMENTATION>'`）或边界框（`'<REGION_TO_SEGMENTATION>'`）约束的分割多边形：

```py
results = run_example(image, task_prompt='<REFERRING_EXPRESSION_SEGMENTATION>', text_input=”camera”)
draw_polygons(image, results[task_prompt])
```

```py
results = run_example(image, task_prompt='<REGION_TO_SEGMENTATION>', text_input="<loc_345><loc_417><loc_648><loc_845>")
draw_polygons(output_image, results['<REGION_TO_SEGMENTATION>'])
```

![](../Images/d3545d83b63447e4043f6d333e0c4d17.png)

左侧的图像展示了使用‘相机’文本作为输入的REFERRING_EXPRESSION_SEGMENTATION任务的结果，右侧的图像展示了使用边界框围绕镜头作为输入的REGION_TO_SEGMENTATION任务的结果。

## 4. OCR 相关任务：

Florence-2展示了强大的OCR能力。它可以通过`'<OCR>'`任务提示从图像中提取文本，或者通过`'<OCR_WITH_REGION>'`提取文本及其位置：

```py
results = run_example(image,task_prompt)
draw_ocr_bboxes(image, results['<OCR_WITH_REGION>'])
```

![](../Images/3cab509233de43f25be60c54ce24c3a2.png)

# 结论

Florence-2是一个多功能的视觉语言模型（VLM），能够在单一模型中处理多种视觉任务。它在图像描述、物体检测、分割和OCR等多种任务中都展示了出色的零-shot能力。虽然Florence-2开箱即用效果良好，但进一步的微调可以让模型适应新任务或在独特的自定义数据集上提高性能。

# 感谢阅读！

恭喜你一路走到了这里。点击👍表示感谢，并提升算法的自尊心 🤓

**想了解更多吗？**

+   [**探索**](https://medium.com/@lihigurarie)我写的其他文章

+   [**订阅**](https://medium.com/@lihigurarie/subscribe)以在我发布文章时收到通知

+   在[**Linkedin**](https://www.linkedin.com/in/lihi-gur-arie/)上关注我

# 完整代码，作为Colab笔记本：

# 参考文献

[0] Colab Notebook中的代码: [link](https://gist.github.com/Lihi-Gur-Arie/427ecce6a5c7f279d06f3910941e0145)

[1] Florence-2: [推进统一的表示方法，以应对多种视觉任务](https://arxiv.org/pdf/2311.06242).

[2] CLIP: [从自然语言监督中学习可转移的视觉模型](https://arxiv.org/pdf/2103.00020v1).

[3] Grounding DINO: [结合DINO和基础预训练进行开放集物体检测](https://arxiv.org/abs/2303.05499).

[4] SAM2: [图像和视频中的任何物体分割](https://arxiv.org/pdf/2408.00714).

[5] DaViT: [双重注意力视觉变换器](https://arxiv.org/abs/2204.03645).

[6] BART: [去噪序列到序列预训练，用于自然语言生成、翻译和理解](https://arxiv.org/pdf/1910.13461).
