# 从零开始构建产品经理的用户洞察收集工具

> 原文：[https://towardsdatascience.com/building-a-user-insights-gathering-tool-for-product-managers-from-scratch-a6459dc1c3f6?source=collection_archive---------8-----------------------#2024-08-12](https://towardsdatascience.com/building-a-user-insights-gathering-tool-for-product-managers-from-scratch-a6459dc1c3f6?source=collection_archive---------8-----------------------#2024-08-12)

## 向付费的客户洞察中心告别吧！学习如何将五个开源AI模型结合起来，自动化从用户访谈中收集洞察。

[](https://medium.com/@zaninihugo?source=post_page---byline--a6459dc1c3f6--------------------------------)[![Hugo Zanini](../Images/cbda793f1cf82f34b29c6e136556361d.png)](https://medium.com/@zaninihugo?source=post_page---byline--a6459dc1c3f6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a6459dc1c3f6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a6459dc1c3f6--------------------------------) [Hugo Zanini](https://medium.com/@zaninihugo?source=post_page---byline--a6459dc1c3f6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6459dc1c3f6--------------------------------) ·阅读时间 11 分钟 ·2024年8月12日

--

![](../Images/6094d50facdc92f35218923622bab796.png)

图片来自 [Daria Nepriakhina](https://unsplash.com/@epicantus?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于 [Unsplash](https://unsplash.com/photos/printed-sticky-notes-glued-on-board-zoCDWPuiRuA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

作为一个数据平台的技术产品经理，我经常进行用户访谈，以识别与数据开发过程相关的挑战。

然而，当我与用户一起探索一个新的问题领域时，我很容易被与组织内各个个体的众多对话所压倒。

随着时间的推移，我采用了一种系统化的方法来应对这一挑战。我专注于在每次访谈中做详细的笔记，然后再回顾这些笔记。这让我能够巩固理解并识别出用户讨论的模式。

然而，在做笔记和积极倾听之间分心往往会影响我对话的质量。我注意到，当别人为我做笔记时，我的访谈效果显著提升。这使我能够完全投入到与受访者的交流中，专注于他们所说的内容，从而进行更有意义和富有成效的互动。

为了提高效率，我从在会议中做笔记转变为每当有功能时，就进行录音并转录。这大大减少了我需要进行的访谈数量，因为我可以从较少的对话中获得更多的洞察。然而，这一变化要求我花时间回顾转录内容并观看视频。下图展示了我在绘制新的产品开发机会时所遵循的简化流程。

![](../Images/98af683776e7bdb22a3114f9e803ed9d.png)

图片来源：作者

**由于会议转录的体积和分类用户洞察的困难，整合与分析变得具有挑战性。**

此外，现有的会议转录工具仅限于英语，而我大多数的对话是葡萄牙语。因此，我决定在市场上寻找一个能够帮助我应对这些挑战的解决方案。

我找到的解决了大多数痛点的工具是 [Dovetail](https://dovetail.com/)、[Marvin](https://heymarvin.com/)、[Condens](https://condens.io/) 和 [Reduct](https://reduct.video/)。它们定位自己为客户洞察中心，主要产品通常是客户访谈的转录。

基本上，你可以在这里上传访谈视频，并获得一份转录，指明每一句话的发言人，并在每句话上附有指向原视频的超链接。在文本上，你可以添加高亮、标签和评论，还可以请求对话的总结。**这些功能将解决我的问题；然而，这些工具价格昂贵，特别是考虑到我住在巴西，而他们收取美元费用。**

> **这些工具没有任何革命性创新，所以我决定实现一个开源替代方案，可以在Colab笔记本上免费运行。**

## 需求

作为一名优秀的产品经理，我做的第一件事是根据用户（我的）需求识别产品的必备功能。以下是我所列出的高层次需求：

**成本与可访问性**

+   免费；

+   无需编程经验即可使用；

**数据隐私与安全**

+   保持数据私密——与外部服务无连接；

**性能**

+   执行速度必须快于视频时长；

+   高精度的多语言转录；

**功能**

+   发言人识别；

+   易于在转录内容中搜索；

+   轻松突出显示转录内容；

+   容易创建正在进行的研究的存储库；

**集成**

+   与我公司现有工具（Google Workspace）集成；

+   集成LLM模型以接收任务提示，基于转录内容执行；

## 解决方案

根据需求，我设计了我的解决方案应具备的功能：

![](../Images/92381377517963db8d4c326de1666e2b.png)

图片来源：作者

然后，我设计了预期的输入和用户界面来定义这些功能：

![](../Images/2cd00c7a1d251e1fb7d75824b24ef412.png)

图片来源：作者

用户将把他们的面试上传到YouTube，并设置为未列出的视频，然后创建一个Google Drive文件夹来存储转录文件。接着，他们可以访问Google Colab笔记本，提供面试的基本信息、粘贴视频网址，并可以选择为大型语言模型（LLM）定义任务。输出结果将是Google Docs，用户可以在其中整合洞察。

以下是产品架构。这个解决方案结合了五个不同的机器学习模型和一些Python库。接下来的部分将提供每个构建模块的概述；但是，如果你更感兴趣的是尝试这个产品，请跳到“I got it”部分。

![](../Images/96168c1df8b7d3ba902a9a547c92e61f.png)

图片由作者提供

## 面试设置与视频上传

为了创建一个用户友好的界面，用于设置面试并提供视频链接，我使用了[Google Colab的表单功能](https://colab.research.google.com/notebooks/forms.ipynb?ref=dataschool.io#scrollTo=ig8PIYeLtM8g)。它允许创建文本框、滑动条、下拉框等。代码被隐藏在表单后面，非常适合非技术用户使用。

![](../Images/89dea50e1e495b9369553a667507f74a.png)

面试选择表格 — 图片由作者提供

## 音频下载与转换

我使用了yt-dlp库来仅下载YouTube视频的音频，并将其转换为mp3格式。这个工具非常简单易用，你可以[在这里查看它的文档](https://github.com/yt-dlp/yt-dlp)。

![](../Images/4c47572c2a50d6190d0dac82e9bbbae7.png)

图片由[yt-dlp](https://github.com/yt-dlp/yt-dlp)提供

## 音频转录

为了转录会议内容，我使用了[Open AI的Whisper](https://github.com/openai/whisper)。这是一个开源的语音识别模型，训练数据来自超过68万小时的多语言数据。

该模型运行非常迅速；一段一小时的音频大约需要6分钟就能在16GB T4 GPU（Google Colab提供的免费GPU）上完成转录，并且它支持[99种不同语言](https://github.com/openai/whisper/blob/ba3f3cd54b0e5b8ce1ab3de13e32122d0d5f98ab/whisper/tokenizer.py#L11-L110)。

由于隐私是这个解决方案的一个要求，模型的权重被下载，所有推理操作都在Colab实例内部进行。我还在笔记本中添加了一个模型选择表单，用户可以根据他们所需要的精度选择不同的模型。

![](../Images/12597ca463c3d2bcaf8f4ae25e6d8dfb.png)

图片由作者提供

## 讲话者识别

讲话者识别是通过一种叫做讲话者分段技术（Speakers Diarization）来完成的。其原理是将音频划分为不同的语音段，每个段落对应一个特定的讲话者。通过这种方式，我们可以识别出谁在什么时候发言。

由于从YouTube上传的视频没有元数据来标识谁在说话，讲话者将被分为讲话者1、讲话者2等……稍后，用户可以在Google Docs中查找并替换这些名字，以添加讲话者的身份标识。

![](../Images/f8b66de2615683dd7210b7c4d1be7a3c.png)

图片由作者提供

对于说话者分离，我们将使用一个名为[多尺度说话者分离解码器（MSDD）](https://arxiv.org/pdf/2203.15974)的模型，该模型由Nvidia研究人员开发。这是一种先进的说话者分离方法，利用多尺度分析和动态加权来实现高精度和灵活性。

该模型以在识别并正确分类多个讲者交替发言的时刻而闻名——这是访谈中常见的现象。

这个模型可以通过[NVIDIA NeMo框架](https://github.com/NVIDIA/NeMo)使用。它让我能够获取MSDD检查点，并直接在colab笔记本中运行说话者分离，只需几行代码。

查看MSDD的说话者分离结果时，我注意到标点符号很差，长句子中某些像“*嗯*”和“*是的*”的插入语被误认为是说话者的中断——这使得文本难以阅读。

因此，我决定在管道中添加一个标点模型，以提高转录文本的可读性并便于人工分析。于是，我从[Hugging Face获取了punctuate-all模型](https://huggingface.co/kredor/punctuate-all)，这是一个非常精确且快速的解决方案，支持以下语言：英语、德语、法语、西班牙语、保加利亚语、意大利语、波兰语、荷兰语、捷克语、葡萄牙语、斯洛伐克语和斯洛文尼亚语。

## 视频同步

从我所对比的行业解决方案来看，一个强烈的需求是每个短语都应该与采访中讲述的时刻相关联。

Whisper转录包含指示短语说出时间戳的元数据；然而，这些元数据并不十分精确。

因此，我使用了一个名为[Wav2Vec2](https://arxiv.org/abs/2006.11477)的模型来更准确地进行这种匹配。基本上，该解决方案是一个神经网络，旨在学习音频表示并执行语音识别对齐。该过程包括在音频信号中找到每个段落说出的确切时间戳，并相应地对齐文本。

通过准确地匹配转录文本与时间戳，我通过简单的Python代码创建了超链接，指向视频中开始说出短语的时刻。

## LLM模型

这个管道步骤有一个准备好在本地运行并分析文本的大型语言模型，提供关于访谈的洞见。默认情况下，我添加了Gemma模型1.1b，并设置了一个提示来总结文本。如果用户选择进行总结，它将以项目符号列表的形式显示在文档顶部。

![](../Images/1175b705bc66896a25ab9333288fe76a.png)

图片由作者提供

此外，通过点击*显示代码*，用户可以更改提示并要求模型执行不同的任务。

![](../Images/f782e28c6df86a0db83ca2f6bb0210bc.png)

## 用于标签、重点和评论的文档生成

解决方案执行的最后一项任务是生成带有访谈转录和超链接的 Google Docs。这是通过[Google API Python 客户端库](https://googleapis.github.io/google-api-python-client/)完成的。

# 我知道了

由于该产品在我日常工作中变得非常有用，我决定给它起个名字，以便更容易引用。我将它称为**I**nsights **G**athering **O**pen-source **T**ool，简称iGot。

![](../Images/8d4274e8d7769313b98c24f070f4ab62.png)

[由 DALL·E-3 生成的图片](https://openai.com/policies/terms-of-use/)

在首次使用该解决方案时，需要进行一些初始设置。让我通过一个实际的例子来指导您开始使用。

## 打开 iGot 笔记本并安装所需的库

点击[此链接](https://colab.research.google.com/drive/1eMN4-b4kAH4uNTzzgJ-X4J90BYozGl6o?usp=sharing)打开笔记本并运行第一个单元格以安装所需的库。大约需要 5 分钟。

![](../Images/dd64fbb094759505bde82f7e4b22e499.png)

作者提供的图片

如果系统提示您重启笔记本，请直接取消。无需重启。

![](../Images/933bc06cfe3b64c5b15ec643472bc6e6.png)

作者提供的图片

如果一切按预期运行，您将看到“所有库已安装！”的消息。

![](../Images/1bfd13f827456fe8cd37187bf397eddd.png)

作者提供的图片

## 获取 Hugging 用户访问令牌和模型访问权限

*(此步骤仅在首次执行笔记本时需要)*

为了运行 Gemma 和 punctuate-all 模型，我们将从 Hugging Face 下载权重文件。为此，您必须申请一个用户令牌并获得模型访问权限。

为此，您需要创建一个 Hugging Face 账户，并按照以下步骤获取具有阅读权限的令牌。

![](../Images/bc3cd784c45aa25cbac192ff56881600.png)

作者提供的图片

一旦您获得了令牌，复制它并返回到实验笔记本。进入“Secrets”选项卡，然后点击“Add new secret”。

![](../Images/805b3887ad6143ac349b4a479e64bc27.png)

作者提供的图片

将您的令牌命名为*HF_TOKEN*，并粘贴您从 Hugging Face 获得的密钥。

![](../Images/3d133e00fce9e77c3aebe876f80e2051.png)

作者提供的图片

接下来，点击[此链接](https://medium.com/r?url=https%3A%2F%2Fhuggingface.co%2Fgoogle%2Fgemma-1.1-2b-it)打开 Hugging Face 上的 Gemma 模型。然后点击“确认许可”以获得模型访问权限。

![](../Images/2a1a389a054a697d3c52a2aee0a80a2b.png)

作者提供的图片

## 发送访谈

要将访谈发送到 iGot，您需要先将其作为未列出的 YouTube 视频上传。为了本教程的目的，我从 Andrej Karpathy 与 Lex Fridman 的访谈中截取了一部分，并将其上传到我的账户。这是 Andrej 给机器学习初学者的一些建议部分。

然后，您需要获取视频的 URL，将其粘贴到*Interview Selection*笔记本单元格的*video_url*字段中，定义一个名称，并注明视频中的语言。

一旦你运行了该单元，你将收到一条消息，指示音频文件已生成。

![](../Images/7cf4b3fb57c466921b073bc7a0f4af38.png)

作者提供的图片

## 模型选择与执行

在下一个单元中，你可以选择你想要用于转录的 Whisper 模型的大小。模型越大，转录精度越高。

默认情况下，选择的是最大的模型。做出选择后，运行该单元。

![](../Images/48cd47e34184546ea5fb73b849b06a4e.png)

作者提供的图片

然后，运行模型执行单元，执行上一部分中显示的模型流程。如果一切顺利，你应该在最后看到消息“标点符号处理完毕！”。

![](../Images/85223fc8f974c1c2401713da9585adf9.png)

作者提供的图片

如果你收到提示消息，询问是否允许访问 Hugging Face 令牌，请授予访问权限。

![](../Images/6405dfa35e01114692e8732e8ce80f3b.png)

作者提供的图片

## 配置转录输出

最后一步是将转录保存到 Google Docs 文件中。为此，你需要指定文件路径，提供采访名称，并指示是否希望 Gemma 总结会议内容。

第一次执行该单元时，你可能会收到一条提示消息，询问是否允许访问你的 Google Drive。点击“允许”。

![](../Images/9b8a9c6633be1f95a04f9f472ca7d678.png)

作者提供的图片

然后，给 Colab 完全访问你的 Google Drive 工作区。

![](../Images/b18e48f3be24d56a6af91e644cd05110.png)

作者提供的图片

如果一切顺利，你将会看到一个指向 Google Docs 文件的链接。只需点击它，你就可以访问你的采访转录。

![](../Images/0fb83c207c6df706d2958faf05fdba3f.png)

作者提供的图片

## 从生成的文档中提取见解

最终文档将包含转录内容，每个短语都与视频中开始的相应时刻相关联。由于 YouTube 不提供讲者元数据，建议使用 Google Docs 的查找和替换工具，将“Speaker 0”、“Speaker 1”等替换为实际的讲者名字。

![](../Images/931431f03c01af159f04973515555704.png)

作者提供的图片

有了这个，你可以处理高亮、笔记、反应等内容。如最初设想的那样：

![](../Images/08bd85c364d8aa7ff383f3237f763ad0.png)

作者提供的图片

# 最后的思考

该工具仍处于第一版本，我计划将其发展为一个更用户友好的解决方案。也许会搭建一个网站，让用户不需要直接与笔记本交互，或者开发一个插件，用于在 Google Meets 和 Zoom 中使用。

我的这个项目的主要目标是创建一个高质量的会议转录工具，它不仅对他人有益，还能展示现有的开源工具如何与商业解决方案的能力相匹配。

希望你觉得它有用！如果你有任何反馈或对iGot的发展有兴趣，欢迎随时通过[LinkedIn联系我](https://www.linkedin.com/in/hugozanini/)。
