# 我为“Read the Docs”流量分析构建了一个可重复使用的仪表板，使用了Vizro-AI

> 原文：[https://towardsdatascience.com/i-built-a-reusable-dashboard-for-read-the-docs-traffic-analytics-using-vizro-47dc15dc04f8?source=collection_archive---------1-----------------------#2024-05-17](https://towardsdatascience.com/i-built-a-reusable-dashboard-for-read-the-docs-traffic-analytics-using-vizro-47dc15dc04f8?source=collection_archive---------1-----------------------#2024-05-17)

## （不到50行代码）

[](https://stichbury.medium.com/?source=post_page---byline--47dc15dc04f8--------------------------------)[![Jo Stichbury](../Images/c72e6f969467e9dfcaa156924ebf061e.png)](https://stichbury.medium.com/?source=post_page---byline--47dc15dc04f8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--47dc15dc04f8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--47dc15dc04f8--------------------------------) [Jo Stichbury](https://stichbury.medium.com/?source=post_page---byline--47dc15dc04f8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47dc15dc04f8--------------------------------) ·阅读时间7分钟·2024年5月17日

--

![](../Images/540bdff879af04e5dad6ac1ae7ddefcc.png)

来自典型流量数据的最终仪表板

在这篇文章中，我将解释我是如何构建一个仪表板来可视化我作为技术作家维护的一些文档的流量数据的。我设计技能有限，Python经验也不多，所以我需要一个简单的、低代码的方法来展示我所维护文档的影响和使用情况。最终，我找到了一个开源解决方案：[Vizro](https://github.com/mckinsey/vizro)作为低代码仪表板的模板，和[Vizro-AI](https://vizro.readthedocs.io/projects/vizro-ai/en/latest/)来使用生成性AI构建单独的图表。

## TL;DR?

如果你想直接开始，可以在我的[GitHub仓库](https://github.com/stichbury/documentation_project)中找到仪表板的Jupyter Notebook代码。

## 一个Read the Docs仪表板项目

如果像我一样，你管理着一个使用[Read the Docs (RTD)](https://docs.readthedocs.io/en/stable/)的开源文档项目，你可能已经发现，你可以从项目仪表板中下载过去90天的流量数据，文件格式为CSV。仪表板还会显示一个每日页面浏览量总计的图表，像下面这个图。

![](../Images/a52c2996c34389553866d91c6c49758b.png)

一个典型的RTD页面浏览量图表（唯一提供的图形流量数据）

如果需要额外的可视化输出，您可以使用 Google Analytics (GA)。然而，某些项目不愿意使用 GA，因为它是否符合通用数据保护条例（GDPR）在[欧盟](https://usercentrics.com/knowledge-hub/google-analytics-and-gdpr-compliance-rulings/)等地区存在争议。

## **获取代码和数据**

*请注意，在下面的示例中，我使用了一组由 OpenAI 帮助生成的虚假 CSV 流量数据，以确保项目流量的私密性。这些虚假数据与真实的 RTD 数据具有相同的字段，因此您可以下载并使用从 RTD 仪表板中下载的数据。*

若要亲自运行此示例，您需要我的虚假数据（或您自己的下载数据）以及存储在我的[GitHub 仓库](https://github.com/stichbury/documentation_project)中的 Jupyter Notebook 代码。基本使用非常简单，但高级用户可以对其进行扩展。如果您确实创建了增强版，请告诉我！

# Vizro 和 Vizro-AI 是什么？

[Vizro](https://github.com/mckinsey/vizro) 是一个基于 [Plotly](https://plotly.com/) 和 [Dash](https://dash.plotly.com/) 构建的框架，采用配置方式指定自定义仪表板布局。Vizro 仪表板可以通过 Vizro-AI 填充，Vizro-AI 是一个独立的软件包，通过依赖生成 AI 简化了可视化过程。

在这个示例中，我提供了数据和自然语言指令，Vizro-AI 生成了 Python 代码并创建了我所请求的图表。对我作为作者来说，这非常有效，因为我没有前端设计技能，也不熟悉 Plotly，但我很乐意给出适当的生成 AI 提示，并从 OpenAI 生成图表。

## **设置 Vizro-AI**

在运行 Notebook 代码之前，您需要在一个虚拟环境中使用 Python 3.9 或更高版本[安装 Vizro-AI](https://vizro.readthedocs.io/projects/vizro-ai/en/latest/pages/user-guides/install/)。使用 `pip install vizro_ai` 安装该软件包。

接下来，您需要一个 API 密钥来访问 OpenAI。如果您还没有账号，请[创建一个](https://platform.openai.com/signup)，并购买一些积分来使用模型，因为您不能使用免费版本。生成一个 API 密钥并将其添加到您的环境中，以便您在下一步编写的代码可以成功调用 OpenAI。有关详细的[指南，请参考 OpenAI 文档](https://platform.openai.com/docs/quickstart/step-2-set-up-your-api-key)，该过程也可以在[Vizro-AI LLM 设置指南](https://vizro.readthedocs.io/projects/vizro-ai/en/latest/pages/user-guides/install/#set-up-access-to-a-large-language-model)中找到。

## **构建图表**

此时，您可以[打开Jupyter Notebook制作您的第一个图表，](https://vizro.readthedocs.io/projects/vizro-ai/en/latest/pages/tutorials/quickstart/#2-open-a-jupyter-notebook)或者只需打开[我的存储库中的Notebook](https://github.com/stichbury/documentation_project/tree/main)来逐步执行我创建的代码，并将您的RTD数据（或我提供的虚假数据）加载到一个名为`df`的pandas DataFrame中，如下面的代码所示。

以下代码显示了如何向Vizro-AI提交请求，以构建一个类似于Read the Docs项目仪表板中显示的图表，显示按日期查看的图表，但将数据分为两个跟踪，用于[文档的稳定和最新版本](https://docs.readthedocs.io/en/stable/versions.html)： 

“为最新和稳定版本的每个日期组合Views行。绘制一条平滑的线图，比较最新和稳定版本每个日期的Views。”

Vizro-AI将自然语言查询“**为最新和稳定版本的每个日期组合Views行。绘制一条比较最新和稳定版本每个日期Views的线图**”和数据框传递给模型。请注意，在上面的示例中，我指定了一个gpt-4模型。Vizro-AI将[默认使用gpt-3.5-turbo](https://vizro.readthedocs.io/projects/vizro-ai/en/latest/pages/user-guides/customize-vizro-ai/)，因为它提供了更低的价格和更高的速度来提供答案，但它并不提供最复杂的图表功能，因此我选择明确请求使用gpt-4模型。

图表输出将取决于您的数据，以及在提交查询时从OpenAI收到的输出。参数`explain=True`请求Vizro-AI解释生成的图表是如何获得的，解释将显示在Jupyter Notebook的输出中，同时使用`show()`命令显示的图表也会显示出来。

Vizro-AI返回的Insights文本解释了如何操作流量数据。代码部分描述了代码片段遵循的步骤，以生成所请求的线图。

![](../Images/01e9aec5fc54dae71510d4b1ca24b752.png)

通过调用plot()返回的Insights部分带有指令“为最新和稳定版本的每个日期组合Views行。绘制一条平滑的线图，比较最新和稳定版本每个日期的Views。”

返回的图表如下所示：

![](../Images/183d6d66f15d5403b42d6e4d87730231.png)

通过调用plot()返回的图表带有指令“为最新和稳定版本的每个日期组合Views行。绘制一条平滑的线图，比较最新和稳定版本每个日期的Views。”

## **构建更多图表**

我创建了一些额外的图表，以进一步说明我们文档的流量情况，如下所示：

“*整理 Path 中 Version==stable 的数据行。创建描述前 5 个页面总浏览量的水平条形图。在每个条形图上添加数字和标题‘前 5 个稳定页面的总浏览量’。减小标记的字体大小*” 和 “整理 Path 中 Version==stable 的数据行。为前 5 个 Path 的每个日期的总浏览量创建折线图”

Vizro-AI 通过生成操作数据和生成一组图表的代码为我减轻了很大的负担，这些图表本身就很有用。更有用的是将它们组合在一起形成一个完整的仪表板。

## **创建一个 Vizro 仪表板**

您可以在与上面的 Vizro-AI 代码相同的 Jupyter Notebook 中使用 Vizro。确保按照 [Vizro 文档](https://vizro.readthedocs.io/en/stable/pages/user-guides/install/) 中的描述进行 `pip install vizro`。这里是一个简单仪表板框架的代码，没有图表生成：

`# TO DO` 部分是我们添加每个图表的地方。

此时有两个选项：

+   使用 Vizro-AI 每次生成仪表板时生成图表

+   使用 Vizro-AI 返回的 Python 代码直接调用 Plotly。

第一个选项需要的代码较少，但返回速度较慢，而且更昂贵，因为它使用了调用 OpenAI 的 Vizro-AI。第二个选项速度更快，但需要更多的代码操作。

这是一个包含演示第一个选项的仪表板代码的单元格，其中包含调用 Vizro-AI 的函数（*如果您打算自己运行这个，请确保您正在使用我的存储库中的 Notebook，在加载数据并逐步执行设置调用 Vizro-AI 的单元格*）：

这是一个稍微不同的版本，它使用第二个选项生成其中一个图表。我趁机稍微调整了 Python 代码以改变线条的颜色，这已经是我对 Plotly 操作的极限了！（*再次强调，如果你打算自己运行这个，请确保你正在使用我的存储库中的 Notebook，在加载数据并逐步执行设置图表创建函数的单元格*）。

您可以下载 Jupyter Notebook 来尝试使用您自己的 Read the Docs 数据查看仪表板。使用我提供的虚假数据，它看起来如下。

![](../Images/21aad4bf348f8e913cfff9c1d0d9666e.png)

使用第 2 种方法构建的最终输出，这使我能够调整第一个图表中的颜色。

我的一位同事（感谢 Nadija！）给了我一个提示，你可以在 Notebook 中运行仪表板，然后通过查看你选择的端口在单独的浏览器窗口中查看，方法如下：

```py
Vizro().build(dashboard).run(port=8006) # localhost8006 in the browser
```

或者（感谢 Antony！），如我在上面的第二个仪表板示例中所示，你可以生成一个可点击的链接来查看仪表板，方法如下：

```py
Vizro().build(dashboard).run(jupyter_mode="external")
```

# **总结**

在这个示例中，我展示了如何使用 Vizro-AI 生成 Plotly 图表来可视化文档流量，然后将这些图表构建到一个 Vizro 仪表板中。

如果你具备数据科学和 Python 技能，并且有设计天赋，你*可能*会想挑战一下用 Plotly 和 Dash 构建一个仪表盘。但对于像我这样没有这些技能的人来说，能够使用 OpenAI 并实现上面提到的输出，简直是一个游戏规则的改变者。我现在只用大约 50 行代码，就能得到一个有用的 Read the Docs 流量数据可视化。它看起来专业，且容易扩展，相对也容易分享。通过更多的努力，我可以进一步改进它，添加自定义功能，如[过滤器，参数](https://vizro.readthedocs.io/en/stable/pages/user-guides/filters/)或独立的[可导航页面](https://vizro.readthedocs.io/en/stable/pages/user-guides/navigation/)。

更重要的是，我可以和我的同事们一起合作修改仪表盘代码，以适应其他 Read the Docs 项目。我使用了一个 Jupyter Notebook 来方便演示这个项目，但这种方法同样适用于 Python 脚本，使得它在版本控制中既易于分享又易于维护。我还可以[部署仪表盘](https://vizro.readthedocs.io/en/stable/pages/user-guides/run/#deployment)，这样我的同事们就可以直接访问，而无需运行代码。

我们的团队现在有了一个实用且可用的仪表盘，用于跟踪文档的影响，这个仪表盘是由一位技术写作者在一个下午内完成的。还有什么比这更好的呢？

我想感谢我的同事们，特别是[Nadija](https://www.linkedin.com/in/nadija-ratkusic-graca-b044a143)和[Anna](https://github.com/Anna-Xiong)，以及[Joe,](https://www.linkedin.com/in/joseph-perkins-aa278272/)，感谢他们在我整理这篇文章时提供了多轮的反馈意见。
