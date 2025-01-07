# LLaVA：一个开源的 GPT-4V(ision)替代方案

> 原文：[`towardsdatascience.com/llava-an-open-source-alternative-to-gpt-4v-ision-b06f88ce8efa?source=collection_archive---------2-----------------------#2024-01-23`](https://towardsdatascience.com/llava-an-open-source-alternative-to-gpt-4v-ision-b06f88ce8efa?source=collection_archive---------2-----------------------#2024-01-23)

## 在 Web、本地和 Google Colab 上运行 LLaVA

[](https://ya-lb.medium.com/?source=post_page---byline--b06f88ce8efa--------------------------------)![Yann-Aël Le Borgne](https://ya-lb.medium.com/?source=post_page---byline--b06f88ce8efa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b06f88ce8efa--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b06f88ce8efa--------------------------------) [Yann-Aël Le Borgne](https://ya-lb.medium.com/?source=post_page---byline--b06f88ce8efa--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b06f88ce8efa--------------------------------) ·7 分钟阅读·2024 年 1 月 23 日

--

![](img/ce1327ce8af3bcc9107ecbaba49c33c1.png)

好奇这张图片拍摄于哪里吗？问问 LLaVA 吧！(图片来自[Guy Rey-Bellet](https://pixabay.com/users/grey48-7109111/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3116211)，来自[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3116211))。

[LLaVA](https://llava-vl.github.io/)（**L**arge **L**anguage 和 **V**isual **A**ssistant 的缩写）是一个有前途的开源生成型 AI 模型，它复制了 OpenAI GPT-4 在与图像对话方面的一些能力。用户可以将图片添加到 LLaVA 的聊天对话中，不仅能够讨论这些图片的内容，还可以利用它们以视觉方式描述想法、情境或场景。

LLaVA 最引人注目的特点是，它能够在使用更简单的模型架构和数量级更少的训练数据的情况下，超越其他开源解决方案。这些特点使得 LLaVA 不仅在训练上更快速且成本更低，而且更适合在消费级硬件上进行推理。

本文概述了 LLaVA，并更具体地旨在

+   展示如何通过网络界面进行实验，以及如何在您的计算机或笔记本电脑上安装它

+   解释它的主要技术特点

+   通过一个简单的聊天机器人应用示例，展示如何用它编程，该应用是基于 HuggingFace 库（*Transformers* 和 *Gradio*）在 Google Colab 上构建的。

# 使用 LLaVA 在线

如果你还没有尝试过，使用 LLaVA 的最简单方法是访问其作者提供的[Web 界面](https://llava.hliu.cc/)。下面的截图展示了界面如何操作，其中用户根据冰箱内容的图片询问可以做哪些餐点的建议。用户可以通过左侧的控件上传图片，聊天界面允许提问并以文本形式获取答案。

![](img/e4dc5aac06a83925f5c53726d2e9212e.png)

[LLaVA Web 界面](https://llava.hliu.cc/)

在这个例子中，LLaVA 正确识别出了冰箱中的食材，例如蓝莓、草莓、胡萝卜、酸奶或牛奶，并建议了相关的食谱创意，比如水果沙拉、果昔或蛋糕。

与 LLaVA 的其他对话示例可在[项目网站](https://llava-vl.github.io/)上查看，这些示例说明了 LLaVA 不仅能够描述图像，还能够根据图像中的元素进行推理和推断（例如通过图片中的线索识别电影或人物、根据画图编码网站、解释幽默情况等）。

# 本地运行 LLaVA

LLaVA 还可以使用[Ollama](https://ollama.ai/)或 Mozilla 的‘[llamafile](https://github.com/Mozilla-Ocho/llamafile)’安装到本地计算机。这些工具可以在大多数仅配备 CPU 的消费级机器上运行，因为模型仅需要 8GB 内存和 4GB 的可用磁盘空间，甚至已经证明可以在 Raspberry PI 上成功运行。在围绕 Ollama 项目开发的工具和接口中，一个值得注意的项目是[Ollama-WebUI](https://github.com/ollama-webui/ollama-webui)（如下图所示），它复现了 OpenAI ChatGPT 用户界面的外观和操作体验。

![](img/da0916a9564113060e6305c3a0d43299.png)

[Ollama Web 用户界面](https://github.com/ollama-webui/ollama-webui) — 受[OpenAI ChatGPT](https://chat.openai.com/)的启发

# LLaVA 主要特性简要概述

LLaVA 是由威斯康星大学麦迪逊分校、微软研究院和哥伦比亚大学的研究人员设计的，并且最近在 NeurIPS 2023 上展示。该项目的代码和技术规格可以通过[Github 仓库](https://github.com/haotian-liu/LLaVA)访问，仓库还提供了与助手互动的各种接口。

正如作者在[论文摘要](https://arxiv.org/pdf/2310.03744.pdf)中总结的：

> [LLaVA] 在 11 个基准测试中达到了最先进的水平。我们的最终 13B 检查点仅使用了 1.2M 公开可用的数据，并且在单个 8-A100 节点上完成了大约 1 天的全训练。我们希望这能使最先进的大型多模态模型研究变得更加易于接触。代码和模型将公开发布。

论文中报告的基准结果，如下方的雷达图，展示了与其他最先进模型的对比改进。

![](img/98a3010228400b1064aded781a0eee41.png)

LLaVA 基准结果的雷达图（图片来自[论文](https://arxiv.org/pdf/2304.08485.pdf)）

## 内部工作机制

LLaVA 的数据处理工作流程在概念上非常简单。该模型本质上作为一个标准的因果语言模型工作，接受语言指令（用户文本提示）作为输入，并返回语言响应。语言模型处理图像的能力由一个独立的视觉编码器模型提供，该模型将图像转换为语言标记，这些标记被悄悄地添加到用户文本提示中（充当一种[软提示](https://huggingface.co/docs/peft/main/en/conceptual_guides/prompting)）。LLaVA 的处理流程如下所示。

![](img/b68138aa94aba7d0da27c16272489945.png)

LLaVA 网络架构（图片来自[论文](https://arxiv.org/pdf/2304.08485.pdf)）

LLaVA 的语言模型和视觉编码器分别依赖于两个参考模型，称为 Vicuna 和 CLIP。[Vicuna](https://lmsys.org/blog/2023-03-30-vicuna/)是一个基于 LLaMA-2（由 Meta 设计）的预训练大语言模型，具有与中型 LLM 相媲美的竞争性能（参见 HuggingFace 上[7B](https://huggingface.co/lmsys/vicuna-7b-v1.5)和[13B](https://huggingface.co/lmsys/vicuna-13b-v1.5)版本的模型卡）。[CLIP](https://openai.com/research/clip)是由 OpenAI 设计的图像编码器，经过预训练，可以在相似的嵌入空间中对图像和文本进行编码，使用的是**对比**语言-**图像**预训练（因此称为‘CLIP’）。LLaVA 中使用的模型是视觉变换器变种 CLIP-ViT-L/14（查看其在 HuggingFace 上的[模型卡](https://huggingface.co/openai/clip-vit-large-patch14)）。

为了匹配视觉编码器的维度与语言模型的维度，一个投影模块（如上图中的**W**）被应用。它在原始的[LLaVA](https://arxiv.org/abs/2304.08485)中是一个简单的线性投影，在[LLaVA 1.5](https://arxiv.org/abs/2310.03744)中是一个两层感知机。

## 训练过程

LLaVA 的训练过程由两个相对简单的阶段组成。

第一阶段仅仅是为了调优投影模块**W**，而视觉编码器和 LLM 的权重保持冻结。训练使用来自[CC3M 概念性字幕数据集](https://ai.google.com/research/ConceptualCaptions/)的约 60 万对图像/字幕数据的子集进行，并且可以在 HuggingFace 的[此仓库](https://huggingface.co/datasets/liuhaotian/LLaVA-CC3M-Pretrain-595K)中找到。

在第二阶段，投影模块的权重**W**与 LLM 的权重一起进行微调（同时保持视觉编码器的权重冻结），使用包含 158K 语言-图像指令跟随数据的训练集。这些数据是通过 GPT-4 生成的，包含对话、详细描述和复杂推理的示例，并且可以在 HuggingFace 的[此仓库](https://huggingface.co/datasets/liuhaotian/LLaVA-Instruct-150K)中找到。

整个训练过程大约需要一天时间，使用八个 A100 GPU。

# 使用 LLaVA 进行编程：如何开始

*代码可在* [*Colab 相关笔记本*](https://colab.research.google.com/drive/1L28bJX14-Y5lJvswYwydsletYFMIxVH5)*.* 

LLaVA 模型已集成在 Transformers 库中，可以通过标准的*pipeline*对象加载。7B 和 13B 版本的模型可以在[LLaVA 😊 Hub 空间](https://huggingface.co/llava-hf)上找到，并可以以 4 位和 8 位加载，以节省 GPU 内存。下面我们展示如何使用代码加载并运行模型，代码可以在 Colab 上使用 T4 TPU（15GB 内存 GPU）执行。

以下是加载 4 位 LLaVA 1.5 7B 版本的代码片段：

```py
from transformers import pipeline, BitsAndBytesConfig
import torch

quantization_config = BitsAndBytesConfig(
    load_in_4bit=True,
    bnb_4bit_compute_dtype=torch.float16
)

model_id = "llava-hf/llava-1.5-7b-hf"

pipe = pipeline("image-to-text", model=model_id, model_kwargs={"quantization_config": quantization_config})
```

然后让我们加载这张图片

![](img/ce1327ce8af3bcc9107ecbaba49c33c1.png)

我们使用标准的 PIL 库来加载这张图片：

```py
import requests
from PIL import Image

image_url = "https://cdn.pixabay.com/photo/2018/01/29/14/13/italy-3116211_960_720.jpg"
image = Image.open(requests.get(image_url, stream=True).raw)
image
```

最后，我们使用图像查询 LLaVA 模型，并提供一个提示请求描述这张图片。

注意：[提示的格式](https://huggingface.co/docs/transformers/model_doc/llava)如下

> “USER: <image>\n<prompt>\nASSISTANT:”

```py
prompt = "USER: <image>\nDescribe this picture​​\nASSISTANT:"

outputs = pipe(image, prompt=prompt, generate_kwargs={"max_new_tokens": 200})
print(outputs[0]['generated_text']) 
```

返回以下答案：

> USER: 描述这张图片
> 
> ​ASSISTANT: ​这张图片展示了一个大型、空旷的剧场，背景是壮丽的海洋景色。剧场四周被郁郁葱葱的绿色山坡环绕，远处可以看到一座雄伟的山脉。整个场景宁静而如画，阳光明媚地照耀在大地上。

## LLaVA 聊天机器人

最后，让我们创建一个简单的聊天机器人，依赖于 LLaVA 模型。我们将使用[Gradio 库](https://www.gradio.app/)，它提供了一种快速简便的方法来创建机器学习 Web 界面。

界面的核心是一个包含图像上传器（Gradio 图像对象）和聊天界面（Gradio [ChatInterface](https://www.gradio.app/docs/chatinterface)对象）的行。

```py
import gradio as gr

with gr.Blocks() as demo:

    with gr.Row():
      image = gr.Image(type='pil', interactive=True)

      gr.ChatInterface(
          update_conversation, additional_inputs=[image]
      )
```

聊天界面连接到一个*update_conversation*函数，该函数负责保持对话历史记录，并在用户发送消息时调用 LLaVA 模型生成响应。

```py
def update_conversation(new_message, history, image):

    if image is None:
        return "Please upload an image first using the widget on the left"

    conversation_starting_from_image = [[user, assistant] for [user, assistant] in history if not assistant.startswith('Please')]

    prompt = "USER: <image>\n"

    for i in range(len(history)):
        prompt+=history[i][0]+'ASSISTANT: '+history[i][1]+"USER: "

    prompt = prompt+new_message+'ASSISTANT: '

    outputs = pipe(image, prompt=prompt, generate_kwargs={"max_new_tokens": 200, "do_sample" : True, "temperature" : 0.7})[0]['generated_text']

    return outputs[len(prompt)-6:]
```

界面是通过调用*launch*方法启动的。

```py
demo.launch(debug=True)
```

几秒钟后，聊天机器人 Web 界面将出现：

![](img/05629290b6a0cdaaf30927ff858868a6.png)

恭喜，你的 LLaVA 聊天机器人已经启动并运行！

# 有用的链接

+   [HuggingFace LLaVA 模型文档](https://huggingface.co/docs/transformers/model_doc/llava)

+   [Llava Hugging Face 组织](https://huggingface.co/llava-hf)

+   使用 AutoPrecessor 和 LLaVAForConditionalGeneration 加载和运行 LLaVA：[Colab 笔记本](https://colab.research.google.com/drive/1_q7cOB-jCu3RExrkhrgewBR0qKjZr-Sx)

+   [GPT-4V(ision)系统卡片](https://cdn.openai.com/papers/GPTV_System_Card.pdf)

+   [理解视觉指令微调](https://newsletter.artofsaience.com/p/understanding-visual-instruction)

注意：除非另有说明，所有图片均由作者提供。
