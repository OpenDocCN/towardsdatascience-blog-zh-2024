# 生成性人工智能的经济学

> 原文：[https://towardsdatascience.com/economics-of-generative-ai-75f550288097?source=collection_archive---------0-----------------------#2024-08-01](https://towardsdatascience.com/economics-of-generative-ai-75f550288097?source=collection_archive---------0-----------------------#2024-08-01)

## 考虑到我们目前对这项技术和市场的了解，生成性人工智能的商业模式是什么？

[](https://medium.com/@s.kirmer?source=post_page---byline--75f550288097--------------------------------)[![Stephanie Kirmer](../Images/f9d9ef9167febde974c223dd4d8d6293.png)](https://medium.com/@s.kirmer?source=post_page---byline--75f550288097--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--75f550288097--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--75f550288097--------------------------------) [Stephanie Kirmer](https://medium.com/@s.kirmer?source=post_page---byline--75f550288097--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75f550288097--------------------------------) ·6分钟阅读·2024年8月1日

--

![](../Images/21594879c6d77dd1459c2d3922b3fbe2.png)

图片由[Ibrahim Rifath](https://unsplash.com/@ripey__?utm_source=medium&utm_medium=referral)提供，发布于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

> OpenAI已经建立了历史上增长最快的企业之一。它可能也是运营成本最高的企业之一。
> 
> 根据The Information的一项分析，ChatGPT的制造商今年可能会亏损多达50亿美元，该分析基于先前未公开的内部财务数据以及参与该业务的人士。如果我们的分析正确，OpenAI（最近估值为800亿美元）将在接下来的12个月内需要筹集更多的资金。
> 
> - [The Information](https://www.theinformation.com/articles/why-openai-could-lose-5-billion-this-year)

我在这里写作时花了一些时间讨论[技术](https://medium.com/towards-data-science/how-human-labor-enables-machine-learning-367feee8bc91)和[资源](https://medium.com/towards-data-science/environmental-implications-of-the-ai-boom-279300a24184)方面的限制，非常有趣的是，随着这些挑战变得越来越清晰且对围绕这项技术崛起的行业变得愈发紧迫，情况也在发生变化。

然而，我认为这个问题引出了一个关键问题，那就是生成性人工智能的商业模式究竟是什么？我们应该期待什么，又什么只是噱头？这项技术的承诺与实际现实之间的差距在哪里？

# 生成性人工智能是一个功能还是一个产品？

我与一些人讨论过这个话题，也在媒体上听到过很多相关讨论。技术是作为*功能*还是*产品*的区别，基本上取决于它是否在独立使用时具备足够的价值，让人们愿意单独购买使用，还是它是否在与其他技术结合时展现出大部分或全部的价值。现在我们看到“AI”被添加到许多现有的产品中，从文本/代码编辑器到搜索引擎，再到浏览器，这些应用都是“生成式 AI 作为功能”的例子。（我现在就在 Notion 中写这段文字，它一直试图让我用 AI 做点什么。）另一方面，我们看到 Anthropic、OpenAI 和其他一些公司正在尝试销售以生成式 AI 为核心的产品，比如 ChatGPT 或 Claude。

这可能开始变得有些模糊，但我认为关键因素是，对于“生成式 AI 作为产品”这一群体来说，如果生成式 AI 无法满足客户的期望，无论是什么期望，那么他们就会停止使用这个产品，并停止支付给服务提供商。另一方面，如果有人发现（可以理解的）Google 的 AI 搜索摘要很糟糕，他们可以抱怨并关闭它，然后像以前一样继续使用 Google 搜索。核心商业价值主张并不是建立在 AI 的基础上的，它只是一个额外的潜在卖点。这使得整体业务的风险大大降低。

苹果在生成式 AI 领域的做法是一个很好的例子，它将生成式 AI 概念化为功能，而不是产品，在我看来，他们显然的策略更具潜力。在最近的 WWDC 上，苹果透露他们正在与 OpenAI 合作，让苹果用户通过 Siri 访问 ChatGPT。这里有几个关键点非常重要。首先，苹果并没有向 OpenAI 支付任何费用来建立这种合作关系——苹果带来了其具有高度经济吸引力的用户群，而 OpenAI 则有机会将这些用户转化为 ChatGPT 的付费订阅者，如果他们能够做到的话。苹果在这一关系中没有承担任何风险。其次，这并不排除苹果以相同的方式为他们的用户提供其他生成式 AI 服务，比如 Anthropic 或 Google 的产品。他们并没有明确押注于生成式 AI 更大竞争格局中的某一方，尽管 OpenAI 恰好是第一个宣布的合作伙伴。当然，苹果也在开发[Apple AI，他们自己的生成式 AI 解决方案](https://www.cnbc.com/2024/07/29/apple-releases-apple-intelligence-its-long-awaited-ai-features.html)，但显然他们的目标是将这些服务作为增强现有和未来产品线的一部分——使你的 iPhone 更有用——而不是将模型作为独立的产品进行销售。

所以，以上是想说生成式 AI 如何以及应当如何融入商业战略的思考方式有很多种，而仅仅构建技术本身并不能保证它会是最成功的。十年后回头看，我怀疑我们认为是生成式 AI 业务领域“最大赢家”的公司，是否会是那些实际开发了底层技术的公司。

# 什么样的商业战略对于发展是有意义的？

好吧，你可能会想，但是有人必须去打造它，如果这些功能足够有价值，值得拥有，对吧？如果钱并不在于生成式 AI 能力的实际创建上，那我们会拥有这种能力吗？它会发挥其全部潜力吗？

我应该承认，很多科技领域的投资者确实相信生成式 AI 有巨大的赚钱潜力，这也是他们已经向 OpenAI 及其同行投资了数十亿美元的原因。然而，我在之前的几篇文章中也提到过，即使有这些数十亿资金，我还是强烈怀疑，未来我们将看到的生成式 AI 性能改进只会是轻微的、渐进式的，而不是继续我们在 2022–2023 年看到的似乎是指数级的技术进步。（特别是，为了实现承诺的进展，训练所需的海量人类生成数据的限制，不能仅通过投钱来解决。）这意味着我并不相信生成式 AI 会变得比现在更加有用或“聪明”。

说到这些，不管你是否同意我的观点，我们都应该记住，拥有一项高度先进的技术与能够利用这项技术创造出人们愿意购买的产品并从中建立一个可持续的、可再生的商业模式是完全不同的。你可以发明一个很酷的新东西，但正如任何初创公司或科技公司中的产品团队所告诉你的那样，这并不是过程的终点。弄清楚普通人如何以及为何会使用你的新东西，并将这一点传达出去，让人们相信你的新东西值一个可持续的价格，这是极其困难的。

我们确实看到了很多来自各个渠道的提议，但其中一些想法的效果相当差。[OpenAI上周宣布的搜索引擎新测试版，已经在输出结果中出现了重大错误](https://www.theatlantic.com/technology/archive/2024/07/searchgpt-openai-error/679248/)。任何读过[我之前的文章](https://medium.com/towards-data-science/what-does-it-mean-when-machine-learning-makes-a-mistake-37b213200697)和[其他文章](https://medium.com/towards-data-science/is-generative-ai-taking-over-the-world-a970a5ccdad5)的人都不会感到惊讶。（我个人只是感到惊讶，他们在开发这个产品时，居然没有考虑到这个显而易见的问题。）即便是那些看起来有吸引力的想法，也不能只是“可有可无”或者奢侈品，它们必须是必不可少的，因为要使这个业务可持续所需的成本必须非常高。当你的烧钱速度达到每年50亿美元时，为了实现盈利并自我维持，你的付费用户数量必须是天文数字，或者用户支付的价格必须令人瞠目结舌。

# 难道研究本身就不具备内在的价值吗？

这让那些最想推动技术边界的人处于一个困难的境地。为了研究而研究的行为一直以某种形式存在，即使其结果并不立即具有实际应用价值。但资本主义并没有一个好的渠道来维持这种工作，尤其是在这种研究的参与成本高得令人难以置信的情况下。几十年来，美国一直在把学术机构的资源榨干，因此学者和[学术研究人员几乎没有机会参与这种研究，除非有私人投资](https://www.washingtonpost.com/technology/2024/03/10/big-tech-companies-ai-research/)。

我认为这真是太遗憾了，因为学术界正是可以在适当监管下进行这种研究的地方。伦理、安全和风险问题可以在学术环境中得到认真对待和探索，而这些问题在私营部门根本不会得到优先考虑。学术研究的文化和规范能够让学者把知识置于金钱之上，但当私营部门企业主导所有研究时，这些选择就发生了变化。我们的社会信任做“更纯粹”研究的人，并没有获得足够的资源来显著参与生成性AI的蓬勃发展。

# 那接下来怎么办？

当然，即便是这些私营公司，也很可能没有足够的资源来支撑继续训练更多、更大的模型的疯狂竞赛，这又将我们带回我在本文开头提到的那句话。由于支配我们技术进步的经济模型，我们可能会错失潜在的机会。那些有意义的生成性人工智能应用，虽然合理，却未必能赚取足够的数十亿，以支撑GPU的费用，因此可能永远无法深入探索；而那些社会有害、愚蠢或无用的应用则可能获得投资，因为它们带来了更大的现金收益机会。

欲了解更多我的作品，请访问 [www.stephaniekirmer.com.](http://www.stephaniekirmer.com.)

# 进一步阅读

[https://www.theatlantic.com/technology/archive/2024/07/searchgpt-openai-error/679248/](https://www.theatlantic.com/technology/archive/2024/07/searchgpt-openai-error/679248/)

[https://www.washingtonpost.com/technology/2024/03/10/big-tech-companies-ai-research/](https://www.washingtonpost.com/technology/2024/03/10/big-tech-companies-ai-research/)
