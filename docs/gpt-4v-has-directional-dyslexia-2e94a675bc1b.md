# GPT-4V 具有方向性阅读障碍

> 原文：[https://towardsdatascience.com/gpt-4v-has-directional-dyslexia-2e94a675bc1b?source=collection_archive---------6-----------------------#2024-02-20](https://towardsdatascience.com/gpt-4v-has-directional-dyslexia-2e94a675bc1b?source=collection_archive---------6-----------------------#2024-02-20)

## 展示了我们基于 WSDM 2023 Toloka VQA 挑战的研究

[](https://medium.com/@suxodolskaya?source=post_page---byline--2e94a675bc1b--------------------------------)[![Evgeniya Sukhodolskaya](../Images/2d7cd9aa6b106fefa2ae598a4255ec10.png)](https://medium.com/@suxodolskaya?source=post_page---byline--2e94a675bc1b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2e94a675bc1b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2e94a675bc1b--------------------------------) [Evgeniya Sukhodolskaya](https://medium.com/@suxodolskaya?source=post_page---byline--2e94a675bc1b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e94a675bc1b--------------------------------) ·阅读时长 10 分钟·2024年2月20日

--

![](../Images/9b963b7571afe769ac8dbfaaf657207b.png)

图像由 [GPT-4V](https://openai.com/research/gpt-4v-system-card) 生成

自从 [WSDM 2023 年 Toloka 图像问答（VQA）挑战赛](https://codalab.lisn.upsaclay.fr/competitions/7434)以来已经过去一年，正如我们当时预测的那样，获胜的机器学习解决方案并未超越人类基准。然而，过去一年充满了生成式 AI 的突破。感觉每篇文章都在交替指出 OpenAI 的 GPT 模型不能做的事情，并赞扬它们做得比我们更好的地方。

自 2023 年秋季以来，GPT-4 Turbo 已获得“视觉”能力，这意味着它可以接受图像作为输入，并且现在可以直接参与 VQA 挑战。我们很想测试它在我们的 Toloka 挑战中的表现，看看它是否终于缩小了与人类基准的差距。

## 图像问答

图像问答（VQA）是一个跨学科的人工智能研究问题，专注于让 AI 理解图像并用自然语言回答相关问题。这个领域有各种应用：帮助视障人士、丰富教育内容、支持图像搜索功能以及提供视频搜索功能。

VQA 的发展“伴随着巨大的责任”，例如确保技术应用的可靠性和安全性。随着 AI 系统具备视觉能力，虚假信息的传播潜力增加，考虑到有论点称 [“带有虚假信息的图像可能使陈述看起来更具可信度”](https://cdn.openai.com/papers/GPTV_System_Card.pdf)。

VQA领域的一个子领域，[VQA定位不仅仅是回答视觉问题，还涉及将这些答案与图像中的元素连接起来](https://arxiv.org/abs/2306.15195)。这个子领域在混合现实（XR）头戴设备、教育工具和在线购物等应用中具有巨大的潜力，通过将注意力引导到图像的特定部分，从而提升用户交互体验。Toloka VQA挑战赛的目标是支持VQA定位的发展。

## Toloka的VQA挑战赛回顾

在Toloka VQA挑战赛中，任务是根据描述物体功能而非其视觉特征的问题，识别出一个物体并将其放入一个边界框中。例如，问题可能不会问“找一个圆形的红色物体”，而是问“图中哪个物体适合放在沙拉和披萨上？”这反映了人类根据物体的实用性来感知物体的能力。就像当你看到一张桌子上有一份报纸、一只咖啡杯和一副眼镜时，问你“找个东西来拍苍蝇”，你会知道该选什么，而无需任何视觉描述。

**问题**：*我们用什么来将披萨切成块？*

![](../Images/4c46a016fdabfbe7c708aef05aca4729.png)

图片来自[“*Toloka VQA挑战赛*”](https://zenodo.org/records/7570356)（CC BY 4.0）

这个挑战要求同时整合视觉、文本和常识知识。作为基准方法，[我们提出将YOLOR和CLIP作为独立的视觉和文本骨干模型结合使用](https://ceur-ws.org/Vol-3357/invited1.pdf)。然而，获胜的解决方案根本没有使用双塔结构，[而是选择了Uni-Perceiver模型并配备了ViT-Adapter以便更好地进行定位](https://arxiv.org/pdf/2301.09045.pdf)。该方案最终获得了较高的交并比（IoU）得分**76.347**，但未能达到众包基准的IoU得分**87**。

鉴于人类和AI解决方案之间的巨大差距，我们非常好奇GPT-4V在Toloka VQA挑战赛中的表现。由于该挑战赛基于[MS COCO数据集](https://cocodataset.org/#home)，该数据集在计算机视觉中已经被多次使用（例如在[视觉空间推理数据集](https://aclanthology.org/2023.tacl-1.37.pdf)中），因此可能是GPT-4在训练数据中“已知”的，有可能GPT-4V会接近人类基准。

## GPT-4V和Toloka VQA挑战赛

最初，我们想了解GPT-4V是否能够直接应对Toloka VQA挑战赛。

![](../Images/e53d1d0d97fca3e14de6c9c25bd555d1.png)

然而，尽管 GPT-4V 大多数情况下正确地定义了物体，它在提供有意义的边界框坐标时遇到了严重的困难。这并不完全出乎意料，因为[OpenAI 的指南承认](https://platform.openai.com/docs/guides/vision) GPT-4V 在需要识别物体在图像中**精确**空间定位的任务中存在局限性。

![](../Images/52e8450191873d57d2be525055fcb178.png)

图片由作者提供

这促使我们探索 GPT-4 在识别图像中基本高层次位置方面的表现。它能否判断物体的位置——不一定准确，但能否知道它们在**左侧**、**中间**或**右侧**？或者是在**顶部**、**中间**或**底部**？由于这些位置不是精确的，对于 GPT-4V 来说，这可能是可行的，特别是考虑到它已在数百万张图像与对应说明文字的配对中进行训练，其中指出了物体的方向性位置。教育材料通常会详细描述图片（例如脑结构的教科书中会提到像“树突”位于图片的“左上方”或“轴突”位于“右下方”的部分）。

理解大语言模型（LLM）和多模态语言模型（MLM）在空间推理方面的局限性，即使是我们上述讨论的简单推理，在实际应用中也至关重要。[将 GPT-4V 集成到“Be My Eyes”应用程序中](https://cdn.openai.com/papers/GPTV_System_Card.pdf)，该应用通过解读图像来帮助视障用户，完美地展示了这一重要性。尽管 GPT-4V 的能力不容小觑，但该应用程序提醒用户要小心，强调了当前技术在关键的安全和健康环境中尚无法完全代替人类判断。然而，具体的技术不足之处并未明确指出。

## GPT-4V 和空间推理

在我们探索 GPT-4V 在图像中物体基本位置推理的过程中，我们从一个包含 4,500 对图像-问题的较大数据集（该数据集是比赛的私人测试数据集）中随机选择了 500 对图像-问题。我们尽量减少测试数据泄露到 GPT-4V 训练数据中的可能性，因为该子集的比赛数据是在比赛时间表中最后发布的。

![](../Images/fc3adeab5900b53eaa2b30e52f0f3c20.png)

在这 500 对数据中，有 25 对被 GPT-4V 拒绝，标记为“无效图像”。我们怀疑这种拒绝是由于内置的安全措施引起的，可能是由于图像中出现了可能被分类为个人身份信息（PI）的物体，例如人脸。剩下的 475 对数据被用于我们的实验。

理解事物在相互关系中的位置，例如弄清楚什么是左、中或右、上、中或下，并不像看起来那么简单。[很多事情取决于观察者的视角，物体是否有前面，如果有的话，它的方向如何](https://aclanthology.org/2023.tacl-1.37.pdf)。因此，人类的空间推理可能[依赖于我们进化历史中对世界的显著归纳偏差](https://arxiv.org/pdf/2304.11164.pdf)。

**问题：** *什么能保护眼睛免受灯光的眩光？*

![](../Images/185ea2c6c237fcf0ae592fad449e9ccc.png)

图片来自[“*Toloka VQA Challenge*”](https://zenodo.org/records/7570356)（CC BY 4.0）

以实验数据中采样的一个带灯罩的例子为例。一个人可能会说它偏向图像的左上方，因为灯罩稍微向左倾斜，而另一个人可能会称它为中上，看到它位于图片的中央。两种观点都有道理。要为定位建立严格的规则很难，因为物体可能有各种形状和部件，比如灯具的长电线，这可能会改变我们对其放置位置的看法。

考虑到这种复杂性，我们计划尝试至少两种不同的方法来标注图像中事物的位置的真实情况。

它的工作原理如下：如果图像中心与物体中心（由其边界框标记）的像素差小于或等于图像宽度的一定百分比（水平位置）或高度（垂直位置），那么我们就标记该物体位于中间。如果差异更大，则标记为左或右（或上或下）。我们决定使用2%作为阈值百分比。这个决定是基于观察物体相对于图像整体大小的像素差异如何表现的。

```py
object_horizontal_center = bb_left + (bb_right - bb_left) / 2
image_horizontal_center = image_width / 2
difference = object_horizontal_center - image_horizontal_center
if difference > (image_width * 0.02):
    return 'right'
else if difference < (-1 * image_width * 0.02):
    return 'left'
else:
    return 'middle'For our first approach, we decided on simple automated heuristics to figure out where objects are placed in a picture, both horizontally and vertically. This idea came from an assumption that GPT-4V might use algorithms found in publicly available code for tasks of a similar nature.
```

对于第二种方法，我们使用了众包标注。以下是众包项目设置的详细信息：

+   图片在没有边界框的情况下展示给众包人员，以鼓励他们对物体位置的标注不带有偏见（关于真实答案），就像回应有关物体在视觉上下文中放置的问题一样。

+   GPT-4V的回答既作为提示，也作为验证其物体检测准确性的一种方式。

+   参与者可以选择报告如果给定的图像无法清晰回答某个问题，从而将任何潜在的模糊/灰色区域情况从数据集中剔除。

为了确保众包回应的质量，我审查了所有GPT-4的回答与众包结果不符的情况。在此审查过程中，我无法看到GPT-4V或众包的回应，这使我能够在没有偏袒的情况下调整标签。

![](../Images/e3ed34bdd84415386cdfba2c3d50f8b9.png)

图片来自作者。*标注界面在* [*Toloka*](https://toloka.ai/)

## GPT-4V有方向性读写困难

我们选择了**准确度**作为评估指标，因为我们数据集中的类别分布均匀。在通过众包和启发式方法建立的真实标签上，我们评估了GPT-4V的表现，涵盖了475张图片，排除了45对众包认为难以回答的图片。剩余的数据表明，与众包和启发式标签相比，GPT-4V在识别水平和垂直位置时的准确度相当低，仅为**30%**。

![](../Images/eac053ca7d5e07416621b6c972568fab.png)

*与* ***自动启发式方法*** *相比，GPT-4V回答的准确度*

![](../Images/865d87e02fa9a53f3caa06bf703fff56.png)

*与* ***众包标签*** *相比，GPT-4V回答的准确度*

即使我们接受GPT-4V的回答为正确，只要它与众包或启发式方法相匹配，其准确度仍未达到50%，最终结果为**40.2%**。

为了进一步验证这些发现，我们手动复审了100个GPT-4V错误标注的图像-问题对。

![](../Images/beed3c08072603e39bd5376857733bf4.png)

通过直接要求GPT-4V指定物体的位置并比较其回应，我们确认了最初的结果。

![](../Images/db9e85d72435988c8d9bdcfd73d08c39.png)

图片由作者提供。*标签界面在* [*Toloka*](https://toloka.ai/)

GPT-4V一贯将左右、上下搞混，因此，如果GPT-4V是你的导航员，请准备好走风景路线——这完全是无意的。

然而，GPT-4V的物体识别能力令人印象深刻，达到了**88.84%**的准确率。这表明，通过将GPT-4V与专业的物体检测工具集成，我们可能能够达到（甚至超过）人类基准。这是我们研究的下一个目标。

## 提示工程与方向性阅读障碍

为了确保我们不是在指出GPT-4V的局限性而不进行任何提示优化努力，以避免变成我们所讨厌的那种情况，我们探索了研究文献中提到的多种提示工程技术，这些技术有助于增强大语言模型的空间推理能力。

**问题**：*什么被用作一个国家的象征或标志？*

![](../Images/dedd0be04c9d0c20f9f435f6b71e6eb1.png)

来自 [“*Toloka VQA Challenge*”](https://zenodo.org/records/7570356)（CC BY 4.0）

我们在上述实验数据集示例中应用了三种已发现的提示工程技术，而GPT-4V在这些示例中固执且一贯地误解了信息。图中询问的旗帜位于图片的**中右**位置。

“[《Shikra: 释放多模态LLM的参考对话魔力》](https://arxiv.org/abs/2306.15195)”论文介绍了一种结合了思维链（CoT）和位置标注，特别是中心标注的方法，称为**基础CoT（GCoT）**。在GCoT设定中，作者提示模型为每个提到的物体提供思维链并附上中心点。由于作者特别训练了模型来提供图像中物体的坐标，我们必须将提示工程技术适应到一个不那么严格的设定，要求模型根据物体的中心提供关于物体位置的推理。

![](../Images/e28185fd170537629617121eb251a0f1.png)![](../Images/c519689f4d6cfde878563a4e410598a3.png)

作者提供的图片。 ***基础CoT方法*** （*正确答案是* ***中右****）

Patel和Pavlick（2022）的研究 “[将语言模型映射到基础概念空间](https://openreview.net/pdf?id=gJcEM8sxHK)” 说明了GPT-3即使在基于文本的网格中也能掌握空间和方位方向，通过“定位”模型并使用训练中学习到的特定词形。他们用**北/南**和**西/东**代替了传统的**上/下**和**左/右**方位词，以指导模型的空间推理。

![](../Images/9903aa30513521037c4503e22b7df12e.png)![](../Images/4fc04e114f7201b5215d66444d5c86bf.png)

作者提供的图片。 ***方位方向方法*** （*正确答案是* ***东南****）

最后，"[视觉空间推理](https://aclanthology.org/2023.tacl-1.37.pdf)"文章阐述了空间描述中不同视角的重要性：**内在**框架是围绕物体中心的框架（例如，椅子后面 = 带靠背的那一面），**相对**框架是从观察者的视角出发的框架，而**绝对**框架则使用固定坐标（例如，椅子“北”侧）。英语通常偏向使用相对框架，因此我们在提示中明确提到它，希望能优化GPT-4V的空间推理能力。

![](../Images/e1ad259503bc85cad07b24c572ee3f89.png)![](../Images/9d3426d1c830f639bd2e883cd3939321.png)

作者提供的图片。 ***相对框架方法*** （*正确答案是* ***中右****）

如我们从示例中看到的，GPT-4V在基础空间推理方面仍然存在挑战。

## 结论与未来工作

GPT-4V在简单的空间推理上遇到困难，例如在图像中识别物体的水平和垂直位置。然而，基于隐性功能描述的强大物体识别能力仍然是非常有前景的。我们的下一步是将GPT-4V与专门训练用于图像物体检测的模型结合起来。让我们看看这种结合能否超越人类基准，在Toloka VQA挑战中取得胜利！
