# 残疾、无障碍性与AI

> 原文：[https://towardsdatascience.com/disability-accessibility-and-ai-0d5ab06ec140?source=collection_archive---------7-----------------------#2024-09-16](https://towardsdatascience.com/disability-accessibility-and-ai-0d5ab06ec140?source=collection_archive---------7-----------------------#2024-09-16)

## 一场关于AI如何帮助和伤害残疾人的讨论

[](https://medium.com/@s.kirmer?source=post_page---byline--0d5ab06ec140--------------------------------)[![Stephanie Kirmer](../Images/f9d9ef9167febde974c223dd4d8d6293.png)](https://medium.com/@s.kirmer?source=post_page---byline--0d5ab06ec140--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0d5ab06ec140--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0d5ab06ec140--------------------------------) [Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--0d5ab06ec140--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0d5ab06ec140--------------------------------) ·9分钟阅读·2024年9月16日

--

![](../Images/a9bce1629a093e02f32f06eacf5af422.png)

图片由[Thought Catalog](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我最近读了[9月4日的一篇帖子](https://bsky.app/profile/shengokai.bsky.social/post/3l3evxm4zly2l)，作者是[美国大学的Johnathan Flowers博士](https://www.american.edu/cas/faculty/jflowers.cfm)，讲述了当[NaNoWriMo](https://en.wikipedia.org/wiki/National_Novel_Writing_Month)的组织者发布声明，表示支持参与者使用生成型AI（如大型语言模型聊天机器人）作为今年活动的一部分时，所引发的争议。

> “比如，艺术往往是唯一一个可以在不依赖健全人慷慨或强迫亲密的情况下，克服残疾身体与世界之间不契合的地方。说我们需要AI的帮助，其实是忽视了这一切。” -[Johnathan Flowers博士，2024年9月4日](https://bsky.app/profile/shengokai.bsky.social/post/3l3evxm4zly2l)

Flowers博士认为，通过特别强调这一决定是为了让残疾人和边缘化群体能够参与，组织者实际上是在轻视这些群体的创造力和艺术参与能力。作为一名残疾人，他指出，艺术是社会中少数几个残疾不构成参与障碍的领域之一，这一点在其他不太容易接触的空间中并不常见。

自从最初的声明以及这次和其他许多批评之后，NaNoWriMo的组织者已经软化或撤回了一些他们的表态，[最近的帖子](https://nanowrimo.org/a-note-to-our-community-about-our-comments-on-ai-september-2024/)似乎是本周早些时候的更新。不幸的是，正如常常发生的那样，社交媒体上的许多讨论变成了无效的争论。

我之前在这里讨论过，当生成性人工智能参与艺术创作时，评估其真正含义的难度，我依然坚持我的观点：作为艺术的消费者，我寻求的是与他人视角和世界观的连接，因此，人工智能生成的作品在这一点上并不引起我的兴趣。然而，我之前并没有花太多时间思考人工智能作为辅助工具的角色，而今天我想讨论的正是这个话题。

> *我不是一名身体有障碍的人，所以我只能作为一名社会科学家和外部观察者来探讨这个话题。我的观点仅代表我个人，而非任何社区或组织的立场。*

# 框架

在最近的一次演讲中，我被要求首先给出“人工智能”的定义，我总是有些畏惧这个问题，因为它非常模糊且难以把握，但这次我尝试了一个新的角度，阅读了一些最近的监管和政策讨论，并得出了这个定义：

> ***人工智能：使用某些形式的机器学习来执行本应由人类完成的工作。***

我仍在不断探索这个问题，可能会一直探索下去，因为世界在变化，但我认为这个定义对于今天的讨论是有用的。请注意，这并不是将我们的讨论仅限于生成性人工智能，这是很重要的。关于人工智能的这次讨论，特别是指将机器学习应用于完成当前无法通过其他方式自动化的任务，无论它是否涉及深度学习。

关于残疾的社会理论是一个独立的学科，具有巨大的深度和复杂性。就像讨论和学术研究其他群体的人一样，实际的残疾群体成员的声音不仅需要被听到，而且要在讨论他们如何被对待以及他们在更广泛社会中的机会时，发挥主导作用。根据我对这个领域的理解，我希望优先考虑残疾人士拥有他们所希望的自主权和独立性，并获得足够的支持，以便他们能有与非残疾人士相当的机会和成果。还值得一提的是，许多最初为帮助残疾人士而开发的技术，实际上也对所有人有助，比如自动门。

# 人工智能作为工具

那么，人工智能在这个目标中到底能发挥什么作用呢？人工智能对残障人士来说是一个净好处吗？一般来说，技术，尤其是与人工智能相关的发展，已经在多个方面应用，为残障人士提供了自主性和独立性，这是过去无法实现的。任何像我一样最近在观看巴黎残奥会的人，都能想到技术在这方面的应用实例。

但我很好奇，人工智能提供了哪些其他工具是之前不存在的，以及可能存在的缺点或风险。事实证明，已经有相当多有趣的学术研究在这一问题上展开，并且持续发布。我将简要概述几个关键领域，并在你有兴趣深入了解时提供更多资源。

# 优点

## 神经学和沟通问题

这似乎应该是人工智能工具的一个强项。大语言模型（LLM）在重新表述、改写或总结文本方面具有很大的实用性。当个体在阅读长篇文本或集中注意力上有困难时，能够生成准确的总结可以让他们更容易接触到文本的主题。这不一定是对整篇文本的替代，而只是一个增强读者理解的工具。（就像 Cliff Notes，但它们是按应有的方式使用。）我不推荐直接向大语言模型询问一段话的含义，因为那样更可能产生错误或不准确，但总结已经存在的文本则是一个不错的用例。

其次，语言交流有困难的人可以通过人工智能工具获得支持。这些技术可以将语音文本转化为高度准确的自动转录，可能让患有语言障碍的人更容易理解，或者它可以让一个有说话困难的人写下文字，并将其转化为高度真实的人类语音。（事实上，人工智能合成语音最近变得非常惊人！）

这还没有涉及到人工智能如何帮助听力障碍者！助听器可以使用模型来识别并隔离用户希望专注的声音，减少干扰或背景噪音。任何使用过主动噪声取消技术的人，都在受益于这种技术，这也是一个很好的例子，说明这些技术对有无障碍的人都有帮助。

+   [利用深度学习的语音识别：最新进展的系统评审（2024年3月）](https://www.researchgate.net/profile/Adi-Maaita/publication/378042331_Speech_Recognition_Utilizing_Deep_Learning_A_Systematic_Review_of_the_Latest_Developments/links/65cb6678790074549783aa83/Speech-Recognition-Utilizing-Deep-Learning-A-Systematic-Review-of-the-Latest-Developments.pdf)

## 视觉与图像

对于视力障碍人士来说，数字化参与可能存在障碍，包括一些设计不当的网页无法与屏幕阅读器兼容，或图像内容缺乏描述性替代文本等。模型在识别图像中的物体或特征方面越来越熟练，如果能够广泛普及，这可能是一个极具价值的人工智能应用，屏幕阅读软件可以生成自己的替代文本或图像描述。

+   [https://tink.uk/thoughts-on-screen-readers-and-image-recognition/](https://tink.uk/thoughts-on-screen-readers-and-image-recognition/)

+   [https://www.theverge.com/2022/3/18/22984474/microsoft-edge-automatic-image-labels-accessibility-feature](https://www.theverge.com/2022/3/18/22984474/microsoft-edge-automatic-image-labels-accessibility-feature)

+   [人工智能计算机视觉和图像识别的应用](https://www.researchgate.net/publication/376510799_Application_of_AI_Computer_Vision_and_Image_Recognition)（2022年1月）

## 物理义肢

还有一些形式的人工智能，能够帮助义肢和身体辅助工具更好地工作。我并不是指使用神经植入物的技术，虽然这类技术正在研究中，但有许多模型通过学习人体运动的物理学，帮助计算机驱动的义肢更好地为人们服务。这些模型可以与肌肉和神经末梢相结合，或者它们可以巧妙地自动化某些动作，帮助上肢义肢改善诸如精细运动技能等问题。下肢义肢则可以利用人工智能更好地理解和生成步幅、流畅度等动作。

+   [人工智能在义肢中的应用](https://www.researchgate.net/publication/378907593_Application_of_Artificial_Intelligence_in_Prosthetics_A_Review)（2024年3月）

+   [人工智能如何帮助推动下一代义肢的发展（2023年1月）](https://www.wevolver.com/article/how-ai-is-helping-power-next-generation-prosthetic-limbs)

# 否定

## 表现与抹除

好的，这只是人工智能可以为残障需求带来的其中一些积极作用。然而，我们也应该花一些时间讨论人工智能可能对残障人士和我们社会带来的负面影响。大多数这些问题都涉及使用人工智能的文化生产，我认为这些问题主要源于这些模型复制和强化了社会偏见和歧视。

举个例子：

+   由于我们的社会结构并未优先考虑或突出残障人士及其需求，模型也没有做到这一点。我们的社会充斥着能力主义，这种偏见在人工智能生成的文本中得到了体现。我们可以在提示工程中明确尝试纠正这一点，但很多人不会花时间去做这件事，或者根本没有意识到这一点。

+   同样，由人工智能模型生成的图像往往会抹去所有那些在文化上不占主导地位或在媒体中未得到优先展示的群体，包括残障人士。只要这些模型使用包含积极展示残障人士的训练数据，情况就会有所改善，但在展示比例是否符合现实与我们希望获得更多展示而不被抹去之间，总是存在一种天然的紧张关系。

## 数据隐私与伦理

这一领域有两个主要主题，对残障人士具有负面潜力。

+   首先，人工智能被用来对残障人士的需求和能力做出假设，存在很高的风险，可能会导致歧视。和任何群体一样，询问人工智能该群体可能偏好、需要或认为理想的事物，并不能代替让该群体参与决定那些将影响他们的事务。但人们往往会选择“直接问人工智能”，这是轻松且懒惰的做法，毫无疑问，这种情况时常发生。

+   其次，数据隐私是一个复杂的话题。具体来说，当某人使用无障碍技术时，比如手机或网页的屏幕阅读器，这可能会推断出该人是否有残障的状态。如果这些数据没有得到妥善保护，个人的残障状态，或如果推断错误时的感知状态，可能会成为一个潜在的负担，导致个人在其他领域面临歧视的风险。我们需要确保，无论某人是否在使用无障碍工具或功能，都应视为敏感的个人数据，正如对待他们的其他信息一样。

## 医疗治疗中的偏见

当医疗界开始在工作中使用人工智能时，我们应密切关注这对边缘群体，包括残障人士的副作用。与大型语言模型（LLM）使用可能导致残障人士的真实声音在重要决策中被忽视类似，如果医疗专业人员使用LLM来建议残障的诊断或治疗方案，这些建议将受到这些模型所带有的社会和文化负面偏见的影响。

这可能意味着非刻板印象或不常见的残障表现可能会被忽视或忽略，因为模型在理解异常和特殊情况时必然存在困难。这也可能意味着，当患者的实际经历与模型的预期或预测相反时，患者可能很难说服医疗提供者接受其经历。正如我在其他工作中讨论过的那样，人们可能对机器学习模型的准确性过于自信，而人类的观点可能会因此被视为不那么可信，尽管这并非一个可以证明的断言。

# 技术的获取

还有许多其他技术我没有时间在这里讨论，但我确实想指出，技术的存在本身并不等于残障人士能轻松、负担得起地访问并实际使用这些技术。残障人士往往在经济上处于不利地位，部分原因是经济参与的障碍，许多突破性的进展实际上并未对许多有需求的人群开放。这是一个我们社会需要承担责任的问题——尤其是在美国的医疗保健领域，我们在满足人们对护理和工具的需求方面做得非常糟糕，这些工具本应帮助人们过上更好的生活并参与经济活动。

# 结论

这只是对这个领域一些关键问题的简要回顾，我认为这对我们这些从事机器学习工作的人来说是一个重要的话题。我们构建的技术对边缘化群体，包括残障人士，有着双重影响——既有益处也有风险，我们的责任是要在工作中考虑到这些风险，并尽力减轻它们。

# 进一步阅读

[https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2020.571955/full?ref=blog.mondato.com](https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2020.571955/full?ref=blog.mondato.com)

[https://slate.com/technology/2024/09/national-novel-writing-month-ai-bots-controversy.html](https://slate.com/technology/2024/09/national-novel-writing-month-ai-bots-controversy.html)

[](https://www.american.edu/cas/faculty/jflowers.cfm?source=post_page-----0d5ab06ec140--------------------------------) [## 教授讲师

### 约翰·弗劳尔斯（Johnathan Flowers）是美国大学哲学与宗教系的教授讲师…

[www.american.edu](https://www.american.edu/cas/faculty/jflowers.cfm?source=post_page-----0d5ab06ec140--------------------------------)

阅读我更多的作品，请访问[www.stephaniekirmer.com](http://www.stephaniekirmer.com)。
