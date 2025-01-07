# AIML产品UX最佳实践

> 原文：[https://towardsdatascience.com/best-practices-for-aiml-product-ux-f62da32d5392?source=collection_archive---------9-----------------------#2024-05-17](https://towardsdatascience.com/best-practices-for-aiml-product-ux-f62da32d5392?source=collection_archive---------9-----------------------#2024-05-17)

## 本博客文章描述了在AIML用户体验（UX）中“优质表现”的实践，提供了示例，并为产品负责人指明了前进的路径。

[](https://medium.com/@bishr_tabbaa?source=post_page---byline--f62da32d5392--------------------------------)[![Bishr Tabbaa](../Images/d543523df58ff763e3164b917651cbf1.png)](https://medium.com/@bishr_tabbaa?source=post_page---byline--f62da32d5392--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f62da32d5392--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f62da32d5392--------------------------------) [Bishr Tabbaa](https://medium.com/@bishr_tabbaa?source=post_page---byline--f62da32d5392--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f62da32d5392--------------------------------) ·9分钟阅读·2024年5月17日

--

近年来，关于人工智能与机器学习（AIML）模型的研究投入了大量的时间、精力、泪水和资源，关注点集中在模型的规模与性能、快速发展、训练成本、安全性、延迟以及云端、本地和边缘计算中的各种模型托管选择。然而，有一个被忽视的领域就是***最后一公里***的产品用户体验（UX），以及如何将AIML最佳地融入产品中。

本博客文章描述了在AIML用户体验中“*优质表现*”的几项实践，提供了参考示例，并为正在塑造其产品和业务未来的产品经理和负责人指引了前进的道路。我还从独特的角度将这些实践与[亚马逊领导原则](https://www.amazon.jobs/content/en/our-workplace/leadership-principles)（LP）以及由Don Norman博士和Steve Krug所阐述的UX原则相结合，他们分别是《日常物品的设计》和《别让我思考》的作者。这些原则和实践共同为理解在构建AIML解决方案时推荐特定UX操作和思路的***原因***提供了坚实的基础。

# 注意力就是你所需要的一切。

![](../Images/12d5c7f7ecb39eb39c6273affda81717.png)

[作家在打字机前工作]，*稳定性AI*

无论是否与人工智能相关，产品特性***不会***产生影响，除非它们以一种方便、易用、易发现并且显而易见的方式呈现给用户。***可见性***在用户体验设计中至关重要，它与亚马逊的领导力原则***创新与简化***相一致，这表明这些特性应该直接展现于*注意力和意识的表面*，而无需过多的麻烦、摸索、寻找或挣扎。无论是内容、对话还是联系人，在正确的时间将正确的信息传递给正确的人是至关重要的。那么现在，让我们看看什么是优秀的表现。

例如，[Google Workspaces](https://workspace.google.com/solutions/ai/)将生成式AI功能直接嵌入到Docs、Mail和其他Google应用的用户体验中，使得用户无论是在撰写新文档，还是总结现有文档（无论是简短的备忘录、较长的论文还是动态的演示文稿）时，都能更加便捷。在Google Docs的情况下，内容会在提示下方生成，并且也会清楚标明该内容是由AIML模型生成的，标识为*星号*（*******）符号。这个表示由AIML生成的标识将在示例中频繁出现。

![](../Images/b7b0ff5faf369cb5903cc4ea888c442a.png)

[Gemini for Google Workspaces]，截图由**作者**提供

另一个例子是[Slack AI](https://slack.com/blog/news/slack-ai-has-arrived)，它允许你个性化搜索结果，并总结频道活动和长篇复杂的对话线程。在这种情况下，生成的内容会在右侧的紧凑面板中显示，作为丰富主面板的上下文细节。这种方式方便用户，不会造成信息过载，同时也清晰标明为AI生成的内容。

![](../Images/dc4e9998658e96636fa62a77f98b1b2b.png)

[Slack AI]，截图由作者提供

最后，亚马逊现在生成并展示了[产品评论摘要](https://www.aboutamazon.com/news/amazon-ai/amazon-improves-customer-reviews-with-generative-ai)。根据亚马逊的说法，仅在2022年，1.25亿客户为Amazon.com贡献了近15亿条评论和评分；某些单一产品的评论数达到数千条。因此，为了帮助客户了解某个产品是否适合他们，Amazon.com在产品详情页面提供了一个简短的段落，突出显示了评论中频繁提到的产品特性和客户情感。AI生成的评论还强调了关键的产品属性，并帮助客户更容易地查看提到这些属性的具体人类评论。同样，AI生成的内容紧凑，清晰标明为机器生成，并且某些方面使用了图标进行视觉标识。

![](../Images/275ed91a2380e25e2adb1347b7ee45f6.png)

[Amazon.com 产品评论]，截图由作者提供

# 你可以引用我说的话

![](../Images/effd10ae33d28af2c4f6671166caaa07.png)

[报纸编辑与记者室]，***Stability AI***

在报业界，有一句老话：“如果你想让读者相信你，那么就考虑让你的来源‘***公开表态***’，并获得他们的同意，这样‘***你就可以引用他们的话***’。”在学术和科学写作中，引用书目来源是一种常见做法。这种做法的目的是**赢得信任**，这是Amazon领导原则之一，也是**帮助性**，另一个UX原则。通过一些其他人如何提供引用的良好示例，最好来说明这一做法。

我向[Perplexity.ai](http://perplexity.ai)，一个基于AWS构建的AI驱动的对话式搜索服务，询问了：“*请帮我排查一个HAProxy安装问题，它无法在多个目标节点间正确进行负载均衡，而且仅错误地缓存了一个IP地址。请提供详细的逐步指导。*”请注意，在下面的响应中，顶部列出了多个来源，并且在引用这些来源时，响应中散布了数字引用（例如 *[1]*）。

![](../Images/2a797605981b56bfa05144528d00dc62.png)

[Perplexity AI]，**作者截图**

接下来，我与[Amazon Q](https://aws.amazon.com/q/)互动，并提出了这个问题：“*我计划创建一个每日日请求量为100k的无服务器API。每个请求都需要从数据库中读取数据。对于这种工作负载，最适合的服务是什么？*”再次，服务回答了我的问题，并提供了来源，但它们附加在响应的末尾，尽管按数字排序，但实际上这些参考文献并没有在响应中明确标明，读者无法直接获得。 我还赞赏清晰的链接到负责任的AI政策，这是建立信任的另一种方式。哪种方法更易于阅读和理解，取决于你特定应用的上下文。引用是否有助于澄清？它们会分散注意力吗？它们最适合放在哪里？

![](../Images/c77329dc292e932a47cc83fa39e293c0.png)

[Amazon Q]，作者截图

# 保持对话持续进行

![](../Images/5e44a9a50d2079e59dc5f977f0542fa1.png)

[Amazon Rufus]，作者截图

从静态用户界面到***动态对话界面***，正在出现一股[UX巨型趋势](https://uxmag.com/articles/how-conversational-ai-is-shaping-the-future-of-ux)。传统的图形用户界面，无论是位于主机、桌面、网页还是移动设备上，通常由窗口、标签、面板、表单、网格、复选框、文本框、列表选择器和按钮组成。虽然这些[UX模式](https://ui-patterns.com/)在让更多人类与计算机互动方面取得了巨大成功，但我们必须认识到，我们曾经需要自己学习如何与计算机工作。

在过去的一代人中，曾有人努力建立语音激活的用户界面，且已经取得了一些[商业成功](https://think.design/blog/interaction-design-evolution-part-2/)，如亚马逊Alexa、苹果Siri和谷歌助手。然而，这种对话式用户体验趋势现在正在改变主流应用程序，超越了大科技公司，进入到消费者和商业环境中。数字聊天机器人和助手是最明显的例子，上面展示的[亚马逊鲁弗斯](https://www.aboutamazon.com/news/retail/amazon-rufus)零售助手展示了从传统搜索到对话的转变。在这种情况下，鲁弗斯了解消费者、亚马逊产品目录，并且对整个世界有一定的认知模型，能够提供推荐、向用户介绍产品、比较不同品牌和类别的产品等等。一些[对话设计原则](https://uxmag.com/articles/7-principles-of-conversational-design-banner)包括*1/提供具有上下文的提示以引导对话*，*2/赋予个性化角色为文本增添情感色彩*，以及*3/分享历史记忆以建立关系，并基于过去的使用建立信任*。更需要注意和小心的是[验证](https://www.zendesk.com/blog/conversational-ux/)哪些提示变量是强制性的，哪些是可选的。你可能还需要提示用户捕捉更多信息，并在执行更多操作之前确认即将进行的操作。此外，你还需要考虑到对话中的停顿和误解。

尽管如此，数字化对话已经成为一种常态，生成式 AI 已经让企业能够将这项技术融入到他们的产品或服务中。这一**保持对话**的实践与亚马逊的核心领导力原则**客户痴迷**和用户体验原则**灵活性**相一致。

# 通过循环向用户学习。

![](../Images/d2aa276e9fae389f865ca5e47264349f.png)

[加利福尼亚州英约的莫比乌斯环岩石形成], 维基共享资源/公有领域

从用户那里学习对于与[商业成果](https://www.nngroup.com/articles/product-ux-benchmarks/)挂钩的产品迭代至关重要。这一实践与亚马逊领导力原则***学习与好奇***以及用户体验原则***反馈***相一致。你无法了解用户脑中的所有信息，因此需要向他们询问。跟踪并衡量这些信息，然后将收集到的数据与结果对齐。考虑优化电子商务购物车体验，以增加订单量并减少待处理购物车，也称为转化率。将网站上的时间和点击次数作为用户参与度的代理指标。当你开始使用任何新系统时，也要进行反思。首次点击、步骤和引导过程中的前几分钟至关重要，能够最大化任务成功率并减少错误率。现在，将这些想法放到生成性AI的背景下，这在商业和消费者场景中都是相对较新的产品。一些常见的做法包括“喜欢/不喜欢”和“分享支持”操作。

[Copilot for Microsoft 365](https://www.microsoft.com/en-us/microsoft-365/enterprise/copilot-for-microsoft-365) 展示了这一实践，提供了一流的“喜欢 / 不喜欢”按钮。另一个值得称赞的方面是清晰的斜体信息：“*AI生成的内容可能不正确*”。这也增强了信任感。

![](../Images/b64a7f5702e962fa7a0814957687d73c.png)

[Microsoft 365 CoPilot]，截图由作者提供

[Einstein Copilot for Salesforce](https://www.salesforce.com/news/press-releases/2024/04/25/einstein-copilot-general-availability/) 同样在拥有“喜欢/不喜欢”操作对方面做得很好，清晰地在右侧面板标注总结为AI生成，并区分了*接受*与*草稿*，使输出意图对用户更明确。

![](../Images/0cfcb2ce27ee9b20eb55895cccf21a05.png)

[Salesforce Einstein CoPilot]，截图由作者提供

# 拥有随机森林的地图。

![](../Images/818624f16102292141b9318dcb994e19.png)

[随机森林的地图]，***Stability AI***

引用路易斯·卡罗尔（《爱丽丝梦游仙境》的作者）的话，*如果你不知道要去哪里，那么任何一条路都能带你到那里*。支持这些AIML核心用户体验实践的，是传统的[产品路线图](https://www.nngroup.com/articles/ux-roadmaps-faq/)，而且没有捷径可以让你快速到达这幅宏大的蓝图。支持这一实践的原则包括亚马逊的领导力原则、***宏大思维***以及用户体验的***一致性***原则。首先要识别你的目标用户、产品使用场景和他们的目标。然后倒推回去。在亚马逊，我们通常会起草一个[PRFAQ](https://www.aboutamazon.com/news/workplace/an-insider-look-at-amazons-culture-and-processes)文档，设想未来应该是什么样子，并从外部公关（PR）和内部常见问题（FAQ）详细角度来思考。考虑实现客户目标所需的高层解决方案信息和流程。接着，深入挖掘用户体验层以及其他技术层的解决方案组件。根据风险、投资回报率、必须实现与可选功能等因素，对解决方案和组件的里程碑进行对齐和优先级排序。确保产品、工程和销售团队达成共识。然后根据路线图执行，前提是沿途可能会做出一些调整。

# 结论

在这篇博客中，我们介绍了五个AIML产品用户体验实践：**注意力就是你所需要的一切**（*可见性/简化*）、**你可以引用我说的这句话**（*赢得信任/有帮助性*）、**保持对话进行**（*客户痴迷、灵活性*）、**通过循环从用户中学习**（*学习与好奇心/反馈*）、以及**为随机森林绘制一张地图**（*宏大思维/创造力*）。我们还讨论了来自顶级科技公司（包括亚马逊、谷歌、微软、Perplexity、Salesforce和Slack）的多个实际产品示例，以说明这些实践和原则。希望你能学到一些新东西，并能立即将其应用到自己的产品和解决方案中。勇敢前行，开始构建吧！

*免责声明：请注意，本文内容仅代表我个人观点，并不一定代表我所在公司的立场。*

*喜欢这篇文章吗？请分享您的评论。关注我，了解更多更新，您可以在* [*Medium*](https://medium.com/@bishr_tabbaa) *和* [*Twitter*](https://twitter.com/bishr_tabbaa) *上找到我。*

# 参考文献

+   [https://www.nngroup.com/articles/principles-visual-design/](https://www.nngroup.com/articles/principles-visual-design/)

+   [https://www.nngroup.com/articles/product-ux-benchmarks/](https://www.nngroup.com/articles/product-ux-benchmarks/)

+   [https://sensible.com/dont-make-me-think/](https://sensible.com/dont-make-me-think/)

+   [https://workspace.google.com/solutions/ai/](https://workspace.google.com/solutions/ai/)?

+   [https://slack.com/blog/news/slack-ai-has-arrived](https://slack.com/blog/news/slack-ai-has-arrived)

+   [https://www.aboutamazon.com/about-us/leadership-principles](https://www.aboutamazon.com/about-us/leadership-principles)

+   [https://www.aboutamazon.com/news/amazon-ai/amazon-improves-customer-reviews-with-generative-ai](https://www.aboutamazon.com/news/amazon-ai/amazon-improves-customer-reviews-with-generative-ai)

+   [https://www.microsoft.com/en-us/microsoft-365/enterprise/copilot-for-microsoft-365](https://www.microsoft.com/en-us/microsoft-365/enterprise/copilot-for-microsoft-365)

+   [https://ui-patterns.com/patterns](https://ui-patterns.com/patterns)
