# 声音图像：用AI创作令人惊叹的视听艺术

> 原文：[https://towardsdatascience.com/images-that-sound-creating-stunning-audiovisual-art-with-ai-024a317c7472?source=collection_archive---------2-----------------------#2024-08-05](https://towardsdatascience.com/images-that-sound-creating-stunning-audiovisual-art-with-ai-024a317c7472?source=collection_archive---------2-----------------------#2024-08-05)

[](https://medium.com/@maxhilsdorf?source=post_page---byline--024a317c7472--------------------------------)[![Max Hilsdorf](../Images/01da76c553e43d5ed6b6849bdbfd00da.png)](https://medium.com/@maxhilsdorf?source=post_page---byline--024a317c7472--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--024a317c7472--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--024a317c7472--------------------------------) [Max Hilsdorf](https://medium.com/@maxhilsdorf?source=post_page---byline--024a317c7472--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--024a317c7472--------------------------------) ·8分钟阅读·2024年8月5日

--

# 背景

你可能已经见过AI生成图像，比如这四只柯基犬。

![](../Images/8d9d61768e52f3121749a0b167aa5d7a.png)

声音图像：在单一画布上创作图像与声音 — [https://arxiv.org/pdf/2405.12221](https://arxiv.org/pdf/2405.12221)

也许你曾见过AI生成声音，例如这些柯基犬的叫声：

如果我告诉你这两种生成方式是完全相同的，你会怎么想？自己看看并听听吧！

现在，你可能对我说的“它们是一样的”感到困惑。但不用担心，你很快就会明白！

2024年5月，来自密歇根大学的三位研究人员发布了一篇名为[《声音图像：在单一画布上创作图像与声音》](https://arxiv.org/pdf/2405.12221)的论文。

在这篇文章中，我将解释

1.  什么是生成“声音图像”，以及这与人类之前的工作有何关联

1.  本模型如何在技术层面上工作，以通俗易懂的方式呈现

1.  为什么这篇论文挑战了我们对AI可以做什么以及应该做什么的理解

# 什么是声音图像？

要回答这个问题，我们需要理解两个术语：

1.  波形

1.  声谱图

在现实世界中，声音是由物体的振动产生的声波（随着时间推移的空气压力变化）。当通过麦克风捕捉到声音或通过数字合成器生成声音时，我们可以将这种声音波形表示为波形图：

![](../Images/7a29480323464d1286efbc88fd608ecc.png)

一首声音歌曲的波形。音乐与图像由作者创作。

波形对于记录和播放音频很有用，但通常在音乐分析或音频数据的机器学习中被避免。相反，使用的是一种更具信息量的信号表示——**声谱图**。

![](../Images/01b95620dae17bc6e8b6aa452c876577.png)

一首声学歌曲的梅尔频谱图。音乐和图像由作者提供。

声谱图告诉我们哪些频率在时间上更突出或较弱。然而，对于本文，关键要注意的是，声谱图是一种图像。由此，我们回到了最初的概念。

在生成上面的柯基声音和图像时，人工智能创造了一种声音，这种声音在转换成声谱图后，看起来像一只柯基。

这意味着该人工智能的输出同时既是声音又是图像。

# 人工智能是如何生成这些艺术作品的？

尽管你现在理解了“发声的图像”是什么意思，但你可能仍然想知道这怎么可能。人工智能是如何知道哪个声音会生成所需的图像呢？毕竟，柯基声音的波形看起来与柯基完全不相似。

![](../Images/82f1b8103f8259b0ecbcc20db1943364.png)

由“发声的图像”生成的柯基声音的波形。图像由作者提供。

首先，我们需要理解一个基础概念：扩散模型。扩散模型是像 DALL-E 3 或 Midjourney 这样的图像模型背后的技术。实质上，扩散模型将用户的提示编码成一个数学表示（一个嵌入），然后从随机噪声中一步步生成所需的输出图像。

这是使用扩散模型创建图像的工作流程

1.  使用人工神经网络将提示编码成一个嵌入（一些数字）

1.  初始化一个带有白噪声（高斯噪声）的图像

1.  逐步去噪图像。基于提示嵌入，扩散模型确定一个最优的小去噪步骤，使图像更接近提示描述。我们将其称为**去噪指令**。

1.  重复去噪步骤，直到生成无噪声的高质量图像

![](../Images/355f6d2080168bcfaf7da17c3e681dc5.png)

图像扩散模型的高级内部工作原理。图像由作者提供。

为了生成“发声的图像”，研究人员采用了一种巧妙的技术，将两个扩散模型结合为一个。一个扩散模型是文本到图像模型**（**[Stable Diffusion](https://arxiv.org/abs/2112.10752)**）**，另一个是文本到声谱图模型**（**[Auffusion](https://arxiv.org/pdf/2401.01044)**）**。每个模型接收自己的提示，提示被编码成一个嵌入，并决定其自己的去噪指令。

然而，多个不同的去噪指令是有问题的，因为模型需要决定如何去噪图像。在论文中，作者通过对两个提示的去噪指令进行平均，解决了这个问题，从而有效地引导模型平等地优化这两个提示。

![](../Images/484825ab7e29159658a52709ee2073f1.png)

“能发声的图像”高级内部机制。图片由作者提供。

从高层次来看，你可以把它理解为确保最终的图像能同等地反映图像和音频提示。这样做的一个缺点是，输出结果总是两者的混合，而模型产生的每个声音或图像并不一定都会很好看/好听。这种固有的权衡显著限制了模型的输出质量。

# 本文如何挑战我们对AI的理解

## AI仅仅是模仿人类智能吗？

AI通常被定义为模仿人类智能的计算机系统（例如[IMB](https://www.ibm.com/topics/artificial-intelligence)、[TechTarget](https://www.techtarget.com/searchenterpriseai/definition/AI-Artificial-Intelligence)、[Coursera](https://www.coursera.org/articles/what-is-artificial-intelligence)）。这种定义在销售预测、图像分类和文本生成等AI模型中效果良好。然而，它带来了一个固有的限制，即计算机系统只有在执行人类历史上已解决的任务时，才能被视为AI。

在现实世界中，存在着大量（可能是无限多）可以通过智能解决的问题。尽管人类智能已经解决了一些问题，但大多数问题仍未解决。在这些未解决的问题中，有些是已知的（例如治愈癌症、量子计算、意识的本质），而其他则是未知的。如果你的目标是解决这些未解之谜，模仿人类智能似乎并不是最优策略。

![](../Images/b1bfc6e562832fcce2db5d3d4d265c02.png)

图片由作者提供。

根据上述定义，若一个计算机系统能够发现治愈癌症的方法，但不模仿人类智能，它将不被视为AI。这显然是违反直觉并且适得其反的。我并不打算开始关于“唯一定义”的辩论，而是想强调，AI远不止是人类智能的自动化工具。它有潜力解决我们甚至未曾意识到存在的问题。

## 能否通过人类智能生成频谱图艺术？

在[Mixmag的文章](https://mixmag.net/feature/spectrogram-art-music-aphex-twin)中，Becky Buckle探讨了“艺术家如何将视觉元素隐藏在音乐的波形中”的历史。一个令人印象深刻的人类频谱图艺术的例子是英国音乐人Aphex Twin的歌曲《∆Mᵢ⁻¹=−α ∑ Dᵢ[η][ ∑ Fjᵢ[η−1]+Fextᵢ [η⁻¹]]》。

![](../Images/2bc35c71f72ca3af20f06a034d3d8a92.png)

Aphex Twin的《∆Mᵢ⁻¹=−α ∑ Dᵢ[η][ ∑ Fjᵢ[η−1]+Fextᵢ [η⁻¹]]》中外星人面孔的截图。[视频链接。](https://www.youtube.com/watch?v=wSYAZnQmffg&ab_channel=Maxdamantus)

另一个例子是加拿大音乐人Venetian Snares的专辑《Songs about my Cats》中的曲目《Look》。

![](../Images/3b722e0f35f03aafa55d6425651f6ca1.png)

Venetian Snares的《Look》中的猫咪图像截图。[视频链接](https://www.youtube.com/watch?v=BHup81lEjqo&ab_channel=monotoniac)。

尽管这两个例子展示了人类如何将图像编码为波形，但“声音图像”的能力有明显的区别。

## “声音图像”与人类声谱艺术有何不同？

如果你听上面这些人类声谱艺术的例子，你会注意到它们听起来像噪声。对于外星人的面孔来说，这也许是一个合适的音乐背景。然而，听到猫的例子后，似乎声音和声谱图像之间并没有什么有意的联系。人类作曲家能够生成在转化为声谱图时看起来像某种事物的波形。然而，据我所知，还没有人能够生成声音和图像在预定标准下完全匹配的例子。

“声音图像”可以生成听起来像猫并且看起来像猫的声音。它还可以生成听起来像太空船并且看起来像海豚的声音。它能够产生声音与图像之间的有意联系，展现了人工智能的非人类智能。

## “声音图像”没有实际应用案例。这正是它美丽的地方。

近年来，人工智能大多被描绘为一种通过自动化提升经济产出的生产力工具。虽然大多数人会同意，在某种程度上这一点是非常值得追求的，但也有一些人对此未来的观点感到威胁。毕竟，如果人工智能不断取代人类的工作，它可能最终会取代我们热爱的工作。因此，我们的生活可能会变得更加高效，但却少了意义。

“声音图像”对比了这种观点，并且是美丽人工智能艺术的典范。这个作品并非由经济问题驱动，而是出于好奇心和创造力。尽管我们永远不能说绝对不可能，但这项技术不太可能有任何经济应用案例……

在我与众多谈论人工智能的人交谈后，艺术家们往往对人工智能持最为消极的态度。这一点得到了最近德国GEMA的[研究](https://www.gema.de/en/news/ai-study)的支持，研究显示，超过60%的音乐家“认为人工智能的使用风险大于其潜在机会”，而只有11%的人“认为机会大于风险”。

类似这篇论文的更多作品能够帮助艺术家理解，人工智能有潜力将更多美丽的艺术带入世界，而这一切并不一定要以牺牲人类创作者为代价。

# 展望：人工智能在艺术创作中的其他创意应用

“声音图像”并不是第一个有潜力创造美丽艺术的人工智能应用。在这一部分，我想展示一些其他的人工智能艺术尝试，希望能激发你的灵感，并让你对人工智能产生不同的思考。

## 修复艺术

![](../Images/2a605af850dc0b4198e6336a1c871ce0.png)

这幅《亚马逊战士之战》的马赛克图像，由人工智能重建。取自[这篇论文](https://arxiv.org/pdf/2210.06145)。

AI通过精准修复受损的艺术品，帮助恢复艺术，确保历史作品能保存得更久。这种技术与创造力的结合使我们的艺术遗产得以为未来世代保存。[阅读更多](/how-ai-could-help-preserve-art-f40c8376781d)。

## 让画作复生

一段YouTube视频，展示蒙娜丽莎说唱《Paparazzi》（AI生成）。

AI可以为照片添加动画，创建具有自然动作和同步嘴型的真实视频。这使得历史人物或艺术作品，如蒙娜丽莎，可以动起来并说话（或说唱）。虽然在深度伪造的背景下，这项技术无疑是危险的，但如果应用于历史肖像，它可以创造出有趣和/或有意义的艺术。[阅读更多]

## 将单声道录音转为立体声

AI有潜力通过将单声道混音转换为立体声混音来增强旧录音。虽然有经典的算法方法来实现这一点，但AI承诺使人工立体声混音听起来越来越真实。[阅读更多](https://www.alango.com/technologies-mono2stereo.php) 和 [阅读更多](https://arxiv.org/pdf/2306.14647)。

# 结论

《发出声音的图像》是我2024年最喜欢的论文之一。它使用先进的AI训练技术，达成一种纯粹艺术性的成果，创造出一种全新的视听艺术形式。最令人着迷的是，这种艺术形式目前超出了人类的能力范围。我们可以从这篇论文中了解到，AI不仅仅是模仿人类行为的一套自动化工具。相反，AI可以通过提升现有的艺术作品或创作全新的作品和艺术形式，丰富我们生活中的美学体验。我们才刚刚看到AI革命的开始，我迫不及待地想要塑造和体验它（艺术方面）的后果。

# 关于我

我是一名音乐学家和数据科学家，分享我对当前AI与音乐话题的看法。以下是我与这篇文章相关的一些之前的工作：

+   **2024年3个音乐AI突破**：[https://towardsdatascience.com/3-music-ai-breakthroughs-to-expect-in-2024-2d945ae6b5fd](/3-music-ai-breakthroughs-to-expect-in-2024-2d945ae6b5fd)

+   **Meta的AI如何基于参考旋律生成音乐**：[https://medium.com/towards-data-science/how-metas-ai-generates-music-based-on-a-reference-melody-de34acd783](https://medium.com/towards-data-science/how-metas-ai-generates-music-based-on-a-reference-melody-de34acd783)

+   **AI音乐源分离：它是如何工作的，为什么这么难**：[https://medium.com/towards-data-science/ai-music-source-separation-how-it-works-and-why-it-is-so-hard-187852e54752](https://medium.com/towards-data-science/ai-music-source-separation-how-it-works-and-why-it-is-so-hard-187852e54752)

在[Medium](https://medium.com/@maxhilsdorf)和[Linkedin](https://www.linkedin.com/in/max-hilsdorf/)上找到我！
