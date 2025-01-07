# UniFliXsg：为新加坡大学提供AI驱动的本科课程推荐

> 原文：[https://towardsdatascience.com/uniflixsg-ai-powered-undergraduate-program-recommendations-for-singapore-universities-b9b448f7ea19?source=collection_archive---------11-----------------------#2024-08-14](https://towardsdatascience.com/uniflixsg-ai-powered-undergraduate-program-recommendations-for-singapore-universities-b9b448f7ea19?source=collection_archive---------11-----------------------#2024-08-14)

## AI如何为你推荐专业？

[](https://oadultradeepfield.medium.com/?source=post_page---byline--b9b448f7ea19--------------------------------)[![Phanuphat (Oad) Srisukhawasu](../Images/9267b36dccb1782caa4017ca6f0656f3.png)](https://oadultradeepfield.medium.com/?source=post_page---byline--b9b448f7ea19--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b9b448f7ea19--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b9b448f7ea19--------------------------------) [Phanuphat (Oad) Srisukhawasu](https://oadultradeepfield.medium.com/?source=post_page---byline--b9b448f7ea19--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b9b448f7ea19--------------------------------) ·阅读时长7分钟·2024年8月14日

--

一周前，我在[我的LinkedIn](https://www.linkedin.com/in/psrisukhawasu/)上分享了我的最新项目UniFliXsg的故事，这是一款基于你的兴趣和职业目标，推荐新加坡本科专业的AI应用。如果你还没看到，下面的链接也可以尝试。目前，数据库只包含NUS、NTU、SMU和SUTD的单一专业课程。

[](https://huggingface.co/spaces/oadultradeepfield/uniflixsg?source=post_page-----b9b448f7ea19--------------------------------) [## Uniflixsg — 由oadultradeepfield创建的Hugging Face空间

### 发现社区中制作的精彩机器学习应用

huggingface.co](https://huggingface.co/spaces/oadultradeepfield/uniflixsg?source=post_page-----b9b448f7ea19--------------------------------)

如承诺的那样，我将在这篇Medium博客中分享我如何从零开始做这件事的工作流程和技术细节。本文的布局与我的思维过程相同，因此你可能会看到步骤之间有些跳跃。此外，本文几乎没有代码。如果你感兴趣，可以在这个[GitHub仓库](https://github.com/oadultradeepfield/UniFliXsg/)中查看相关代码。

这是我的第一篇博客，如果我们还不认识，我是Oad。目前我在NUS攻读计算机科学本科，热衷于AI，特别是大型语言模型（LLM）。这也是我开始这个项目的原因之一，目的是更深入地了解它们的应用 :)

# 步骤 #1：概述工作流程

一切都从规划开始。我首先想到的是做类似于基于内容的过滤，其中我们可以将用户的资料与项目的信息进行匹配。在查看了项目的样本后，我发现所有项目的信息基本上就是描述和职业前景。因此，我可以采用技术来计算用户与每条信息之间的平均相似度，以便推荐项目。这有点类似于[语义搜索](https://en.wikipedia.org/wiki/Semantic_search)，在语义搜索中，我们将用户的查询与数据库中的条目进行匹配。

我还注意到，不同大学之间可能存在文化差异，因此将大学的描述作为另一条信息包含进来，可能有助于提供更个性化的推荐。在这一刻，UniFliXsg这个名字诞生了，它模仿了Netflix，这也是以其强大的推荐系统而著称。

在用户端，我会让他们输入个人兴趣和职业目标。为了减少额外的计算，我决定让输入到模型中的用户资料是一个单一查询，即“我对`user_input`感兴趣。毕业后，我想做`user_input`的工作。”

我使用了[余弦相似度](https://en.wikipedia.org/wiki/Cosine_similarity)来计算相似度分数。我选择这个度量标准，因为它比其他距离度量能够更好地处理长度不同的向量。因此，我们将首先把每个信息和用户的个人资料转换成向量（文本嵌入），然后计算分数。整体的计划如下面的图所示。

![](../Images/d9d804d8dd360ea5e8b4ce8e2c5f7b9d.png)

这是这个想法的一个简单示意图。在这里，我将嵌入空间简化为二维空间。余弦相似度就是向量之间角度的余弦值，具体的公式将在后面详细说明。

# 第二步：数据收集与预处理

这是这个项目中最耗时的部分。每个项目的信息组织方式完全不同。因此，我决定手动收集所有数据，而不使用任何网页抓取技术，因为无论如何这样做所需的时间和精力差不多。

你可以在之前提供的 Hugging Face 仓库中找到数据集。数据以 parquet 文件格式存储，因为我计划在这个项目中使用[Polars](https://pola.rs/)而不是 Pandas。我以前从未使用过它，但听说它更快，所以我想尝试一下！不过，最初我将数据存储在 Excel 文件中，因为我发现这样操作表格最方便。下图展示了在进行任何嵌入处理之前，数据的原始样貌。

![](../Images/ef369387568ef6fc554017c2fb1d6700.png)

用于收集数据的 Microsoft Excel 表格截图，数据尚未进行嵌入处理。

# 第三步：文本嵌入与模型选择

为了将数据集中的文本进行嵌入，使用一些来自 Hugging Face 的语言模型可能会很方便，这些模型是预训练的，已经知道词语之间的相似度。我们可以只使用嵌入层，而不是整个模型。通常，我们可以使用 [Sentence Transformers](https://huggingface.co/sentence-transformers) 库从大多数模型中访问这些层来进行文本嵌入。我们可以通过以下方式简单地初始化：

```py
from sentence_transformers import SentenceTransformer

# model_name is the name of the model to use
model = SentenceTransformer(model_name)
```

首先，我们语料库中的每个文本都会被标记化（即拆分为独立的单词）。处理过程将自动与我们选择的模型配置匹配。每个标记（单词）将通过嵌入矩阵嵌入为一个向量。最终，我们将得到一个矩阵，可以通过转换器的层进行处理。输出是一个 n 维向量。

在选择模型时，我们应考虑选择高性能和轻量级模型之间的平衡。通常，句子转换模型比大多数模型更轻量。然而，由于 Hugging Face 的免费空间仅限于使用 CPU，我决定选择一些已知在任务中表现良好的轻量级模型。这使得用户在使用应用程序时能够获得更快的结果。

说实话，我试过几种模型，但我发现最适合这个任务的模型是“[all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2)”。我使用这个模型将数据集中的所有相关文本进行嵌入。当用户输入他们的个人资料时，我们只需嵌入个人资料，并根据计算与每个程序的相似度。这有助于通过避免每次运行任务时都嵌入数据集中的文本来节省计算资源。

# 第四步：计算余弦相似度

余弦相似度将在程序的每个向量（代表不同的信息）与用户的向量之间计算。最终的相似度得分是三项得分的平均值。然而，这种方法有一个复杂的部分。经过一些测试，我发现模型并没有提供非常准确的结果。因此，我决定对每个相似度得分进行不同的加权。我给程序描述和职业前景赋予更多的权重，而给大学描述赋予较少的权重。这本质上是 [加权算术平均数](https://en.wikipedia.org/wiki/Weighted_arithmetic_mean)，可以最终得到更准确的结果。

![](../Images/4def4740fb6d4fda5af2aec172ec9073.png)

这个项目的加权算术平均数的更精确公式。

在获取所有相似度后，我将它们按降序排序，并返回前十个结果。如前所述，所有代码和数据都已上传到我的 [GitHub](https://github.com/oadultradeepfield)。

![](../Images/e8dadab9811abe0f0f1d20692e429730.png)

使用定义好的函数来计算相似度并返回最匹配的输出。这是后续步骤中创建的应用程序原型。

# 第五步：创建和部署应用

我通过集成[Gradio](https://www.gradio.app/)和Hugging Face生态系统构建了这个应用。如果你是新手，Gradio是一个库，它允许你使用其模板和预构建的元素，直接在Python中创建和部署应用原型。它非常方便，因为它促进了更快的用户生产，而且不一定需要我了解太多关于网页开发的知识。尽管我使用了一些HTML知识来构建应用，但仅仅是为了居中或缩放元素。下面我提供了一些代码片段，展示Gradio在实际使用中的样子。

```py
selected_programs = []

with gr.Blocks(theme=gr.themes.Soft(primary_hue="rose", neutral_hue="slate", font=gr.themes.GoogleFont("Inter"))) as app:
    with gr.Column():
        gr.HTML(value='<p align="center"> Made with ❤️📚 by <a href="https://www.instagram.com/oadultradeepfield/"> @oadultradeepfield </a> </p>', visible=True)
        gr.HTML(value='<h1 align="center">Discover Your Perfect Undergraduate Program in Singapore with AI-Powered Matching!</h1>', visible=True)
        gr.HTML(value='<h3 align="center">What Are the Key Word(s) That Best Describe You?</h2>', visible=True)
        gr.HTML(value='<p align="center"> P.S. Only single major programs will be shown. 😉 <br> You can also try out instantly with the example keywords. 🚀 </p>', visible=True)

    with gr.Row():
        with gr.Column():
            input_interest = gr.Textbox(placeholder='e.g. Business, Coding, Data Analysis, etc.',
                                        label='I am interested in...', scale=3)
            input_career = gr.Textbox(placeholder='e.g. Entrepreneur, Data Scientist, Software Engineer, etc.',
                                    label='Upon graduation, I want to work as...', scale=3)

        button = gr.Button("🔍 Search Now!")

        button.click(fn=return_search_results, inputs=[input_interest, 
                                                       input_career],
                     outputs=selected_programs)  

    for _ in range(10):
        with gr.Row():
            selected_programs.append(gr.Image(visible=False))
            selected_programs.append(gr.HTML(visible=False))

app.launch()
```

最后，这个应用已经构建完成，可以通过[链接](https://huggingface.co/spaces/oadultradeepfield/uniflixsg)在任何设备上轻松访问我的Hugging Face空间！

![](../Images/48eda7617fa06d0a8d80ccbaf9740588.png)

这是通过桌面访问该应用时的界面。它也是响应式的，你可能会在其他移动设备上看到略有不同的配置。

## 数据可用性声明

本项目使用的数据集直接来源于各大学本科项目的官方网站。由于这些信息是公开可用的，可以通过标准搜索引擎找到。

## 致谢

我增加了这个特殊部分，向社区和与我在LinkedIn帖子互动的人表示衷心感谢。我甚至收到了Gradio的评论，这虽然出乎意料，但非常感激。我还收到了评论和其他平台上的反馈和友善建议。所以，感谢每一位为我的学习做出贡献的人！我将在未来的博客和帖子中分享更多故事，届时见！

除非另有注明，所有图片均为作者提供。
