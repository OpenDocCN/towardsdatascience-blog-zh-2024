# 如何在 5 分钟内构建自己的 Google AI 聊天机器人

> 原文：[https://towardsdatascience.com/how-to-build-your-own-ai-chatbot-within-5-minutes-using-google-llm-and-your-private-knowledge-eed4b6852917?source=collection_archive---------0-----------------------#2024-02-11](https://towardsdatascience.com/how-to-build-your-own-ai-chatbot-within-5-minutes-using-google-llm-and-your-private-knowledge-eed4b6852917?source=collection_archive---------0-----------------------#2024-02-11)

## 充分利用 Google LLM 和你的私人知识的力量

[](https://medium.com/@lizhuohang.selina?source=post_page---byline--eed4b6852917--------------------------------)[![Selina Li](../Images/8bc2e0079d72b4d23f9d903b24144acb.png)](https://medium.com/@lizhuohang.selina?source=post_page---byline--eed4b6852917--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--eed4b6852917--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--eed4b6852917--------------------------------) [Selina Li](https://medium.com/@lizhuohang.selina?source=post_page---byline--eed4b6852917--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--eed4b6852917--------------------------------) ·14 分钟阅读 ·2024年2月11日

--

作者：[Selina Li](https://medium.com/u/7b9ea39b0d79?source=post_page---user_mention--eed4b6852917--------------------------------)、[Tianyi Li](https://medium.com/u/4092d7367010?source=post_page---user_mention--eed4b6852917--------------------------------)

· [问题](#e714)

· [解决方案](#1b3c)

· [使用案例](#5489)

· [逐步指南](#59ba)

∘ [第 1 步：环境设置](#9037)

∘ [第 2 步：准备私人知识并将其存储到 Google 云存储（低代码）](#f3cd)

∘ [第 3 步：创建聊天机器人和聊天机器人背后的数据存储（无代码）](#4368)

∘ [第 4 步：测试聊天机器人（无代码）](#7842)

∘ [第 5 步：发布/集成聊天机器人（低代码）](#5ba1)

∘ [第 6 步（可选）：通过一个精美的应用发布它（低代码）](#eb61)

· [是什么让这变得“神奇”？](#c1a0)

· [一些观察](#d869)

· [总结](#9e33)

· [喜欢这个故事吗？](#1884)

# 问题

你可能已经熟悉了由大语言模型（LLM）驱动的 AI 聊天，如 OpenAI ChatGPT 或 Google Bard。而且你可能注意到一件事——这些 LLM 拥有广泛的世界通用知识，但当你询问一个非常具体或专业的领域时，它们可能无法给出令人满意的答案，尤其是当这个领域的知识并不公开或不易共享时。

你是否想过将你的私人知识“赋予”LLM，并创建自己的聊天机器人？

你知道吗，这可以在 5 分钟内通过无代码或低代码完成？

最终产品将是这样的：

![](../Images/43425b636db296b5f717c704821742cf.png)

Github 链接：[https://github.com/bianbianzhu/property-hunter](https://github.com/bianbianzhu/property-hunter)

# 解决方案

在亚太地区的 Google Cloud 应用 AI 峰会期间，**Alan Blount** 来自 **Google** 分享了一个有趣的想法，利用 Google Cloud Vertex AI 搜索与对话实现这一目标，我觉得这个方法非常吸引人，值得尝试。

这个想法很简单，首先将一批私有知识文档放到 Google Cloud Storage 上：

![](../Images/ef41c9002b1f774d04761323d723ee89.png)

然后创建一个数据存储，并将文档从 Cloud Storage 导入到数据存储中：

![](../Images/fb494f7b0897ae6aebf5ea2e54c71af5.png)

最后将数据存储接入 Dialogflow CX：

![](../Images/d1accd902a2ca0296ef96a71fff49740.png)

然后我们就完成了！

我们可以像这样测试聊天机器人：

![](../Images/acc5dc21a230ba1d9c21fb32d076d0c5.png)

如果我们想通过一个漂亮的应用程序发布它，Google 提供了一个公开的 Git 仓库，可以用于一个聊天应用程序。通过一点编程知识，我们可以将 Dialogflow 聊天机器人链接插入到这个聊天应用程序中，并自定义界面，像这样：

![](../Images/6b68e2456865c0acdd93a11b340e53a3.png)

或者是这个：

![](../Images/6c6a1713a41172745feeeb5b0d0a147d.png)

# 使用案例

在这个案例中，假设我是一个电商网站的所有者。我想创建一个聊天机器人，让我的用户可以在网站上提问关于任何商品（价格、产品、服务、运输等）的具体问题。聊天机器人将获得“私有知识”并基于网站内容回答问题。

鉴于我并没有真正拥有一个电商网站，我将采用一种变通方法，从互联网上现有的网站抓取内容。这有些棘手，因为大多数网站在其使用条款中声明反爬虫，并且抓取电商网站如 Amazon、eBay、Alibaba 等可能是非法的。

ChatGPT 给了我一个完美的选项 —

> 要抓取的书籍 ([https://books.toscrape.com/](https://books.toscrape.com/))。这是一个专门为网页抓取练习设计的模拟书店。它提供了一个简单的结构，用于抓取书籍的详细信息，如标题、价格和评分。

在这个使用案例中，我假设我是这个 [Books to Scrape 网站](https://books.toscrape.com/) 的所有者，并基于该网站创建聊天机器人。

# 步骤指南

一开始可能看起来有些冗长，因为它涵盖了你所需要的每个详细步骤。一旦你跑通了，你就能在 5 分钟内完成相同的操作。

## **步骤 1：环境设置**

我们将使用的工具位于 Google Vertex AI 上，我们需要一个 Google Cloud Platform (GCP) 账户。

Google 提供了一个 [免费套餐程序](https://cloud.google.com/free/docs/free-cloud-features#free-trial)，为新的 Google Cloud Platform (GCP) 用户提供为期 90 天的试用期，其中包括 $300 的免费云计费积分。

按照[这里的教程](https://medium.com/@lizhuohang.selina/create-a-free-tier-google-cloud-account-9f4303516a28)设置免费的**Google Cloud账户**。

在你设置好 Google Cloud 账户并能够访问控制台后，**创建一个存储桶**（[分步指南在这里](https://cloud.google.com/storage/docs/creating-buckets#create_a_new_bucket)）供下一步使用。

## **步骤 2：准备私有知识并将其存储到 Google Cloud Storage（低代码）**

如上所述，在此情况下的私有知识将是位于书店网站上的内容。

对于电商网站的所有者，你需要做的就是提供网站的 URL，Google 会自动从你定义的域名列表中抓取网站内容。

由于我不是实际的所有者，我将通过爬取解决此问题。来自 Google 的 Alan Blount 提供了[一个非常有用的笔记本](https://goo.gle/4847mcz)来实现这一点。所有的代码片段所做的就是从你指定的网站抓取网页并将其存储到你指定的 Google Cloud Storage 存储桶中。

这就是你需要做的全部：

**2.1 将笔记本保存到你自己的云盘中**

回想一下，在步骤 2 中，当你注册 Google Cloud 时，你创建了一个新的 Google 账户？你的 Google 账户将拥有[Google Drive](https://drive.google.com/drive/u/0/home)，你可以将这个笔记本保存到你的云盘中。

从“文件”下拉菜单中选择“在 Drive 中保存副本”选项

![](../Images/fd5f4b55322d2af0a812da6fa47b221b.png)

图片来自[Google Colab 笔记本](https://colab.research.google.com/gist/zeroasterisk/54e6caa81fc8dea0c629275f12463e5f/-flutter-vertex-ai-demo-crawl-a-website-and-save-the-html-files-to-google-cloud-bucket.ipynb?utm_source=events-with-google&utm_medium=et&utm_campaign=FY24-Q1-apac-EXP89-onlineevent-er-applied-ai-summit-2023-apac-mc&utm_content=apac_appaisummit_lp_2024_resources) 由 Alan Blount 提供

然后，如果你进入[Google Drive](https://drive.google.com/drive/u/0/home)，你将能够看到你创建的笔记本。根据需要自由重命名它。

![](../Images/4991dcbe134654bcd5e1f935c794a203.png)

**2.2 在你自己的笔记本中，定位以下内容并指定**

![](../Images/78797a6185557f76579a72b238276df1.png)

图片来自 Google Colab 笔记本

`website_url` 指的是你想要抓取的网页 URL。

`storage_bucket` 指的是你在步骤 1 中创建的 Google Cloud Storage。

`metadata_filename` 指的是将与网页一起创建并存储的 JSON 文件。你可能需要通过将 `applied_ai_summit_flutter_search` 改为能够描述你的使用场景的名称来使其与网站相关。

这是我的版本：

![](../Images/852466c2662e2ff8f9b098cf809280f4.png)

图片来自 Google Colab 笔记本

**2.3 运行所有**

![](../Images/a3ddf0b6c7e615dab7fd751a6cf39d12.png)

图片来自 Google Colab 笔记本

**2.4 当系统提示你授权 Google Colab 笔记本访问你的 Google 凭证时，点击“允许”->“继续”**

![](../Images/e9f5054915992ec2a7fb33474c87e91a.png)

来自 Google Colab 笔记本的图像

然后，脚本应该会运行，并在底部显示抓取进度，就像这样：

![](../Images/243dc44d573ffe7e5e813866425dae78.png)

来自 Google Colab 笔记本的图像

如果你查看 Google Cloud 存储桶，你会看到这些 HTML 文件被正确抓取并存储在你的存储桶中：

![](../Images/1958b429f4b9a1dfe1b2cda49c66baa1.png)

来自 Google Cloud 控制台的图像

需要注意的是，代码片段并非为所有用例设计，你可能需要对代码进行一些微调，以达到你的目标。

例如，在我的案例中，我通过修改代码进行了一些调整

```py
blob.upload_from_string(html_string)
```

转换为

```py
blob.upload_from_string(html_string, content_type='text/html')
```

默认情况下，`html_string` 将作为 `text/plain` 上传。通过将其改为 `text/html`，我希望能够在后续阶段正确显示这些 HTML 内容。

你可以根据需要调整代码。

## **步骤 3：创建聊天机器人以及其背后的数据存储（无需代码）**

前往 Google Cloud 控制台 ([https://console.cloud.google.com/](https://console.cloud.google.com/)) 并输入“搜索与对话”作为服务：

![](../Images/cdc2fa2c03fe7366995674b632080c99.png)

创建“新应用”：

![](../Images/23fb1e16321fa04a0763e1412706040e.png)

来自 Google Cloud 控制台的图像

选择“聊天”：

![](../Images/a9bdcf2828414350c09b17a39ffcfa3a.png)

来自 Google Cloud 控制台的图像

提供你的“公司名称”和“代理名称”。请注意，这里的**“代理名称”**将是**聊天机器人的名称**，你可能想为你的用户取个好名字。

![](../Images/7de2b5b1cef6e653803f3fc9e071c515.png)

来自 Google Cloud 控制台的图像

在此“数据”页面，选择“创建新数据存储”：

![](../Images/4a7c4131fcc6fb7d1fd7022478b60752.png)

来自 Google Cloud 控制台的图像

对于电子商务网站的拥有者，选择“网站 URL”并配置你的网站 URL

由于我已经将网站内容抓取到 Cloud Storage，我们可以在此选择“Cloud Storage”：

![](../Images/26f6021d1fbe20f93b4e51f4c1fbf0f5.png)

来自 Google Cloud 控制台的图像

指定 Cloud Storage 存储桶名称，并在下方选择“非结构化文档”：

![](../Images/007f40ea225ea53e2f09824696bccb9f.png)

来自 Google Cloud 控制台的图像

给你的数据存储命名，然后点击“创建”

![](../Images/cb47148fdce7687e914f5e42e94ef37c.png)

来自 Google Cloud 控制台的图像

你会看到数据存储列出，然后点击“创建”

![](../Images/263ca3f4e929d8484d3e6affe02f50e6.png)

来自 Google Cloud 控制台的图像

你的数据存储将如下所示创建

![](../Images/f3a674367d1e83604ab947a730fdffdb.png)

来自 Google Cloud 控制台的图像

如果你点击进去，你会看到你的数据存储正在“处理数据”，并从我们之前指定的 Cloud Storage 存储桶中导入文档：

![](../Images/5e4d7f67be0634518a6421f5aab1f710.png)

来自 Google Cloud 控制台的图像

如果我们点击“ACTIVITY”标签，我们可以看到导入正在进行中：

![](../Images/f185ddec47ae70cf303ae4d317ff3bf6.png)

来自 Google Cloud 控制台的图片

导入过程可能需要几分钟到几小时，具体取决于你 Cloud Storage 存储桶中的文档数量。

在我的情况下，我有超过1000个文件，几分钟内就完成了。

导入完成后，突出显示的状态已发生变化：

![](../Images/d2d95cd21050dbab5288a33a8627fa5d.png)

来自 Google Cloud 控制台的图片

如果你切换回“文档”标签，你将看到导入到数据存储中的文件列表：

![](../Images/5e45c1fa781b8ae0197c8fd6f51382df.png)

来自 Google Cloud 控制台的图片

这意味着你已经准备好所有材料，可以开始工作了！

## 步骤 4：测试聊天机器人（无代码）

在上面的第3步中，我们已经创建了一个聊天机器人应用程序以及其背后的数据存储。

点击顶部的“应用”：

![](../Images/7450fedd30a09bc504966d67b4733843.png)

来自 Google Cloud 控制台的图片

你将看到你在第3步中创建的聊天机器人：

![](../Images/00b8eead5847c59b5fed9fb8485fb09d.png)

来自 Google Cloud 控制台的图片

如果你点击聊天机器人名称，你将被引导到类似下面的 Dialogflow CX 页面：

![](../Images/2a7e6e5f22342f2be8407396c90ad25b.png)

来自 Google Cloud 控制台的图片

要测试聊天机器人，选择右上角的“测试代理”：

![](../Images/2caf01d59ef23194b0031248b5cfe325.png)

来自 Google Cloud 控制台的图片

然后对话框会弹出：

![](../Images/0bfcf164f96e4f7c25ffec31b04d1d39.png)

来自 Google Cloud 控制台的图片

你可以通过说“hi”并开始向聊天机器人提问来启动对话：

![](../Images/49719ee09a923fcf2fac33803a903ec1.png)

来自 Google Cloud 控制台的图片

它工作正常！

## 步骤 5：发布 / 集成你的聊天机器人（低代码）

如果你对聊天机器人满意，可以轻松地将其与你的 web 应用程序集成

进入左侧面板，选择“管理” -> “集成” -> “Dialogflow Messenger”

![](../Images/0773340e4495d08c40822981036748b2.png)

来自 Google Cloud 控制台的图片

你可以根据自己的需求选择 API 类型和 UI 风格

为了演示，我选择了“未认证的 API”作为 API 和“弹出式”作为 UI 风格：

![](../Images/a29403b6a78b67cac4c9de6dea005d10.png)

来自 Google Cloud 控制台的图片

选择“完成”后，下一页会生成如下的HTML代码片段：

![](../Images/8635dec907fe99d3b43227079a7cf16d.png)

来自 Google Cloud 控制台的图片

你可以复制代码片段并轻松将其粘贴到你的应用程序中进行集成。

为了演示，我将这个 HTML 代码片段复制并粘贴到 [JSFiddle](https://jsfiddle.net/) 中运行，然后我看到我的小聊天机器人在右下角正常工作！

![](../Images/65bb7fe604bec0cf0d4394f324f190b8.png)

来自 [JSFiddle](https://jsfiddle.net/) 的图片

## **步骤 6（可选）：通过美观的应用程序发布（低代码）**

如果您还没有应用程序并希望创建一个，Google 提供了一个很好的起点，通过一个公开的 Git 仓库 [聊天应用](https://github.com/GoogleCloudPlatform/generative-ai/tree/main/conversation/chat-app)。

这是一个用 Node.js 编写的聊天机器人应用程序，您可以通过稍微修改 `chat-app/src/routes/+page.svelte` 中的代码片段，轻松将其适配到您自己的需求。

您需要将 `project-id`、`agent-id` 和 `chat-title` 更改为您自己的。

![](../Images/4b3fe26c95da711768cf3af30a354c3e.png)

该图片来自 Git 仓库 [https://github.com/GoogleCloudPlatform/generative-ai/tree/main/conversation/chat-ap](https://github.com/GoogleCloudPlatform/generative-ai/tree/main/conversation/chat-app)

一旦您运行/部署该应用程序，您将获得如下所示的 Web UI：

![](../Images/c6c931e4ab087b0f9dec6bb65bf71225.png)

该图片来自 Git 仓库 [https://github.com/GoogleCloudPlatform/generative-ai/tree/main/conversation/chat-app](https://github.com/GoogleCloudPlatform/generative-ai/tree/main/conversation/chat-app)

当然，您可以根据需要更改 UI 的外观。

现在，您可以拥有自己的应用程序了！

# 是什么让这个“魔法”得以实现？

回想我们在开头提到的解决方案设计。看起来有点像魔法，因为您只需要将您的私人知识提供给 Google Cloud Storage 存储桶，就能轻松获得自己的 LLM 驱动的聊天机器人。

![](../Images/d1accd902a2ca0296ef96a71fff49740.png)

这一切的实现得益于 Google 在幕后做了大量的集成工作，通过将 **Vertex AI 平台** 与聊天机器人代理服务 **Dialogflow CX** 集成，并推出了一个新的抽象层，称为 **Vertex AI 对话**（前身为 Gen 应用构建器）。这个新的抽象层还支持 **搜索** 和 **推荐**，该服务的全名为“**Vertex AI 搜索与对话**”。

正如我们所见，这种新的“Vertex AI 搜索与对话”的抽象位于 Vertex AI 之上，后者协调了多个基础模型，并通过用户提供的最新真实世界信息进行“增强”，使其能够将这些信息纳入其回应的上下文。

![](../Images/98e515df9473082c7f1482959086d777.png)

该图片来自 Google Cloud CEO 的演讲幻灯片 [生成式 AI：开发者的下一个前沿](https://youtu.be/L-UKYBPJTWE?si=zJR9gJs7YTH_JiJ1)

该集成非常出色，因为它可以帮助至少两类人群 —

1.  传统的聊天机器人构建者，以及

1.  人们在探索生成式 AI 解决方案，但还未找到合适的应用场景

想象你是一个使用Dialogflow CX的**传统聊天机器人构建者**，你在创建页面、意图和路由，将客户意图路由到相应的页面。基本上，你在定义“如果客户说这个，那么我用这个回应”，这有点像硬编码。现在，谷歌将Vertex AI接入进来，它可以利用LLM模型（例如text-bison、gemini）生成智能的代理回应，并控制对话流程。这可以显著减少代理设计时间，并提高代理质量。

另一方面，想象你在**探索LLM和生成式AI的强大功能，但不确定如何使用它**。这个**Vertex AI 对话**功能可以让你轻松构建并快速启动自己的聊天机器人应用，并使其适用于实际用例。这可以显著缩短LLM和生成AI解决方案的上市时间。

# 一些观察

尽管看起来有些“魔力”，我们观察到一些值得与考虑使用“**Vertex AI 搜索与对话**”功能的开发者分享的事项。

我们的直觉是，这是谷歌通过“整合”几个现有工具推出的一款新产品，并且仍在努力使其变得更好。集成如何在幕后发生，以及开发者如何理解和配置它，仍然不够清晰。

我很快就得到了我们的聊天机器人，但一旦开始查看如何微调它，我花了相当多的时间来弄明白Dialogflow CX是如何工作的，什么是“生成器”，以及它是如何工作的。此刻，我仍然困惑为什么这个聊天机器人在我甚至没有配置任何[谷歌文档中描述的“生成器”](https://cloud.google.com/dialogflow/cx/docs/concept/generative/generators)的情况下运作得如此出色，是否/如何通过使用“生成器”能使它变得更好。

开发过程中还有一些其他观察：

+   索引一个网站或一组文档可能需要几分钟或几天，具体取决于数据量。对此过程所需时间没有明确估计，开发者能做的就是等待并定期检查。

+   我们知道如何将数据存储与聊天机器人应用连接，但看起来我们无法“断开”连接。

+   尽管有一定的基础，用户提供的数据质量仍然会显著影响聊天机器人的性能。“垃圾进，垃圾出”依然在很大程度上适用。

+   通过提供私人数据和知识来“增强”可以解决大型语言模型（LLM）的一个问题——缺乏更新的现实世界信息。但幻觉问题依然存在，因为有时聊天机器人可能会给出“虚假”信息（当然，这取决于你提供的私人知识的数据质量）。

+   聊天机器人在与用户聊天时会提供相关网页/文档页（例如 PDF）的链接。这很棒，但聊天机器人提供的链接是 Google Cloud Storage 授权的 URL，仅能被具有授权权限的用户访问。开发人员需要找出如何将这些 URL 转换为签名 URL，这样可以安全地与公众匿名用户分享，而不是使用 Google Cloud Storage 授权的 URL。

+   支撑聊天机器人的数据存储最适合处理非结构化数据。对于结构化数据，它支持连接到 CSV 格式的结构化数据，但必须采用 Google 文档中提到的“问题”和“答案”格式：

![](../Images/5afdef6df1cace674c95147b7bfb630f.png)

图片来自 Google Cloud 控制台

![](../Images/ba0455873dd78c1f5dfeaec6cb327dfd.png)

图片来自 [Google Cloud Dialogflow 指南](https://cloud.google.com/dialogflow/vertex/docs/concept/data-store#structured)

# 总结

在上述用例中，我假设自己是一家在线书店的老板，并根据我的电子商务网站内容（HTML 格式）创建了一个聊天机器人。

同样，您可以将“私人知识”以 **博客**、**文件（例如 PDF、HTML、TXT）** 和 **各种网站** 的格式提供给 Google Cloud Storage，并创建自己的聊天机器人。

这使得个人/企业能够充分利用 Google LLMs（如 text-bison、gemini 等）的强大功能，将其与私人知识结合，并以非常快速的方式创建自己的聊天机器人。

本文到此结束。希望您觉得它有帮助！

（附言：我正在制作一段视频，以便让这个逐步指南更易于跟随。如果完成的话，我会在不久的将来分享。）

# 喜欢这个故事吗？

**Selina Li** ([Selina Li](https://medium.com/u/7b9ea39b0d79?source=post_page---user_mention--eed4b6852917--------------------------------), [LinkedIn](https://www.linkedin.com/in/selina-zhuohang-li-3b7355120/)) 是一位首席数据工程师，现工作于澳大利亚墨尔本的[Officeworks](https://www.officeworks.com.au/)。Selina 热衷于人工智能/机器学习、数据工程和投资。

**Jason Li** ([Tianyi Li](https://medium.com/u/4092d7367010?source=post_page---user_mention--eed4b6852917--------------------------------), [LinkedIn](https://www.linkedin.com/in/tianyi-li-jason/)) 是一位全栈开发工程师，现工作于澳大利亚墨尔本的[Mindset Health](https://www.mindsethealth.com/)。Jason 热衷于人工智能、前端开发以及与太空相关的技术。

Selina 和 Jason 很乐意探索各种技术，帮助人们实现他们的目标。

*除非另有说明，所有图片均由作者提供。*
