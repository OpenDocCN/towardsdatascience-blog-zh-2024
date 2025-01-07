# 看一看引擎盖下的情况

> 原文：[https://towardsdatascience.com/take-a-look-under-the-hood-24e40281c900?source=collection_archive---------7-----------------------#2024-06-13](https://towardsdatascience.com/take-a-look-under-the-hood-24e40281c900?source=collection_archive---------7-----------------------#2024-06-13)

## 使用单义性理解大型语言模型所学到的概念

[](https://medium.com/@doriandrost?source=post_page---byline--24e40281c900--------------------------------)[![Dorian Drost](../Images/1795395ad0586eafd83d3e2f7b975ca8.png)](https://medium.com/@doriandrost?source=post_page---byline--24e40281c900--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--24e40281c900--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--24e40281c900--------------------------------) [Dorian Drost](https://medium.com/@doriandrost?source=post_page---byline--24e40281c900--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--24e40281c900--------------------------------) ·阅读时间10分钟·2024年6月13日

--

![](../Images/2276f286b884bf6fec521e0d14acb17f.png)

就像大脑一样，理解LLM内部到底发生了什么是相当困难的。照片由[Robina Weermeijer](https://unsplash.com/@averey?utm_source=medium&utm_medium=referral)提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

随着大型语言模型（LLMs）使用的增加，我们对它们的推理和行为的理解需求也在增加。在本文中，我想向你介绍一种方法，它揭示了LLM内部表示的概念。在这种方法中，提取了一种表示方法，使人们能够理解模型的激活，并通过离散的概念来解释给定输入。这被称为[单义性](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html)，意味着这些概念只有一个（mono）含义（semantic）。

在这篇文章中，我将首先描述单义性（Monosemanticity）背后的主要思想。为此，我将解释稀疏自编码器，这是该方法中的核心机制，并展示它们是如何以可解释的方式构建大型语言模型（LLM）的激活的。接着，我将回顾一些单义性方法的作者提出的示范，以解释他们方法的洞见，这些洞见紧密跟随了[他们的原始出版物](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html)。

# 稀疏自编码器

![](../Images/57186dd272b00fbd5f6be5bcb887a1ec.png)

就像沙漏一样，自编码器有一个数据必须通过的瓶颈。照片由[Alexandar Todov](https://unsplash.com/@alexandar_todov?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们必须首先了解稀疏自编码器。首先，自编码器是一个神经网络，它的训练目标是重现给定的输入，也就是说，它应该产生与输入完全相同的向量。现在你可能会问，这样做的意义何在？关键的细节在于，自编码器有一些中间层，它们的大小比输入和输出都要小。通过这些层传递信息必然会导致信息丢失，因此模型不能仅仅通过记住元素来完全重现它。它必须通过一个瓶颈传递信息，因此需要提出一种密集的输入表示，它仍然能够尽可能地重现输入。我们将模型的前半部分称为*编码器*（从输入到瓶颈），后半部分称为*解码器*（从瓶颈到输出）。在训练完模型之后，你可以丢弃解码器。编码器现在将给定的输入转换为一种表示，保留了重要信息，但具有与输入不同的结构，并且可能去除了不需要的数据部分。

为了使自编码器变得*稀疏*，它的目标得到了扩展。除了尽可能地重建输入外，模型还被鼓励尽可能少地激活神经元。与其让所有神经元都稍微激活一下，不如集中精力激活其中的少数神经元，并且激活值要高。这也使得在模型中可以有更多的神经元，从而让瓶颈在模型架构中消失。然而，激活过多神经元会受到惩罚，这仍然保持了尽可能压缩数据的思想。被激活的神经元将被期望表示描述数据的有意义的关键概念。我们从现在开始称它们为*特征*。

在原始的《单义性》出版物中，这种稀疏自编码器是在[Claude 3 Sonnet](https://www.anthropic.com/news/claude-3-family)模型的中间层上训练的（Claude 3是由[Anthropic](https://www.anthropic.com/)发布的一个大型语言模型，可以说与OpenAI的GPT模型处于同一水平）。也就是说，你可以将一些标记（即文本片段）传递给Claude 3 Sonnet模型的前半部分，并将该激活传递给稀疏自编码器。然后，你将得到一个表示输入的特征激活。然而，到目前为止，我们并不真正知道这些特征是什么意思。为了找出答案，我们假设将以下文本输入模型：

+   猫在追逐狗。

+   我的猫整天躺在沙发上。

+   我没有猫。

如果有一个特征在所有三句话中都被激活，你可以猜测这个特征代表的是*猫*的概念。然而，也可能存在一些只在某一句话中激活而不在其他句子中激活的特征。对于第一句，你可以预期*狗*的特征会被激活；而为了表达第三句的意思，你可以预期会有一个代表某种*否定*或“*没有某物*”的特征。

## 不同的特征

![](../Images/89d3a1d1783a558a8342f1cd531ecf61.png)

特征可以描述非常不同的事物，从苹果和香蕉到可以食用和味道甜美的概念。照片由[Jonas Kakaroto](https://unsplash.com/@jkakaroto?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

从前面提到的例子中，我们看到特征可以描述非常不同的事物。有些特征代表具体的物体或实体（例如*猫*、*埃菲尔铁塔*或*班尼迪克特·康伯巴奇*），但也有一些特征专门表示更抽象的概念，如*悲伤*、*性别*、*革命*、*撒谎*、*能融化的东西*或*德语字母ß*（是的，确实有一个只属于我们的额外字母）。由于模型在训练过程中也接触过编程代码，因此它还包含了许多与编程语言相关的特征，表示诸如*代码错误*或*计算函数*之类的上下文。你可以在[这里](https://transformer-circuits.pub/2024/scaling-monosemanticity/features/index.html?featureId=1M_781220)探索Claude 3模型的特征。

如果模型能够使用多种语言进行表达，那么这些特征就是多语言的。这意味着，与*悲伤*相关的特征会在每种语言的相关句子中被激活。同样，如果模型能够处理不同的输入方式，那么这些特征也是多模态的。以班尼迪克特·康伯巴奇为例，该特征会在提到这个名字时被激活，也会在涉及到图片或口头提及班尼迪克特·康伯巴奇时被激活。

## 对行为的影响

![](../Images/7959beedc7cfd09f544567d96dcd39c4.png)

特征可以影响行为，就像方向盘影响你行驶的方式一样。照片由[Niklas Garnholz](https://unsplash.com/@niklasgarnholz?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

到目前为止，我们已经看到，当模型生成特定输出时，某些特征会被激活。然而，从模型的角度来看，因果关系的方向是相反的。如果*金门大桥*的特征被激活，它会促使模型生成与该特征概念相关的答案。接下来，我们将通过在模型推理过程中人为增加某个特征的激活来演示这一点。

![](../Images/9ad46a1d3f77a8cb239afab01a76049b.png)

模型的答案受某个特征高度激活的影响。图片来源于[原始出版物](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html)。

左侧，我们看到正常设置下两个问题的答案，而右侧则显示了如果*金门大桥*（第一行）和*大脑科学*（第二行）特征的激活程度增加时，这些答案如何变化。很直观，激活这些特征会让模型生成包含*金门大桥*和*大脑科学*概念的文本。在通常情况下，这些特征由模型的输入和提示激活，但在我们看到的方法中，也可以通过更有意图和明确的方式激活某些特征。你可以想象，始终激活*礼貌*特征来引导模型的回答朝着期望的方向发展。如果没有特征的概念，你可能需要通过在提示中添加“*在回答中始终保持礼貌*”这样的指示来实现，但通过特征概念，这可以更明确地完成。另一方面，你也可以考虑显式地去激活某些特征，以避免模型告诉你如何制造原子弹或进行逃税。

# 深入探讨：特异性、敏感性和完整性

![](../Images/4b6d406b56f3b48205cf43932f5ff416.png)

让我们更详细地观察这些特征。图片来自[K8](https://unsplash.com/@_k8_?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上。

现在我们已经理解了特征是如何提取的，我们可以参考一些作者的实验，展示模型实际学到的是哪些特征和概念。

首先，我们想知道特征的*特异性*，即它们与具体概念的契合度。我们可以问，代表本尼迪克特·康伯巴奇的特征是否**仅仅**对本尼迪克特·康伯巴奇起作用，而不会对其他演员产生作用？为了回答这个问题，作者们使用了一个LLM对文本进行评分，评估它们与给定概念的相关性。在以下示例中，评估了文本与“大脑科学”概念的相关性，评分范围从0（完全无关）到3（非常相关）。在下图中，我们看到这些评分以颜色表示（蓝色表示0，红色表示3），而在横轴上是激活水平。越往右走，特征的激活程度越高。

![](../Images/d38cb47ea6b34fb67388e5597e1985fe.png)

大脑科学特征的激活与输入的相关性得分。图片来源于[原始出版物](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html)。

我们看到激活（x轴）与相关性（颜色）之间有明确的相关性。激活越高，文本越常被认为与脑科学的主题高度相关。反过来，对于与脑科学主题关联较小或完全无关的文本，该特征仅在边缘上激活（如果有的话）。这意味着，该特征在脑科学主题上非常特定，并且对于相关主题（如心理学或医学）并不那么激活。

## 灵敏度

*特异性*的另一面是*灵敏度*。我们刚才看到的例子展示了一个特征仅在其主题上激活，而不是相关主题（至少不是那么多），这就是特异性。灵敏度现在提出的问题是：“它是否在**每个**提到该主题的地方都激活？”通常，灵敏性和特异性可以独立存在。一个特征可能只在脑科学的主题上激活（高特异性），但它可能在许多句子中都无法激活该主题（低灵敏度）。

作者在灵敏度的研究上花费的精力较少。然而，有一个易于理解的示范：*金门大桥*的特征会在许多不同语言中激活，尽管并未明确提到英文术语“Golden Gate Bridge”。更精细的分析在这里是相当困难的，因为并不总是清楚一个特征应该在细节上表示什么。例如，假设你有一个特征，你认为它表示*本尼迪克特·康伯巴奇*。现在你发现，它非常具体（仅对本尼迪克特·康伯巴奇有反应），但只对某些——而不是所有——图片做出反应。那么你如何知道，这个特征只是缺乏灵敏度，还是它代表的是一个更细化的子概念，例如*BBC系列中的福尔摩斯*（由本尼迪克特·康伯巴奇饰演）？

## 完整性

除了特征对其概念的激活（特异性和灵敏度）之外，你可能会想知道模型是否涵盖了所有重要的概念。然而，决定应该包含哪些概念是相当困难的。你真的*需要*为本尼迪克特·康伯巴奇（Benedict Cumberbatch）创建一个特征吗？“*悲伤*”与“*感到悲伤*”是两个不同的特征吗？“*行为不端*”是一个独立的特征，还是可以通过“*行为*”和“*否定*”这两个特征的组合来表示？

为了快速了解特征的完整性，作者选择了一些类别的概念，这些概念的数量有限，例如周期表中的元素。在下图中，我们可以看到所有元素位于x轴上，并且可以看到是否已经为三种不同大小的自编码器模型（从100万到3400万个参数）找到对应的特征。

![](../Images/3be6b6670fd4b10c506861c402e8e777.png)

各种尺寸的自编码器中具有周期表元素特征的情况。图片来自[原始出版物](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html)。

不足为奇的是，最大的自编码器具有比较小的自编码器更多的周期表元素特征。然而，它也并没有捕捉到所有的元素。不过，我们并不清楚这是否意味着模型没有清晰的概念，比如*博尔赫里乌姆*，还是它仅仅没有在自编码器中存活下来。

## 局限性

虽然我们看到了一些展示模型学习的概念特征的示范，但我们必须强调，这些实际上是定性的示范，而不是定量评估。所有的示例都很好地帮助我们了解模型实际上学到了什么，并展示了单义性方法的有用性。然而，需要一种正式的评估方式，系统地评估所有特征，才能真正支持从这些研究中获得的洞见。虽然这说起来容易，但实施起来很难，因为目前尚不清楚这种评估应该是什么样子。未来的研究需要找到方法，用定量和系统的数据来支撑这些示范。

# 摘要

![](../Images/0bdd7ec1db2de88e15c8aa73416d04f7.png)

单义性是一个有趣的路径，但我们还不知道它将把我们引向何方。照片由[Ksenia Kudelkina](https://unsplash.com/@kseny?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们刚刚看到了一种方法，可以帮助我们深入了解大型语言模型在得出答案时可能利用的概念。一些示范展示了如何以相当直观的方式解释通过稀疏自编码器提取的特征。这预示着一种新的理解大型语言模型的方法。如果你知道模型有一个*撒谎*的概念特征，你就能预期它会这样做，拥有*礼貌*的概念（与没有礼貌相比）会大大影响它的回答。对于给定的输入，这些特征还可以用来理解模型的思维轨迹。当请求模型讲一个故事时，*幸福结局*特征的激活可能解释了它如何得出某个结局，而当模型做你的税务申报时，你可能想知道*欺诈*的概念是否被激活。

如我们所见，理解LLM的潜力相当巨大。不过，仍然需要更加正式和系统化的特征评估，以支持这种分析格式所带来的承诺。

# 来源

本文基于这篇出版物，其中将单义性方法应用于大型语言模型（LLM）：

+   [Scaling Monosemanticity: Extracting Interpretable Features from Claude 3 Sonnet](https://transformer-circuits.pub/2024/scaling-monosemanticity/index.html)

还有一项前期工作介绍了更基础模型中的核心思想：

+   [走向单义性：通过词典学习解构语言模型](https://transformer-circuits.pub/2023/monosemantic-features/index.html)

关于已分析的Claude 3模型，请参见此处：

+   [https://www.anthropic.com/news/claude-3-family](https://www.anthropic.com/news/claude-3-family)

这些特性可以在这里探索：

+   [https://transformer-circuits.pub/2024/scaling-monosemanticity/features/index.html?featureId=1M_354889](https://transformer-circuits.pub/2024/scaling-monosemanticity/features/index.html?featureId=1M_354889)

*喜欢这篇文章吗？* [*关注我*](/@doriandrost) *以便收到我的未来更新。*
