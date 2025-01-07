# 使用 Nvidia 的 LLaMa-Mesh 生成 3D 图像

> 原文：[https://towardsdatascience.com/generate-3d-images-with-nvidias-llama-mesh-69a6929a4580?source=collection_archive---------10-----------------------#2024-11-19](https://towardsdatascience.com/generate-3d-images-with-nvidias-llama-mesh-69a6929a4580?source=collection_archive---------10-----------------------#2024-11-19)

## 深度学习论文

## 论文深度剖析（5分钟速览）

[](https://varshitasher.medium.com/?source=post_page---byline--69a6929a4580--------------------------------)[![Dr. Varshita Sher](../Images/a3f2e9bf1dc1d8cbe018e54f9341f608.png)](https://varshitasher.medium.com/?source=post_page---byline--69a6929a4580--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--69a6929a4580--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--69a6929a4580--------------------------------) [Dr. Varshita Sher](https://varshitasher.medium.com/?source=post_page---byline--69a6929a4580--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--69a6929a4580--------------------------------) ·5分钟阅读·2024年11月19日

--

![](../Images/54a94d7a83e9d4615209cfcd3b48eefe.png)

摘自论文

## 介绍

上周，NVIDIA 发布了一篇引人入胜的论文（[LLaMA-Mesh: 用语言模型统一生成 3D 网格](https://arxiv.org/abs/2411.09595)），该论文展示了如何使用自然语言生成 3D 网格物体。

简单来说，如果你能说，*“给我讲个笑话”*，那么现在你可以说，*“给我一个车的 3D 网格”*，它就能生成一个 OBJ 格式的输出文件（稍后详细说明）。

> 如果你想尝试一些例子，可以点击这里 —— [https://huggingface.co/spaces/Zhengyi/LLaMA-Mesh](https://huggingface.co/spaces/Zhengyi/LLaMA-Mesh)

对我来说，最令人惊讶的部分是，它在没有扩展词汇或引入新标记的情况下完成了这一点，这在大多数微调任务中是很常见的做法。

***但是首先，什么是 3D 网格？***

3D 网格是 3D 物体的数字表示，包含顶点、边和面。

例如，考虑一个立方体。它有 8 个顶点（角点）、12 条边（连接角点的线）和 6 个面（方形的面）。这是立方体的基本 3D 网格表示。立方体的顶点（`v`）定义了它的角点，面（`f`）描述了这些角点如何连接形成表面。

下面是一个 OBJ 文件的示例，表示 3D 物体的几何形状

```py
# Vertices
v: (0, 0, 0)
v: (1, 0, 0)
v: (1, 1, 0)
v: (0, 1, 0)
v: (0, 0, 1)
v: (1, 0, 1)
v: (1, 1, 1)
v: (0, 1, 1)

# Faces
f 1 2 3 4
f 5 6 7 8
f 1 5 8 4
f 2 6 7 3
f 4 3 7 8
f 1 2 6 5
```

这些数字随后被软件解释，软件会渲染出最终图像，即 3D 立方体。（或者你也可以使用像[这个](https://huggingface.co/spaces/Zhengyi/LLaMA-Mesh)这样的 HuggingFace 空间来渲染物体）

随着对象复杂度的增加（与上面的简单立方体相比），它们将有成千上万甚至百万个顶点、边和面来创建详细的形状和纹理。此外，它们还将有更多的维度来捕捉诸如纹理、朝向等信息。

实际来说，日常物品（如长椅）的 obj 文件大概长这样：

![](../Images/17e377768d383fdca3851d49b2b687f3.png)

不同对象的 obj 文件示例（摘自论文）

如你从上面的图片中可能已经注意到的那样，LLM（如 GPT4o 和 LLama3.1）在某种程度上能够直接生成 obj 文件。然而，如果你查看长椅的渲染网格图像，你会明白为什么从质量角度看需要进行微调。

## LLM 如何处理 3D 网格？

众所周知，LLMs 通过将标记（如 `cat`）转换为标记 ID（如 `456`）来理解文本。类似地，为了处理标准的 OBJ 格式，我们也必须以某种方式将通常是小数的顶点坐标转换为整数。

他们在论文中使用顶点量化来实现这一点，将一个坐标分割成多个标记（类似于长单词 `operational` 会被分割成两个标记 —— `oper` 和 `ational`，如 [GPT4o tokenizer](https://platform.openai.com/tokenizer) 所示）。正如预期的那样，减少表示小数的标记数会带来正常的精度-成本权衡。

![](../Images/5fb1a7ee1d43422858e1fc7445211069.png)

为了实现顶点量化，他们将网格中的三个轴都缩放到 (0, 64) 范围，并将坐标量化为最接近的整数，即每个轴的值可以在 0 到 64 之间（在本例中为 39、19 和 35）。最后，通过读取和生成这样的格式，LLM 就能够处理 3D 对象。

## LlaMa-Mesh 的训练过程是什么？

LLama-Mesh 是通过使用 SFT（监督式微调）方法对 LLama3.1–8B 指令模型进行微调而创建的，以提高其网格理解和生成能力。

由于它是一个 SFT，我们需要为其提供文本-3D 指令的输入输出示例。以下是一个示例：

```py
Input
User: Create a 3D obj file using the following description: a 3D model of a car.

Output
Assistant: <start of mesh> v 0 3 4 v 0 4 6 v 0 3 … f 1 3 2 f 4 3 5 … . <end of mesh>
```

除了生成 3D 网格，LLama-Mesh 还能够解释 3D 网格。为此，它的训练数据还包含了几个网格理解和网格生成的示例，作为对话式格式的一部分。以下是数据集中的一些示例：

![](../Images/dfc043eef6d5b257e2f48c7a48e81bec.png)

为 LLama-Mesh 精心策划的训练数据集

## 论文中的最有趣部分

+   LlaMa-Mesh 可以与文本和 3D 对象 **无缝** 交互，无需特殊的标记器或扩展 LLM 的词汇量（这要归功于使用 OBJ 格式和前述的顶点量化方法，它可以有效地将 3D 网格数据标记化为 LLM 可以无缝处理的离散标记）。

![](../Images/9c9d63bf68147f6a8d5ca8cbf283a39d.png)

图片摘自论文

+   LlaMa-Mesh可以从相同的输入文本生成多样化的形状。

![](../Images/9765acd06efa01af4b7afca377607486.png)

来自论文

+   尽管微调过程略微降低了模型的语言理解和推理能力（他们将其视为由选择的指令数据集和较小的8B模型规模所带来的局限性），但这一点被微调模型能够生成高质量OBJ文件以供3D网格生成的能力所抵消。

![](../Images/a9f3f98035525faf5b3b6f84f0f917ba.png)

对比基础模型和微调版本在评估模型的通用知识、常识推理和数学问题解决能力方面的指标（图片来自论文）。

## 为什么你应该关注这篇论文？

我已经为大语言模型生成类人文本、代码以及与视觉内容推理的能力感到惊叹。将3D网格加入这个列表简直是太聪明了。

像LLaMa-Mesh这样的LLM有潜力彻底改变包括游戏、教育和医疗在内的多个行业。

它可以用于直接从文本描述生成用于视频游戏的逼真资产，如角色、环境和物体。

同样，它可以加速产品开发和创意过程，因为任何公司都需要一个设计来知道他们要创造什么。

它还可以用于建筑物、机械、桥梁和其他基础设施项目的建筑设计。最后，在教育技术领域，它可以用于将互动3D模拟嵌入到培训材料中。

这篇论文简洁明了，阅读起来非常快速，我强烈建议你阅读它。

**论文页面** — [https://arxiv.org/pdf/2411.09595](https://arxiv.org/pdf/2411.09595)

**代码** — [https://github.com/nv-tlabs/LLaMA-Mesh](https://github.com/nv-tlabs/LLaMA-Mesh)

**Nvidia的博客** — [https://research.nvidia.com/labs/toronto-ai/LLaMA-Mesh/](https://research.nvidia.com/labs/toronto-ai/LLaMA-Mesh/)
