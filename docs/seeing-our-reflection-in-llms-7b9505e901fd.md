# 在大语言模型中看到我们的倒影

> 原文：[https://towardsdatascience.com/seeing-our-reflection-in-llms-7b9505e901fd?source=collection_archive---------5-----------------------#2024-03-02](https://towardsdatascience.com/seeing-our-reflection-in-llms-7b9505e901fd?source=collection_archive---------5-----------------------#2024-03-02)

## 当大语言模型（LLMs）给我们提供揭示人类社会缺陷的结果时，我们能否选择听取它们告诉我们的内容？

[](https://medium.com/@s.kirmer?source=post_page---byline--7b9505e901fd--------------------------------)[![Stephanie Kirmer](../Images/f9d9ef9167febde974c223dd4d8d6293.png)](https://medium.com/@s.kirmer?source=post_page---byline--7b9505e901fd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7b9505e901fd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7b9505e901fd--------------------------------) [Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--7b9505e901fd--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7b9505e901fd--------------------------------) ·阅读时间 7 分钟 ·2024年3月2日

--

![](../Images/6b8b81aae84440667a1e78dbd9ebe997.png)

图片由 [Vince Fleming](https://unsplash.com/@vincefleming?utm_source=medium&utm_medium=referral) 提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 机器学习，轻推

到现在为止，我相信你们大多数人都听说过关于 [谷歌的新大语言模型 Gemini 生成了穿着纳粹制服的种族多样化人物图片](https://www.vox.com/future-perfect/2024/2/28/24083814/google-gemini-ai-bias-ethics)的新闻。这则小小的新闻提醒了我一些我一直想讨论的事情，那就是当模型存在盲点时，我们如何应用专家规则来调整它们生成的预测，以避免返回给用户一些极端离谱的结果。

这种情况在机器学习中并不罕见，至少在我的经验中是这样，特别是当你拥有存在缺陷或有限的训练数据时。我记得自己工作中的一个典型例子是预测包裹什么时候会送达某个商业办公室。从数学上讲，我们的模型在精确估算包裹何时接近办公室这一点上非常出色，但有时，卡车司机在深夜到达目的地后会在卡车里或酒店休息直到第二天早晨。为什么？因为在办公时间之外，没有人在办公室接收/签收包裹。 

教会一个模型“工作时间”的概念可能非常困难，而更简单的解决方法是直接说：“如果模型预测交付时间在工作时间之外，就给预测加上足够的时间，让它变化到下一个办公室开放的时间。”简单！这解决了问题，而且反映了实际情况。我们只是在给模型一点帮助，让它的结果能更好地工作。

然而，这确实带来了一些问题。首先，我们现在有两个不同的模型预测需要管理。我们不能直接丢弃原始模型的预测，因为我们需要它来进行模型性能监控和指标评估。你不能通过人类干预后的预测来评估一个模型，因为那在数学上是不成立的。但为了更清晰地了解模型在现实世界中的影响，你确实需要查看规则后的预测，因为那才是客户在你的应用中实际体验到/看到的结果。在机器学习中，我们习惯了一个非常简单的框架，每次运行一个模型都会得到一个结果或一组结果，事情就这么定了，但当你开始在发布之前调整结果时，你就需要从不同的层面去思考了。

# 应用于LLM

我有点怀疑这可能是像 Gemini 这样的LLM（大语言模型）所发生的一种形式。然而，与其说是预测后的规则，似乎[聪明的钱说 Gemini 和其他模型正在应用“秘密”提示增强技术，试图改变LLM产生的结果。](https://www.washingtonpost.com/technology/2024/02/22/google-gemini-ai-image-generation-pause/?pwapi_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyZWFzb24iOiJnaWZ0IiwibmJmIjoxNzA4ODM3MjAwLCJpc3MiOiJzdWJzY3JpcHRpb25zIiwiZXhwIjoxNzEwMjE1OTk5LCJpYXQiOjE3MDg4MzcyMDAsImp0aSI6IjFhMzAyYjkyLTRkN2ItNDNmMi1hNThlLWY1MDBjY2I2NDFjMyIsInVybCI6Imh0dHBzOi8vd3d3Lndhc2hpbmd0b25wb3N0LmNvbS90ZWNobm9sb2d5LzIwMjQvMDIvMjIvZ29vZ2xlLWdlbWluaS1haS1pbWFnZS1nZW5lcmF0aW9uLXBhdXNlLyJ9.E-JdVAohho0X-rTsTb1bfof4gIpYl8-NpPdZwL6h9Dc)

本质上，如果没有这种轻推，模型会生成反映它所训练内容的结果。也就是说，模型学习的是由真实人类产生的内容。我们的社交媒体帖子、历史书籍、博物馆画作、流行歌曲、好莱坞电影等。模型吸收了所有这些内容，并学习其中的潜在模式，无论这些模式是我们引以为傲的，还是不是。一个被输入了当代社会中所有可用媒体的模型，将会接触到大量的种族主义、性别歧视以及其他各种形式的歧视和不平等，更不用说暴力、战争和其他可怕的事情了。当模型学习人们的外貌、声音、言辞和动作时，它在学习的其实是那个有缺陷的完整版本。

> 我们的社交媒体帖子、我们的历史书籍、我们的博物馆画作、我们的流行歌曲、我们的好莱坞电影等等。模型吸收了所有这些内容，并学习了其中的潜在模式，无论这些模式是我们引以为傲的，还是我们不愿面对的。

这意味着，如果你要求底层模型展示一位医生，它可能会给你展示一位穿着实验室外套的白人男性。这不仅仅是随机的，这是因为在我们现代社会，白人男性在进入像医生这样的高端职业中占有不成比例的优势，因为他们通常能获得更多和更好的教育、财务资源、指导、社会特权等等。模型正在反映出一种可能让我们感到不舒服的形象，因为我们不愿意去思考这种现实。

# 那么我们该怎么办呢？

一个显而易见的论点是：“我们不希望模型加强我们社会已经存在的偏见，我们希望它能改善对少数群体的代表性。”我非常理解这个观点，并且非常关注我们媒体中的代表性。然而，这其中有一个问题。

很难说这些调整会是一个可持续的解决方案。回想我开始时提到的关于双子座的故事。这就像是在玩打地鼠游戏，因为工作永无止境——现在我们看到有色人种穿着纳粹制服，这显然让很多人感到深深的冒犯。所以，也许我们最初随机在提示中添加“作为一名黑人”或“作为一名土著”时，接下来我们必须添加更多内容，以排除那些不合适的情况——但如何以一种大模型能理解的方式来表达呢？我们可能需要从头开始，重新思考最初的解决方案是如何运作的，并重新审视整体方法。在最好的情况下，像这样的调整只会解决一个狭隘的输出问题，但可能会产生更多的新问题。

让我们举一个非常真实的例子。如果我们在提示中加上“在回答中永远不要使用露骨或粗俗的语言，包括[这里列出不当词汇]”。也许这在很多情况下有效，模型会拒绝说一个13岁男孩为了搞笑而请求的脏话。[但迟早，这会带来意想不到的附加副作用。](https://www.wired.com/story/ai-list-dirty-naughty-obscene-bad-words/)如果有人在查找[英国萨塞克斯的历史](https://www.boredpanda.com/people-with-dirty-last-names-problems/)，会怎么样？或者，总会有人提出你在列表中遗漏的脏话，这意味着维护列表将是一个持续不断的工作。其他语言中的脏话呢？[谁来判断哪些词应该列入列表](https://www.newsweek.com/twitter-lgbtq-censor-censorship-elon-musk-1792139)？光是想想这件事就让我头疼。

这只是两个例子，我相信你可以想到更多类似的情况。这就像是在一个漏水的管子上贴创可贴，每次修补一个地方，另一个地方就会漏水。

# 那我们该怎么办呢？

那么，我们究竟希望从LLM中得到什么呢？我们希望它生成一幅非常真实的镜像，反映人类真实的面貌和我们社会从媒体角度看上去的样子吗？还是我们希望看到一个经过“净化”的版本，将边缘部分清除掉？

老实说，我认为我们可能需要一个折中的方案，并且我们必须继续重新谈判边界，尽管这很困难。我们不希望LLM反映出人类社会中暴力、仇恨等现实中的可怕景象和污秽，这是我们世界的一部分，应该连一点点也不放大。[零内容审查不是答案。](https://open.substack.com/pub/platformer/p/why-platformer-is-leaving-substack?selection=61e54bce-0a54-44d7-9e24-86bc2ac24e36&utm_campaign=post-share-selection&utm_medium=web) 幸运的是，这一动机与运行这些模型的大型公司希望它们受大众欢迎并赚取大量金钱的愿望相吻合。

> …我们必须继续重新谈判边界，尽管这很困难。我们不希望LLM反映出人类社会中暴力、仇恨等现实中的可怕景象和污秽，这是我们世界的一部分，应该连一点点也不放大。零内容审查不是答案。

然而，我确实想继续温和地提出一个观点，那就是我们也可以从LLM世界中的这个困境中学到一些东西。与其在模型生成了一堆白人男性医生的图片时，仅仅感到愤怒并责怪技术，我们应该停下来思考一下为什么模型会生成这样的结果。然后我们应该深入讨论是否允许模型的这种反应，并基于我们的价值观和原则做出决定，并尽力实施。

正如我之前所说，LLM（大规模语言模型）并不是来自另一个宇宙的外星人，它就是我们。它是基于**我们**所写的、说的、拍摄的、录制的、做过的事情进行训练的。如果我们希望模型展示出各性别、性别认同、种族等不同背景的医生，我们就需要建立一个让所有这些不同类型的人都能接触到这个职业和所需教育的社会。如果我们只担心模型如何反映我们，但没有真正意识到需要改进的是我们自己，而不仅仅是模型，那么我们就错过了重点。

> 如果我们希望模型展示出各性别、性别认同、种族等不同背景的医生，我们就需要建立一个让所有这些不同类型的人都能接触到这个职业和所需教育的社会。

*我相信我不是唯一这么想的人，但由于Gemini从定义上是多模态的，不仅仅在训练中使用语言，还有音频、视频等，*“LLM”*似乎是个不太准确的术语。但我在网上找到的所有参考资料似乎仍然在使用这个词。*

*你可以在* [*www.stephaniekirmer.com.*](http://www.stephaniekirmer.com.) *找到更多我的作品。*

# 参考文献

[](https://www.vox.com/future-perfect/2024/2/28/24083814/google-gemini-ai-bias-ethics?source=post_page-----7b9505e901fd--------------------------------) [## 黑色纳粹？女教皇？这只是谷歌 AI 问题的开始。

### Gemini 图像生成器不仅仅是遇到技术问题，还面临着哲学上的难题。

www.vox.com](https://www.vox.com/future-perfect/2024/2/28/24083814/google-gemini-ai-bias-ethics?source=post_page-----7b9505e901fd--------------------------------) [](https://www.washingtonpost.com/technology/2024/02/22/google-gemini-ai-image-generation-pause/?pwapi_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyZWFzb24iOiJnaWZ0IiwibmJmIjoxNzA4ODM3MjAwLCJpc3MiOiJzdWJzY3JpcHRpb25zIiwiZXhwIjoxNzEwMjE1OTk5LCJpYXQiOjE3MDg4MzcyMDAsImp0aSI6IjFhMzAyYjkyLTRkN2ItNDNmMi1hNThlLWY1MDBjY2I2NDFjMyIsInVybCI6Imh0dHBzOi8vd3d3Lndhc2hpbmd0b25wb3N0LmNvbS90ZWNobm9sb2d5LzIwMjQvMDIvMjIvZ29vZ2xlLWdlbWluaS1haS1pbWFnZS1nZW5lcmF0aW9uLXBhdXNlLyJ9.E-JdVAohho0X-rTsTb1bfof4gIpYl8-NpPdZwL6h9Dc&source=post_page-----7b9505e901fd--------------------------------) [## 谷歌撤下 Gemini AI 图像生成器。这是你需要了解的内容。

### 批评者表示，谷歌的 Gemini 图像生成器创造了女教皇和黑人开国元勋的形象。

www.washingtonpost.com](https://www.washingtonpost.com/technology/2024/02/22/google-gemini-ai-image-generation-pause/?pwapi_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJyZWFzb24iOiJnaWZ0IiwibmJmIjoxNzA4ODM3MjAwLCJpc3MiOiJzdWJzY3JpcHRpb25zIiwiZXhwIjoxNzEwMjE1OTk5LCJpYXQiOjE3MDg4MzcyMDAsImp0aSI6IjFhMzAyYjkyLTRkN2ItNDNmMi1hNThlLWY1MDBjY2I2NDFjMyIsInVybCI6Imh0dHBzOi8vd3d3Lndhc2hpbmd0b25wb3N0LmNvbS90ZWNobm9sb2d5LzIwMjQvMDIvMjIvZ29vZ2xlLWdlbWluaS1haS1pbWFnZS1nZW5lcmF0aW9uLXBhdXNlLyJ9.E-JdVAohho0X-rTsTb1bfof4gIpYl8-NpPdZwL6h9Dc&source=post_page-----7b9505e901fd--------------------------------) [](https://www.wired.com/story/ai-list-dirty-naughty-obscene-bad-words/?source=post_page-----7b9505e901fd--------------------------------) [## AI 和脏话、恶作剧、淫秽以及其他不当词汇的列表

### 它最初是为了限制 Shutterstock 上的自动完成功能。现在它影响着 Slack 上的搜索建议，甚至影响着……

www.wired.com](https://www.wired.com/story/ai-list-dirty-naughty-obscene-bad-words/?source=post_page-----7b9505e901fd--------------------------------) [](https://www.boredpanda.com/people-with-dirty-last-names-problems/?source=post_page-----7b9505e901fd--------------------------------) [## 拥有“冒犯性”姓氏的人分享了他们的日常问题，这真是太搞笑了

### 有些人自出生以来就命运多舛，面临许多难题……

[www.boredpanda.com](https://www.boredpanda.com/people-with-dirty-last-names-problems/?source=post_page-----7b9505e901fd--------------------------------) [](https://open.substack.com/pub/platformer/p/why-platformer-is-leaving-substack?selection=61e54bce-0a54-44d7-9e24-86bc2ac24e36&utm_campaign=post-share-selection&utm_medium=web&source=post_page-----7b9505e901fd--------------------------------) [## 为什么 Platformer 要离开 Substack

### 我们以前看过这种情况——我们不会再待着等它上演完。

[open.substack.com](https://open.substack.com/pub/platformer/p/why-platformer-is-leaving-substack?selection=61e54bce-0a54-44d7-9e24-86bc2ac24e36&utm_campaign=post-share-selection&utm_medium=web&source=post_page-----7b9505e901fd--------------------------------)
