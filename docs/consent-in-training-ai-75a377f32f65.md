# 在人工智能训练中的同意问题

> 原文：[https://towardsdatascience.com/consent-in-training-ai-75a377f32f65?source=collection_archive---------4-----------------------#2024-10-02](https://towardsdatascience.com/consent-in-training-ai-75a377f32f65?source=collection_archive---------4-----------------------#2024-10-02)

## 你是否应该控制有关你个人信息是否用于训练生成性人工智能的使用？

[](https://medium.com/@s.kirmer?source=post_page---byline--75a377f32f65--------------------------------)[![Stephanie Kirmer](../Images/f9d9ef9167febde974c223dd4d8d6293.png)](https://medium.com/@s.kirmer?source=post_page---byline--75a377f32f65--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--75a377f32f65--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--75a377f32f65--------------------------------) [Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--75a377f32f65--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75a377f32f65--------------------------------) ·10分钟阅读·2024年10月2日

--

![](../Images/663129a104f75a0529c54dfbd8d3f6bd.png)

图片来源：[Caroline Hall](https://unsplash.com/@notcarolyn?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我相信很多正在阅读这篇文章的你们都听说过最近的争议，LinkedIn 似乎开始在没有通知用户或更新隐私政策以允许此事的情况下，悄悄地使用用户个人数据来训练大型语言模型（LLM）。正如我当时在那边提到的，这让我感到相当震惊，因为考虑到我们对人工智能的监管态度和公众的普遍担忧，似乎这是一项相当惊人的举措。在[最近的新闻中，在线培训平台 Udemy](https://www.404media.co/massive-e-learning-platform-udemy-gave-teachers-a-gen-ai-opt-out-window-its-already-over/) 做了一些类似的事情，他们悄悄地给讲师提供了一个小窗口，让他们可以选择是否让自己的个人数据和课程材料用于训练人工智能，并且现在已经关闭了这个窗口，不再允许选择退出。在这两种情况下，企业都选择使用了[被动选择同意框架](https://www.datagrail.io/blog/data-privacy/opt-out-and-opt-in-consent-explained)，这种框架有其优缺点。

为了说明这些情况下发生的事情，让我们从一些基本概念开始。像Udemy和LinkedIn这样的社交平台与用户相关的内容大致分为两种类型。一种是个人数据，指的是你提供的信息（或他们通过合理推测得出的信息），这些信息可以单独使用或组合使用来识别你在现实生活中的身份。另一种是你创建或发布的其他内容，包括你对他人帖子发表评论或点赞、你为课程创建的幻灯片等。有些内容可能不被视为个人数据，因为它无法单独识别你个人的身份。然而，这并不意味着这些内容对你不重要，但数据隐私通常不涵盖这些内容。各种司法管辖区的法律保护（如果存在）通常会涵盖个人数据，因此这就是我将在这里关注的重点。

# LinkedIn故事

LinkedIn有一项关于一般内容（非个人数据）权利的通用且非常标准的政策，在该政策中，他们获得了非独占权利，允许他们使这些内容对用户可见，通常这也使他们的平台得以运作。

然而，[有一项单独的政策管理数据隐私](https://www.linkedin.com/legal/privacy-policy#use)，该政策涉及到你的个人数据，而非你发布的帖子，这也是在AI训练情况下引发争议的部分。今天（2024年9月30日），它写道：

> *我们如何使用你的个人数据将取决于你使用哪些服务、你如何使用这些服务以及你在* [*设置*](https://www.linkedin.com/psettings/) *中所做的选择。我们可能会使用你的个人数据来改进、开发和提供产品与服务，开发和训练人工智能（AI）模型，开发、提供和个性化我们的服务，并通过AI、自动化系统和推理获得洞察，以便我们的服务能对你和其他人更有相关性和实用性。你可以在此查看LinkedIn的负责任AI原则* [*这里*](https://www.linkedin.com/blog/member/trust-and-safety/responsible-ai-principles) *并进一步了解我们对生成性AI的处理方式* [*这里*](https://www.linkedin.com/help/linkedin/answer/a5538339?hcppcid=search)*。* [*了解更多*](https://www.linkedin.com/help/linkedin/answer/a1337820/) *关于我们可能做出的推理，包括有关* [*你的年龄和性别*](https://www.linkedin.com/help/linkedin/answer/a517610/) *以及我们如何使用它们。*

当然，在他们开始使用你的个人数据进行AI模型训练时，并没有这样表述。2024年9月中旬的早期版本（[感谢Wayback Machine](https://web.archive.org/web/20240917144440/https://www.linkedin.com/legal/privacy-policy#use)）是：

> *我们如何使用您的个人数据将取决于您使用的服务、您如何使用这些服务以及您在* [*设置*](https://web.archive.org/web/20240917144440/https://www.linkedin.com/psettings/) *中所做的选择。我们利用我们所掌握的关于您的数据来提供和个性化我们的服务，包括通过自动化系统和我们做出的推断，从而使我们的服务（包括广告）能够对您和其他人更相关、更有用。*

理论上，“通过自动化系统和我们做出的推断”可以在某些方面被扩展为包括 AI，但大多数用户可能很难接受这一点。然而，在9月18日之前，这段文字尚未更改时，人们已经注意到 LinkedIn 网站上添加了一个非常隐蔽的选择退出切换按钮，形状如下：

![](../Images/04a61940f9a766668e6df6c921e1a663.png)

作者从 linkedin.com 截图

（我的切换按钮是关闭的，因为我更改了它，但默认设置是“开启”的。）

这强烈暗示了，在更新服务条款之前，LinkedIn 就已经在使用用户的个人数据和内容进行生成性 AI 开发。我们当然不能完全确定，但很多用户都有疑问。

# Udemy 的故事

对于 Udemy 的情况，事实稍有不同（而且新的事实正在不断揭露），但根本问题类似。Udemy 的教师和学生将大量个人数据以及他们编写和创作的材料提供给 Udemy 平台，而 Udemy 提供基础设施和协调，使课程得以进行。

Udemy 在八月发布了[讲师生成 AI 政策](https://support.udemy.com/hc/en-us/articles/25180159300631-Instructor-Generative-AI-Policy)，其中包含了相当多的关于他们希望拥有的数据权利的细节，但对于他们的 AI 程序到底是什么，文中并未详细说明。通过阅读这份文档，我对他们计划训练的模型或已经在训练的模型，以及他们期望达成的结果感到非常不清楚。它没有区分个人数据（如讲师的肖像或个人详细信息）和其他内容（如讲座记录或评论）。这份政策显然涵盖了个人数据，而且他们对此非常开放，[在他们的隐私政策中也有明确说明](https://www.udemy.com/terms/privacy/#section3)。在“我们如何使用您的数据”一节下，我们找到了：

> *改进我们的服务并开发新产品、服务和功能（所有数据类别），包括通过使用与* [*讲师生成 AI 政策*](https://www.udemy.com/support/25180159300631/) *(讲师共享内容)一致的 AI；*

它们所说的“所有[数据类别](https://www.udemy.com/terms/privacy/#section1)”包括（但不限于）：

+   账户数据：用户名、密码，但对于讲师来说，如果您提供，还包括“政府身份证信息、验证照片、出生日期、种族/民族和电话号码”

+   个人资料数据：“照片、头衔、简介、语言、网站链接、社交媒体资料、国家或其他数据。”

+   系统数据：“你的IP地址、设备类型、操作系统类型和版本、唯一设备标识符、浏览器、浏览器语言、域名及其他系统数据，以及平台类型。”

+   大致的地理数据：“国家、城市和地理坐标，基于你的IP地址计算。”

但是所有这些类别都可能包含个人数据，有时甚至是受保护的个人身份信息（PII），这些数据在全球多个司法辖区内都受到全面的数据隐私法规的保护。

生成式 AI 的推进似乎从今年夏天悄然开始，像 LinkedIn 一样，它采用了一个选择退出机制，所以不想参与的用户必须主动采取措施。至少从我们目前能看到的情况来看，他们似乎在更改隐私政策之前并没有开始这些操作，但 Udemy 采取了一个不同寻常的做法，即将选择退出设定为时间限制的事项，他们的讲师必须等到每年指定的时间段才能做出改变。这已经开始让用户感到措手不及，尤其是因为这个时间窗口的通知显然没有广泛共享。从美国的数据隐私角度来看，Udemy在实施这个奇怪的时间限制之前，并没有做出任何新的或出乎意料的举措，只要他们更新了隐私政策，并且至少在开始使用个人数据进行训练之前尝试通知用户。

（还有一个问题是平台上教师对自己创作内容的知识产权问题，但这是我文章之外的问题，因为知识产权法与隐私法大相径庭。）

# 伦理

鉴于这些事实，并推测 LinkedIn 确实在通知用户之前就开始使用人们的数据来训练生成式 AI 模型，那么我们该如何看待这一切呢？如果你是这些平台的用户，这是否重要？你应该在乎这些吗？

我将建议，有几个重要的原因让我们关心这些数据使用模式的发展，不论你个人是否介意将自己的数据包含在训练集里。

## 你的个人数据构成了风险。

你的个人数据对这些公司来说很有价值，但它也构成了风险。当你的数据被转移并用于多种用途时，包括训练 AI，随着数据副本的增多，泄露或数据丢失的风险也随之增加。在生成式 AI 中，还有一个风险是训练不充分的大型语言模型（LLM）可能会在其输出中意外地泄露个人信息。每一个使用你的数据进行训练的新模型，都是数据以这种方式无意曝光的机会，尤其是因为许多从事机器学习的人对保护数据的最佳实践知之甚少。

## 知情同意的原则应该被认真对待。

知情同意是生物医学研究和医疗保健领域的一个著名基石原则，但在其他行业中并没有得到足够的关注。这个理念是，每个人都有不应被侵犯的权利，除非该个人同意，并且在完全掌握相关事实的基础上，才能做出慎重的决定。如果我们认为个人数据的保护是这一系列权利的一部分，那么这些情况应该要求知情同意。如果我们对公司忽视这些权利视而不见，就相当于在设定一个先例，表示这些违规行为并不重要，更多公司会继续以同样的方式行事。

## 黑暗模式可以构成强迫行为。

在社会科学中，关于选择加入（opt-in）和选择退出（opt-out）作为框架的研究相当多。通常，将这样一个敏感问题设置为选择退出，是为了让人们难以行使他们真正的选择，可能是因为它难以操作，或者因为他们甚至没有意识到自己有选择的余地。实体通过设计人们表达选择的界面，能够鼓励甚至强迫行为朝着有利于商业的方向发展。这种具有强制性倾向的设计属于我们所说的“黑暗模式”用户体验设计。在Udemy将选择退出的时限限制为一个时间窗口时，这个问题变得更加严重。

## 这不仅仅涉及图像和多媒体，还包括文本。

这可能不会立刻引起每个人的注意，但我只想强调，当你将个人照片或任何形式的个人照片上传到这些平台时，那就成为了他们收集的关于你的数据的一部分。即便你可能并不在意你的LinkedIn帖子上的评论被用于模型训练过程，你可能会更在意你的面部图像被用于训练生成深度伪造视频（deepfake）之类的生成性AI模型。也许你不在乎！但当你考虑你的数据被用于生成性AI时，请记住这一点。

# 该怎么办？

目前，不幸的是，受影响的用户在面对这些不道德商业行为时，几乎没有什么反应的选择。

如果你意识到你的数据正在被用来训练生成式AI，并且你希望避免这种情况，可以选择退出，如果企业允许的话。然而，如果（如同Udemy的情况）它们限制了这一选项，或者根本不提供这个选项，你就需要关注监管领域。许多美国人可能没有太多的追索权，但像CCPA这样的全面数据隐私法往往会稍微涉及此类问题。([查看IAPP追踪器，了解你所在州的状态](https://iapp.org/resources/article/us-state-privacy-legislation-tracker/))。CCPA通常允许选择退出框架，其中用户没有采取行动将被视为同意。然而，CCPA确实要求选择退出不应过于困难。例如，不能要求通过邮寄纸质信件来提交选择退出请求，而你本可以通过电子邮件确认同意。公司还必须在15天内对选择退出请求作出回应。Udemy将选择退出限制为每年特定时间范围，这符合要求吗？

但让我们退一步思考。如果你没有意识到你的数据正在被用来训练AI，而是在事后才发现，那你该怎么办？好吧，CCPA允许同意是被动的，但[它确实要求你被告知个人数据的使用情况](https://oag.ca.gov/privacy/ccpa)。隐私政策中的披露通常已经足够，因此，鉴于LinkedIn一开始没有这样做，这可能成为一些法律挑战的原因。

值得注意的是，欧盟居民可能不需要担心这些问题，因为保护他们的法律更加明确和一致。[我之前写过关于欧盟AI法案的文章](/uncovering-the-eu-ai-act-22b10f946174)，该法案对AI应用有相当多的限制，但它并未真正涉及同意问题或数据如何用于训练。相反，GDPR更可能保护人们免受类似这里发生的事情。在这项法律下，欧盟居民必须被告知并要求明确同意，而不仅仅是提供选择退出的机会。他们还必须有能力撤回对个人数据使用的同意，而我们不知道这种行动的时限是否符合要求，因为[GDPR的规定是，要求停止处理某人个人数据的请求必须在一个月内处理](https://gdpr.eu/checklist/)。

# 经验教训

我们目前并不清楚Udemy和LinkedIn实际上在如何使用这些个人数据，除了它们正在训练生成式AI模型的这一大致想法之外，但我认为我们可以从这两个新闻故事中学到的一点是，保护个人数据权利不能仅仅依赖于公司利益，必须有政府的介入。对于那些注重通知客户并确保容易退出的道德企业来说，将会有很多其他公司规避规则，做最少的事情或更少，除非人们的权利得到了执行的保护。

阅读更多我的作品，访问[www.stephaniekirmer.com.](http://www.stephaniekirmer.com.)

# 深入阅读

[https://www.datagrail.io/blog/data-privacy/opt-out-and-opt-in-consent-explained](https://www.datagrail.io/blog/data-privacy/opt-out-and-opt-in-consent-explained/#:~:text=Although%20CCPA%20is%20primarily%20a,of%2013%20without%20parental%20consent)

[https://www.404media.co/massive-e-learning-platform-udemy-gave-teachers-a-gen-ai-opt-out-window-its-already-over/](https://www.404media.co/massive-e-learning-platform-udemy-gave-teachers-a-gen-ai-opt-out-window-its-already-over/)

[](https://www.404media.co/linkedin-is-training-ai-on-user-data-before-updating-its-terms-of-service/?source=post_page-----75a377f32f65--------------------------------) [## LinkedIn正在使用用户数据训练AI，在更新其服务条款之前

### 多位LinkedIn用户在周三注意到一个设置，显示LinkedIn正在使用用户数据来改进其…

www.404media.co](https://www.404media.co/linkedin-is-training-ai-on-user-data-before-updating-its-terms-of-service/?source=post_page-----75a377f32f65--------------------------------) [](https://iapp.org/resources/article/us-state-privacy-legislation-tracker/?source=post_page-----75a377f32f65--------------------------------) [## 美国各州隐私立法追踪器

### 这个工具追踪全国范围内的美国各州隐私法案，帮助我们的成员跟进…

iapp.org](https://iapp.org/resources/article/us-state-privacy-legislation-tracker/?source=post_page-----75a377f32f65--------------------------------)

## 隐私政策

[https://web.archive.org/web/20240917144440/https://www.linkedin.com/legal/privacy-policy#use](https://web.archive.org/web/20240917144440/https://www.linkedin.com/legal/privacy-policy#use)

[https://www.linkedin.com/blog/member/trust-and-safety/updates-to-our-terms-of-service-2024](https://www.linkedin.com/blog/member/trust-and-safety/updates-to-our-terms-of-service-2024)

[https://www.linkedin.com/legal/privacy-policy#use](https://www.linkedin.com/legal/privacy-policy#use)

[https://www.udemy.com/terms/privacy/#section1](https://www.udemy.com/terms/privacy/#section1)

## GDPR与CCPA

[## GDPR合规性检查清单 - GDPR.eu

### 使用此GDPR合规性检查清单来规划贵组织的数据隐私和安全措施。记录您的步骤…

gdpr.eu](https://gdpr.eu/checklist/?source=post_page-----75a377f32f65--------------------------------) [](https://oag.ca.gov/privacy/ccpa?source=post_page-----75a377f32f65--------------------------------) [## 加利福尼亚消费者隐私法案（CCPA）

### 更新于2024年3月13日，《2018年加利福尼亚消费者隐私法案》（CCPA）赋予消费者更多对其个人信息的控制权…

oag.ca.gov](https://oag.ca.gov/privacy/ccpa?source=post_page-----75a377f32f65--------------------------------)
