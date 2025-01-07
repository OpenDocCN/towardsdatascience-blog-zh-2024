# 像专业人士一样演示 AI 产品

> 原文：[https://towardsdatascience.com/demo-ai-products-like-a-pro-785a2d9f2d63?source=collection_archive---------7-----------------------#2024-05-07](https://towardsdatascience.com/demo-ai-products-like-a-pro-785a2d9f2d63?source=collection_archive---------7-----------------------#2024-05-07)

## 一份关于如何使用 Gradio 向专家和非技术观众展示产品价值的专家指南简介。

[](https://medium.com/@josephgeorgelewis2000?source=post_page---byline--785a2d9f2d63--------------------------------)[![Joseph George Lewis](../Images/2d7c47bd9a323dd0f4a6b610e7fb08fd.png)](https://medium.com/@josephgeorgelewis2000?source=post_page---byline--785a2d9f2d63--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--785a2d9f2d63--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--785a2d9f2d63--------------------------------) [Joseph George Lewis](https://medium.com/@josephgeorgelewis2000?source=post_page---byline--785a2d9f2d63--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--785a2d9f2d63--------------------------------) ·15分钟阅读·2024年5月7日

--

![](../Images/9660be088f4c71b28a709809fc9ddc34.png)

图片来自 [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们每个人至少都经历过一次失败的演示。在数据科学领域，这个问题尤为突出，因为在演示当天可能会出现很多问题。数据科学家在向不同经验水平的观众演示时，通常需要平衡各种挑战。如何在面向广泛观众时，既展示解决方案的价值，又解释核心概念，往往是一个挑战。

本文旨在帮助克服障碍，帮助你展示你的辛勤工作！我们总是非常努力地改进模型、处理数据和配置基础设施。理应我们也要努力确保别人能看到这项工作的价值。我们将探索如何使用 Gradio 工具来分享 AI 产品。Gradio 是 Hugging Face 生态系统中的一个重要部分。Google、Amazon 和 Facebook 等公司也在使用它，因此你将与这些大公司同行！虽然我们将使用 Gradio，但许多核心概念可以在常见的替代工具中复制，比如使用 Python 的 StreamLit 或使用 R 的 Shiny。

## 利益相关者/客户参与在数据科学中的重要性

推销时的首个挑战是确保你以正确的层次进行推介。为了理解你的 AI 模型是如何解决问题的，客户首先需要了解它的功能，以及它所解决的问题。客户可能拥有数据科学的博士学位，或者他们可能从未听说过模型。你不需要教他们线性代数，也不应通过白皮书讲解你的解决方案。你的目标是向所有观众传达你的解决方案所带来的附加价值。

这是一个实用演示的场景。**Gradio** 是一个轻量级的开源工具包，用于制作实用演示[1]。有充分的文献表明，现场演示往往更具个人化，能够促进对话/产生新线索[2]。**实用演示在与新用户建立信任和理解方面至关重要**。信任来源于看到你使用工具，甚至更好的是通过用户自己输入进行测试。当用户可以进行工具演示时，他们知道没有“聪明汉斯效应”[3]，他们看到的就是他们得到的。理解来自于用户看到你的解决方案如何运作中的“如果-那么”模式。

接下来是反面…每个人都参加过糟糕的现场演示。我们都曾经历过或让别人经历过技术问题。

但技术问题并不是我们对现场演示感到恐惧的唯一原因。还有一些常见的让人反感的因素：

+   **信息倾销**：向客户推销时绝不应像一场讲座。添加难以访问的演示会让客户在短时间内接收到过多信息，造成学习压力过大。

+   **开发演示**：演示的开发可能很慢，实际上可能会减慢开发进程。定期在“展示与讲解”中反馈是敏捷团队面临的一个特定问题。获取展示与讲解的内容可能是一个折磨。尤其是当客户习惯了现场演示时。

+   **断开的依赖关系**：如果你负责开发演示，可能会依赖一些保持不变的事物。如果它们发生变化，你就需要重新开始。

## 介绍 Gradio

现在进入技术部分。Gradio 是一个用于展示机器学习/AI 模型的框架，并与 Hugging Face 生态系统的其他部分集成。该框架可以通过 Python 或 JavaScript SDK 实现。这里我们将使用 Python。在我们构建演示之前，下面是一个命名实体识别的 Gradio 应用示例：

![](../Images/f978c0de3422eea0c51b1308d0284395.png)

图片来源：Hugging Face 文档 [4]

你可以在当前工作的任何地方实现 Gradio，这是使用该框架的一个关键优势。如果你在笔记本中快速进行代码原型设计，并希望立即从利益相关者/同事那里获取反馈，你可以添加 Gradio 界面。**根据我使用 Gradio 的经验，我曾在 Jupyter 和 Google Colab 笔记本中实现过。**你还可以通过 HuggingFace 托管的公共链接将 Gradio 实现为独立站点。稍后我们将探讨部署选项。

Gradio 演示帮助我们解决了上述问题，并帮助我们克服了对现场演示的恐惧：

+   **信息泛滥**：Gradio 提供了一个简单的接口，抽象掉了许多复杂的信息。客户不会在一次性了解如何与我们的工具交互**和**工具本身是什么的过程中感到不堪重负。

+   **开发演示**：Gradio 演示和 StreamLit、Shiny 有相同的优点。演示代码简单，并且建立在你已经为产品编写的 Python 代码之上。这意味着你可以快速做出修改并获得即时反馈。你还可以从客户的角度查看演示。

+   **断裂的依赖**：没有任何框架能够克服完全的项目大改。Gradio 被设计为能够适应新的数据、数据类型甚至新的模型。由于允许的输入/输出的简单性和范围，Gradio 演示保持得相当稳定。不仅如此，如果你有很多工具、很多客户和很多项目，好消息是，大部分演示代码不会发生变化！你可以将文本输出换成图像输出，就可以轻松地从 LLM 转到 Stable Diffusion！

## 使用 Gradio 创建演示的逐步指南

本文的实践部分将带你从完全初学者成长为 Gradio 演示专家。话虽如此，有时候少即是多，如果你只需要一个非常简单的演示来突出展示你工作的影响，那就坚持基础吧！

欲了解更多关于 StreamLit 等替代品的信息，查阅我之前的文章：

[](https://python.plainenglish.io/building-lightweight-geospatial-data-viewers-with-streamlit-and-pydeck-de1e0fbd7ba7?source=post_page-----785a2d9f2d63--------------------------------) [## 使用 StreamLit 和 PyDeck 构建轻量级地理空间数据查看器

### 使用两种前沿库构建和部署 Python 的交互式 web 应用，用于地理空间数据可视化。

[python.plainenglish.io](https://python.plainenglish.io/building-lightweight-geospatial-data-viewers-with-streamlit-and-pydeck-de1e0fbd7ba7?source=post_page-----785a2d9f2d63--------------------------------)

**基础知识**

让我们从一个 Hello World 风格的示例开始，这样我们可以更多地了解构成 Gradio 演示的基本要素。我们有三个基本组件：

1.  输入变量：我们提供任何数量的输入变量，用户可以使用切换开关、滑块或其他输入小部件在我们的演示中输入。

1.  功能：演示的作者编写一个执行重活的函数。这是演示之间代码变化**最多**的地方。该函数会将输入变量转化为用户看到的输出。这是我们可以调用模型、转换数据或执行其他必要操作的地方。

1.  接口：接口将输入变量、输入小部件、功能和输出小部件结合到一个演示中。

那么让我们看看这在代码中的表现：

这将给我们以下演示。注意，我们定义的输入和输出都是文本类型，如上所述：

![](../Images/29a50a435e096a72a026dd978ab32828.png)

图片来源：作者提供

现在我们了解了 Gradio 的基本组件，让我们来深入一点，探讨一些技术细节。

为了演示如何将 Gradio 应用到机器学习问题中，我们将使用最简单的算法——线性回归。作为第一个示例，我们将使用加利福尼亚房价数据集来构建线性回归模型。首先，我们更新基本代码，使得该函数能够基于线性模型进行预测：

然后我们更新界面，使得输入和输出与我们的需求相匹配。请注意，我们在这里也使用了**Number**类型作为输入：

然后点击运行，看看效果如何：

![](../Images/8a5fed64fc3219ad8d38670010e3dfac.png)

图片来源：作者提供

为什么停下来呢！我们可以在 Gradio 中使用**Blocks**，使我们的演示更加复杂、深入且引人入胜。

**控制界面**

Blocks 就如其名，是 Gradio 应用的构建**块**。到目前为止，我们只使用了更高级的**Interface**封装。在下面的示例中，我们将使用 Blocks，它具有稍微不同的编码模式。让我们更新最后一个示例，使用**Blocks**，以便更好地理解它们的工作原理：

与之前有输入、函数和界面不同，现在我们已经将所有内容简化到 Gradio 的最基本形式。我们不再设置界面并要求它为我们添加数字输入！现在，我们提供每个单独的数字输入和一个数字输出。这样构建使我们能够对显示进行更多的控制。

通过这种新的控制方式，我们甚至可以添加新的标签。标签使我们能够控制用户的操作流程和体验。我们可以先解释一个概念，比如我们的预测是如何分布的。然后在下一个标签中，我们可以为用户提供一个全新的区域，让他们自己向模型提问，获取预测结果。我们还可以利用标签来应对技术难题。第一个标签为用户提供了很多关于模型性能的信息。这些都是通过之前实现的功能完成的。如果模型代码当天无法运行，我们依然有一些有价值的信息可以分享。虽然不完美，但总比空白屏幕强！

**注意：这并不意味着我们可以通过标签隐藏技术难题！我们只是可以利用标签为观众提供一些线索，万一出现问题时可以参考。如果技术问题解决后，我们再重新分享演示。**

![](../Images/7e59515adde18648612aaddc49612081.png)

图片来源：作者提供

提高复杂度展示了 Gradio 在展示各种信息时的强大功能！到目前为止，我们一直保持着一个相对简单的模型。现在让我们探索一下如何在更复杂的场景中使用 Gradio。

**Gradio 用于 AI 模型和图像**

接下来的应用将展示如何使用 Gradio 演示生成式 AI。我们再次将使用 Blocks 来构建界面。这次演示将包含两个核心组件：

1.  一个介绍标签，解释模型的限制以及适用范围内外的使用。

1.  一个灵感标签，展示之前生成的一些图像。

1.  一个互动标签，用户可以提交提示生成图像。

在这篇博客中，我们仅演示一个预训练的模型。要了解更多关于Stable Diffusion模型的信息，包括关键概念和微调，查看我之前的博客：

[](https://levelup.gitconnected.com/stable-diffusion-how-ai-converts-text-to-images-68943171bd8a?source=post_page-----785a2d9f2d63--------------------------------) [## Stable Diffusion：AI 如何将文本转换为图像

### 记录我学习并亲自操作 Stable Diffusion 的过程，供其他学习者参考。

[levelup.gitconnected.com](https://levelup.gitconnected.com/stable-diffusion-how-ai-converts-text-to-images-68943171bd8a?source=post_page-----785a2d9f2d63--------------------------------)

由于这是一个演示，我们将从最难的组件开始。这确保我们有足够的时间来完成最困难的工作。互动标签可能是最具挑战性的部分，因此我们将从这里开始。为了让我们知道目标是什么，我们的演示页面最终看起来会像这样：

![](../Images/b8baf45c751ee03c3bcb95dcb66f10b8.png)

图片来源：作者提供的图片。Stable Diffusion 图像是由 AI 生成的。

为了实现这一点，演示代码将结合上面两个示例。我们将使用块、函数、输入和**按钮**。按钮使我们能够以类似之前的方式工作，其中我们有输入、输出和函数。我们将按钮作为**事件监听器**。事件监听器有助于控制我们的逻辑流程。

假设我们正在尝试启动我们的演示。在运行时（即演示开始时），我们没有输入。由于没有输入，演示使用的模型没有提示。没有提示，模型无法生成图像。**这将导致错误**。为了克服错误，我们使用**事件监听器**。按钮监听一个事件，在这种情况下是按钮点击事件。一旦它“听到”事件，或者被点击，它会触发一个动作。在这个例子中，动作将是将一个完成的提示提交给模型。

让我们回顾一些使用按钮的新代码，并将其与之前的**界面**示例进行对比：

按钮代码看起来像界面代码，但有一些大的概念变化：

1.  按钮代码使用了块。这是因为虽然我们像**界面**一样使用按钮，但我们仍然需要某种方式来确定演示的外观。

1.  输入和输出小部件作为对象使用，而不是字符串。如果你回到第一个示例，我们的输入是**“text”**类型为*string*，但这里它是**prompt**类型为*gr.Text()*。

1.  我们使用 [*button.click*](http://button.click)*()* 而不是 *Interface.launch()*。这是因为之前界面是我们的整个演示。这一次，事件是按钮点击。

这是演示的最终效果：

![](../Images/2132eea144bcbfca0ae1ce07e99f7225.png)![](../Images/10c685bb515e7a68a427dcfb73809bbf.png)

图片来源：作者提供。Stable Diffusion 图像为AI生成。

你能看到事件监听器的重要性吗！它为我们节省了很多工作，确保事情按正确的顺序发生。Gradio的美妙之处在于，我们还能够得到一些反馈，了解我们需要等待多久才能看到图片。左侧的进度条和时间信息对于用户反馈和互动非常有帮助。

演示的下一部分是分享我们之前生成的图片。这将为客户提供灵感。他们将能够看到工具能做什么。为此，我们将实现另一个新的输出小部件，**图库**。图库显示了我们刚刚生成的图片：

**一个重要的提醒**：我们实际上是利用了之前的*generate_images()*函数。正如我们之前所说，所有这些轻量级的应用程序库使我们能够简单地在现有代码的基础上构建。

演示现在看起来是这样的，用户可以在两个核心功能之间切换：

![](../Images/a0c4e5fd9735cafd05a38e91a11fe0a9.png)

图片来源：作者提供。Stable Diffusion 图像为AI生成。

最后，我们将通过一个着陆页将一切联系在一起。在现场或录制的演示中，着陆页将为我们提供讨论的内容。它很有用，但不是必须的。我们包括着陆页的主要原因是为了**那些没有我们在场的情况下测试工具的用户**。这有助于提高工具的可访问性，并增加用户的信任和理解。如果你每次都需要在客户使用你的产品时在场，那就无法产生价值。

这次我们不会使用任何新的东西。相反，我们将展示*Markdown()*组件的强大功能。你可能已经注意到我们已经使用了一些Markdown。对于那些熟悉的人来说，Markdown可以帮助以文本形式表达各种信息。下面的代码包含了一些想法，但在你的演示中，发挥创意，看看你能将Markdown在Gradio中应用到多远：

![](../Images/194507b2071a3fd7cf000686584d7480.png)

图片来源：作者提供

完成的演示如下。欢迎在评论中告诉我你的想法！

![](../Images/bb4a4b5f04dff429b7705cb6a06e8863.png)![](../Images/2f07696ad3cd718c3ffd703ccbf65ebc.png)![](../Images/bc04001d4371fc82f9de7772d42329ce.png)

图片来源：作者提供。Stable Diffusion 图像为AI生成。

## 与客户分享

无论你是经验丰富的专业人士，还是刚开始的初学者，分享演示都可能让人感到畏惧。构建演示和推介是两项非常不同的技能。到目前为止，本文已经帮助你构建了演示。网络上有很多资源可以帮助推介[5]。现在让我们专注于这两者的交集——如何有效地分享你所构建的演示。

考虑到你偏好的风格，现场演示绝对能让你的展示更加生动有趣（笑话自带！）。对于技术观众，我们可以直接在笔记本中启动演示。这对于那些想深入了解代码的人很有用。我推荐将这种方式分享给新同事、高级开发人员以及任何希望合作或扩展你工作的人员。如果你使用的是 Gradio 以外的工具，我仍然推荐以高层次的方式与这个观众分享代码。这有助于吸引新开发人员加入，或向高级开发人员解释你最新的变化。

另一种方法是只使用“前端”进行现场演示。这可以通过运行演示时提供的链接来完成。通过这种方式分享时，客户无需深入代码就能看到你的演示。这就是迄今为止截取屏幕截图的方式。我推荐这种方式用于现场的非技术观众、新客户以及敏捷反馈/展示会议。如果你在 Gradio 中构建了演示，可以通过提供的链接访问这个功能。

我们可以用来分享的链接还允许我们将演示分享给其他人。通过在启动演示时设置**分享**参数：

```py
demo.launch(debug=True, share=True)
```

这种方式对于无法参加直播会议的用户非常有效，或者那些希望有更多时间进行产品实验的用户也很适用。此链接可用 72 小时。**此时需要小心，因为演示是通过你的机器公开托管的**。建议在以这种方式分享之前，考虑系统的安全性。我们可以通过设置密码保护来使其更加安全：

```py
demo.launch(debug=True, auth=('trusted_user', 'trusted123'))
```

这会为演示添加一个密码弹出窗口。

你可以进一步通过使用授权技术来增强安全性。例如，使用 Hugging Face 或 Google 的 OAuth 身份提供者 [6]。还可以通过设置文件和主机机器上文件路径的限制来进一步加强保护 [6]。

*这种方式并不能完全解决分享时的安全问题。如果你想要私密分享，使用云提供商的容器化可能是一个更好的选择* [7]。

如果你想让更多人参与，你可能会想将演示公开分享给在线观众。这对寻找潜在客户、建立口碑或获取最新 AI 项目的反馈非常有帮助。我多年来一直在 Medium、Kaggle 和 GitHub 上公开分享作品获取反馈。通过这些反馈，我的作品确实有了显著改善。

如果你使用的是 Gradio，演示可以通过 Hugging Face 公共分享。Hugging Face 提供的**Spaces**用于分享 Gradio 应用。Spaces 提供了一个免费的平台来分享你的演示。GPU 实例有相关费用（每小时 $0.40 至 $5）。有关分享到 Spaces 的文档请参见 [6]。文档解释了你可以如何：

+   分享到 Spaces

+   使用 GitHub Actions 实现空间的 CI/CD

+   将 Gradio 演示嵌入你自己的网站中！

Spaces 对于覆盖更广泛的受众非常有帮助，而不需要担心资源问题。它也是一个为潜在客户提供的永久链接。这使得提供尽可能多的指导显得尤为重要。再次提醒，这是一个你不拥有的计算平台上的公共共享平台。如果有更高的安全需求，容器化和专用托管可能更为合适。一个特别好的例子是这个 Minecraft 皮肤生成器[8]。

![](../Images/3ac10b3136b8000af1cd05ac8407dcd2.png)

图片来源：Nick088，Hugging Face [[Stable Diffusion Finetuned Minecraft Skin Generator — a Hugging Face Space by Nick088](https://huggingface.co/spaces/Nick088/Stable_Diffusion_Finetuned_Minecraft_Skin_Generator)]

## 额外的考虑因素

当前整个 AI 社区中最受关注的话题当然是大型语言模型（LLMs）。Gradio 提供了许多专为 LLM 设计的组件。这包括使用代理工作流和模型即服务[9]。

还值得一提的是**自定义组件**。自定义组件是由其他数据科学家和开发者开发的。它们是建立在 Gradio 框架之上的扩展。以下是一些很棒的例子：

+   图像注释组件：[gradio_image_annotation V0.0.6 — a Hugging Face Space by edgargg](https://huggingface.co/spaces/edgargg/gradio_image_annotation)

+   使用上传的 PDF 进行问答：[gradio_pdf V0.0.6 — a Hugging Face Space by awacke1](https://huggingface.co/spaces/awacke1/Gradio-PDF-Document-QA-PDF-UI)

扩展并非 Gradio 独有。如果你选择使用 StreamLit 或 Shiny 来构建演示，这些框架也有很棒的扩展：

+   StreamLit 附加组件，StreamLit UI 组件的扩展：[https://extras.streamlit.app/](https://extras.streamlit.app/)

+   超棒的 R Shiny，Shiny 的附加反应式/UI/主题组件：[https://github.com/nanxstats/awesome-shiny-extensions](https://github.com/nanxstats/awesome-shiny-extensions)

最后谈谈在敏捷背景下分享工作。当通过定期的展示和反馈会议分享时，轻量级的演示可以改变游戏规则。从 MVP 到最终产品的轻松层叠能力，确实能帮助客户看到他们与产品的旅程。

总结来说，Gradio 是一个轻量级的开源工具，用于共享 AI 产品。根据你的需求，可能需要考虑一些重要的安全步骤。希望你在准备演示时感觉更有准备！

如果你喜欢这篇文章，请考虑关注我、分享这篇文章或留下评论。我写了许多关于数据科学领域的内容，欢迎查看我个人主页上的更多内容。

## 参考文献

[1] Gradio 文档。[https://www.gradio.app/](https://www.gradio.app/)

[2] 用户引导产品演示。[https://userpilot.com/blog/product-demos/](https://userpilot.com/blog/product-demos/)

[3] 聪明汉斯维基百科。[https://en.wikipedia.org/wiki/Clever_Hans](https://en.wikipedia.org/wiki/Clever_Hans)

[4] Gradio 命名实体识别应用。[命名实体识别 (](https://www.gradio.app/guides/named-entity-recognition)[gradio.app](http://gradio.app)[)](https://www.gradio.app/guides/named-entity-recognition)

[5] 《哈佛商业评论》。什么是一个优秀的推介。[What Makes a Great Pitch (](https://hbr.org/2020/05/what-makes-a-great-pitch)[hbr.org](http://hbr.org)[)](https://hbr.org/2020/05/what-makes-a-great-pitch)

[6] Gradio 部署到 Spaces。[分享你的应用 (](https://www.gradio.app/guides/sharing-your-app#authentication)[gradio.app](http://gradio.app)[)](https://www.gradio.app/guides/sharing-your-app#authentication)。

[7] 将 Gradio 部署到 Docker。[使用 Docker 部署 Gradio](https://www.gradio.app/main/guides/deploying-gradio-with-docker)

[8] 神奇的 Minecraft 皮肤生成器示例。[Stable Diffusion 微调版 Minecraft 皮肤生成器——Nick088 在 Hugging Face 上的空间](https://huggingface.co/spaces/Nick088/Stable_Diffusion_Finetuned_Minecraft_Skin_Generator)

[9] Gradio for LLM。[Gradio 和 LLM 代理](https://www.gradio.app/guides/gradio-and-llm-agents)
