# OpenAI的Sora如何改变游戏规则：深入了解其核心技术

> 原文：[https://towardsdatascience.com/how-openais-sora-is-changing-the-game-an-insight-into-its-core-technologies-bd1ad17170df?source=collection_archive---------4-----------------------#2024-02-19](https://towardsdatascience.com/how-openais-sora-is-changing-the-game-an-insight-into-its-core-technologies-bd1ad17170df?source=collection_archive---------4-----------------------#2024-02-19)

## 一项代表前沿技术的杰作

[](https://rkiuchir.medium.com/?source=post_page---byline--bd1ad17170df--------------------------------)[![Ryota Kiuchi, Ph.D.](../Images/5459c434848898345d932320c4a01312.png)](https://rkiuchir.medium.com/?source=post_page---byline--bd1ad17170df--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bd1ad17170df--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bd1ad17170df--------------------------------) [Ryota Kiuchi, Ph.D.](https://rkiuchir.medium.com/?source=post_page---byline--bd1ad17170df--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bd1ad17170df--------------------------------) ·12分钟阅读·2024年2月19日

--

![](../Images/4045008b97b49145c5838da473557a91.png)

图片由[Kaushik Panchal](https://unsplash.com/@kaushikpanchal?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

2024年2月15日，OpenAI在2022年底震惊世界发布ChatGPT之后，再次通过揭示Sora震撼全球。这项技术能够根据文本提示生成最长达一分钟的视频，毫无疑问，它将成为一次突破。

在这篇博客文章中，我将基于OpenAI发布的技术报告，介绍这项令人惊叹的技术背后的基础方法和研究。

顺便提一下，“Sora”在日语中意味着“天空”。尽管官方尚未宣布这一命名是否有意为之，但据推测可能是有意的，因为他们的官方发布推文中有一段以东京为主题的视频。

OpenAI通过X平台向全球发布Sora

# 目录

+   [关于Sora](#bfb1)

+   [它背后是什么样的技术和研究？](#7e7b)

+   [这些研究努力为Sora带来的能力](#7b12)

+   [Sora的未来](#642d)

# 关于Sora

Sora是OpenAI开发的先进文本到视频转换模型，其能力和应用范围展示了现代AI技术的新视野。这个模型不仅限于生成几秒钟的视频；它可以创建最长一分钟的视频，在保持高视觉质量的同时，忠实地再现用户指令。就像是将梦想变为现实。

OpenAI Sora通过X平台进行演示

## **基于现实世界生成复杂场景**

Sora 理解提示中描述的元素如何在物理世界中存在和运作。这使得模型能够准确地呈现用户意图的动作和视频中的运动。例如，它可以逼真地重现一个人奔跑的场景或自然现象的运动。此外，它还能够再现多个角色、各种运动类型以及主题和背景的精确细节。

以前，使用生成性 AI 创建视频面临着在不同场景之间保持一致性和可重现性的巨大挑战。这是因为在生成每个场景或帧时，完全理解先前的上下文和细节，并将其适当传递到下一个场景是困难的。然而，这个模型通过将对语言的深刻理解与视觉上下文结合，准确解释提示，从而保持叙事一致性。它还能够捕捉角色的情感和个性，并在视频中呈现为富有表现力的角色。

Bill Peebles（OpenAI）通过 X 发布的帖子

# 它背后是什么样的技术和研究？

![](../Images/5855f8b4ebf6f0eabb6f06a22673761c.png)

由[Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Sora 是建立在先前图像数据生成建模研究的基础之上的。先前的研究使用了各种方法，如递归网络、生成对抗网络（GANs）、自回归 Transformer 和扩散模型，但通常集中于狭窄类别的视觉数据、较短的视频或固定尺寸的视频。Sora 超越了这些局限，并在生成各种时长、纵横比和分辨率的视频方面取得了显著进展。在这一部分，我将介绍支持这些进展的核心技术。

## 1. Transformer

> Vaswani 等人（2017），《Attention is all you need》

Transformer 模型是一种神经网络架构，彻底改变了自然语言处理（NLP）领域。它由 Vaswani 等人于 2017 年首次提出。该模型显著克服了传统递归神经网络（RNNs）和卷积神经网络（CNNs）面临的挑战，作为一种创新方法，今天支持了多种突破性的技术。

![](../Images/2295b7bce37a22b9414d8777b53f8951.png)

图1：Transformer——模型架构。｜Vaswani 等人（2017）

RNNs的问题：

+   长期依赖问题：尽管 RNNs 理论上可以通过时间传递信息，但在实践中它们难以捕捉长时间跨度的依赖关系。

+   并行化的局限性：由于RNN中每一步的计算依赖于前一步的输出，因此必须进行顺序处理（例如，逐一处理文本中的单词或句子），无法利用现代计算机架构所提供的并行处理优势。这使得在大规模数据集上的训练效率低下。

CNN的问题：

+   固定感受野大小：虽然CNN在提取局部特征方面表现优秀，但其固定的感受野大小限制了其捕捉全局上下文中长距离依赖的能力。

+   自然语言层级结构建模的困难：直接建模语言的层级结构具有挑战性，而这对于深层次的上下文理解可能不够充分。

Transformer的新特性：

+   注意力机制：使得能够直接建模序列中任意位置之间的依赖关系，从而直接捕捉长距离依赖和广泛的上下文。

+   并行化的实现：由于输入数据一次性整体处理，实现了高度的并行计算，大大加速了在大规模数据集上的训练。

+   可变感受野：注意力机制使得模型能够根据需要动态调整“感受野”的大小。这意味着模型可以在某些任务或数据中自然地聚焦于局部信息，而在其他情况下考虑更广泛的上下文。

*更多关于Transformer的详细技术解释：*

[](/transformers-141e32e69591?source=post_page-----bd1ad17170df--------------------------------) [## Transformer的工作原理

### Transformer是一种神经网络架构，近年来越来越受到关注。Transformer最近…

towardsdatascience.com](/transformers-141e32e69591?source=post_page-----bd1ad17170df--------------------------------)

## 2\. 视觉Transformer（ViT）

> Dosovitskiy等人（2020年），“一张图等于16x16个词：用于大规模图像识别的Transformer。”

本研究将革命性地改变自然语言处理（NLP）的Transformer原理应用于图像识别，开辟了新的视野。

**Token和Patch**

在原始的Transformer论文中，tokens主要代表单词或句子的部分，分析这些tokens之间的关系可以深入理解句子的含义。在本研究中，为了将这种token的概念应用于视觉数据，图像被划分为16x16的小块（patches），每个小块被视为Transformer中的一个“token”。这种方法使得模型能够学习每个小块在整个图像中的关系，从而基于此进行整个图像的识别和理解。它突破了传统CNN模型在图像识别中固定感受野大小的局限性，实现了在图像内任意位置关系的灵活捕捉。

![](../Images/4b9ab7bd066f9cd9c49e6815c9b9752f.png)

图1：模型概述。｜Dosovitskiy 等人（2020）

*关于ViT的更详细技术解释：*

[https://machinelearningmastery.com/the-vision-transformer-model/](https://machinelearningmastery.com/the-vision-transformer-model/)

## 3. 视频视觉变换器（ViViT）

> Arnab 等人（2021），“Vivit: A video vision transformer.”

ViViT进一步扩展了视觉变换器的概念，将其应用于视频的多维数据。视频数据更为复杂，因为它既包含静态图像信息（空间元素），又包含随时间变化的动态信息（时间元素）。ViViT将视频分解为时空补丁，并将其视为变换器模型中的令牌。通过引入时空补丁，ViViT能够同时捕捉视频中的静态和动态元素，并建模它们之间的复杂关系。

![](../Images/24cf64ec81ec07779af159c87eae68d6.png)

图3：Tubelet（时空输入体积）嵌入图像。｜Arnab 等人（2021）

*关于ViViT的更详细技术解释：*

[](https://medium.com/aiguys/vivit-video-vision-transformer-648a5fff68a4?source=post_page-----bd1ad17170df--------------------------------) [## ViViT 📹 视频视觉变换器

### ICCV 2021 ✨，谷歌研究

medium.com](https://medium.com/aiguys/vivit-video-vision-transformer-648a5fff68a4?source=post_page-----bd1ad17170df--------------------------------)

## 4. Masked Autoencoders (MAE)

> He 等人（2022），“Masked autoencoders are scalable vision learners.”

本研究通过使用一种自监督预训练方法，称为Masked Autoencoder，显著提高了传统上在高维度和大量信息数据集上训练时的高计算成本和低效率。

具体来说，通过对输入图像的部分区域进行遮挡，网络被训练去预测隐藏部分的信息，从而更有效地学习图像中的重要特征和结构，并获得丰富的视觉数据表示。这一过程使得数据的压缩与表示学习变得更加高效，减少了计算成本，并增强了不同类型视觉数据和任务的通用性。

本研究的方法与BERT（双向编码器表示的变换器）在语言模型发展中的应用紧密相关。BERT通过Masked Language Modeling（MLM）实现了对文本数据的深层上下文理解，而He等人将类似的遮挡技术应用于视觉数据，从而实现了对图像的更深理解与表示。

![](../Images/3933fb20cd83083872d34a3afdc4c2c3.png)

图1：Masked Autoencoders 图像。｜He 等人（2022）

*关于MAE的更详细技术解释：*

[](/paper-explained-masked-autoencoders-are-scalable-vision-learners-9dea5c5c91f0?source=post_page-----bd1ad17170df--------------------------------) [## 论文解析：Masked Autoencoders 是可扩展的视觉学习者

### 如何重建图像中的遮挡部分能带来好处

[towardsdatascience.com](/paper-explained-masked-autoencoders-are-scalable-vision-learners-9dea5c5c91f0?source=post_page-----bd1ad17170df--------------------------------)

## 5. 本地分辨率视觉 Transformer（NaViT）

> Dehghani 等人 (2023)，“Patch n’Pack: NaViT，一种适用于任何纵横比和分辨率的视觉 Transformer。”

本研究提出了本地分辨率 ViTransformer（NaViT），这是一个旨在进一步扩展视觉 Transformer（ViT）应用于任何纵横比或分辨率图像的模型。

**传统 ViT 的挑战**

视觉 Transformer 通过将图像分割为固定大小的补丁并将这些补丁作为令牌来处理，从而提出了一种开创性的方法，将 Transformer 模型应用于图像识别任务。然而，这种方法假设模型是针对特定分辨率或纵横比进行优化的，因此需要对不同大小或形状的图像进行模型重新调整。这是一个重大限制，因为实际应用中通常需要处理不同大小和纵横比的图像。

**NaViT 的创新**

NaViT 设计旨在高效处理任何纵横比或分辨率的图像，使其能够直接输入模型而无需预先调整。Sora 也将这种灵活性应用于视频，显著提升了灵活性和适应性，能够无缝处理各种大小和形状的图像与视频。

![](../Images/47627d8a0fd5e4cd491dd460ad0389f2.png)

图 2：｜Dehghani 等人 (2023)

## 6. 扩散模型

> Sohl-Dickstein 等人 (2015)，“使用非平衡热力学的深度无监督学习。”

除了 Transformer，扩散模型也是支撑 Sora 的核心技术之一。此研究为扩散模型奠定了理论基础，扩散模型是一种使用非平衡热力学的深度学习模型。扩散模型引入了一个扩散过程的概念，该过程从随机噪声（没有任何模式的数据）开始，逐步去除噪声，生成类似于实际图像或视频的数据。

例如，可以想象从仅仅是随机的点开始，这些点逐渐转变为美丽景观或人物的视频。这种方法后来被应用于生成复杂的数据，如图像和声音，促进了高质量生成模型的发展。

![](../Images/5b45899f9277252955107f0d698a691c.png)

去噪过程的图像｜图像来源 (OpenAI)

> Ho 等人 (2020)，“去噪扩散概率模型。”
> 
> Nichol 和 Dhariwal (2021)，“改进的去噪扩散概率模型。”

基于Sohl-Dickstein 等人（2015年）提出的理论框架，开发了被称为去噪扩散概率模型（DDPM）的实用数据生成模型。该模型在高质量图像生成方面表现出了特别显著的效果，证明了扩散模型的有效性。

**扩散模型对Sora的影响**

通常，训练机器学习模型需要大量的标注数据（例如，被告知“这是一张猫的图片”）。然而，扩散模型也可以从未标注的数据中学习，使其能够利用互联网上海量的视觉内容生成各种类型的视频。换句话说，Sora可以观察不同的视频和图像，并学习“这就是正常视频的样子”。

*有关扩散模型的更详细技术解释：*

[](/diffusion-models-made-easy-8414298ce4da?source=post_page-----bd1ad17170df--------------------------------) [## 扩散模型轻松实现

### 理解去噪扩散概率模型的基础

towardsdatascience.com](/diffusion-models-made-easy-8414298ce4da?source=post_page-----bd1ad17170df--------------------------------) [](/understanding-the-denoising-diffusion-probabilistic-model-the-socratic-way-445c1bdc5756?source=post_page-----bd1ad17170df--------------------------------) [## 通过苏格拉底式方法理解去噪扩散概率模型

### 深入探讨去噪扩散模型背后的动机以及损失函数的详细推导

towardsdatascience.com](/understanding-the-denoising-diffusion-probabilistic-model-the-socratic-way-445c1bdc5756?source=post_page-----bd1ad17170df--------------------------------)

## 7\. 潜在扩散模型

> Rombach 等人（2022年），《使用潜在扩散模型进行高分辨率图像合成》

该研究在使用扩散模型进行高分辨率图像合成领域作出了重要贡献。它提出了一种方法，通过在潜在空间中利用扩散模型，在保持质量的同时显著降低了与直接高分辨率图像生成相比的计算成本。换句话说，它展示了通过对潜在空间（一个表示图像压缩表示的低维空间）中的数据进行编码并引入扩散过程，而不是直接操作图像，可以用更少的计算资源实现目标。

Sora将这一技术应用于视频数据，将视频的时空数据压缩到低维潜在空间，然后经过一系列的过程将其分解为时空块。这种在潜在空间中进行高效数据处理和生成的能力对于Sora能够更快速地生成更高质量的视觉内容起着至关重要的作用。

![](../Images/3f152224408eb5cdaf418cf2567a2045.png)

视觉编码图｜图片来源（OpenAI）

*关于潜在扩散模型的更多技术细节：*

[](/paper-explained-high-resolution-image-synthesis-with-latent-diffusion-models-f372f7636d42?source=post_page-----bd1ad17170df--------------------------------) [## 论文解读 — 基于潜在扩散模型的高分辨率图像合成

### 尽管 OpenAI 已凭借其生成文本模型主导了自然语言处理领域，但他们的图像……

towardsdatascience.com](/paper-explained-high-resolution-image-synthesis-with-latent-diffusion-models-f372f7636d42?source=post_page-----bd1ad17170df--------------------------------)

## 8. 扩散变压器（DiT）

> Peebles 和 Xie. (2023)，《具有变压器的可扩展扩散模型》。

这项研究可能是实现 Sora 的最关键研究。如 OpenAI 发布的技术报告中所提到，Sora 并不是采用普通的（标准）变压器，而是采用了扩散变压器（DiT）。

> 重要的是，Sora 是一种扩散 *变压器*。（来源：OpenAI Sora 技术报告）

该研究引入了一种新的模型，该模型用变压器结构替代了扩散模型中常用的 U-net 组件。通过对潜在图像块进行变压器操作，这种结构使潜在扩散模型成为可能。该方法使得图像块的处理更加高效，能够生成高质量的图像，同时有效利用计算资源。这一变压器的引入，与 Stability AI 在 2022 年发布的 Stable Diffusion 不同，被认为有助于实现更加自然的视频生成。

![](../Images/54dc92e3b369086a4a28bb343998d748.png)

图 1：扩散变压器生成的图像｜Peebles 和 Xie. (2023)

此外，值得注意的是，他们的验证结果证明了 DiT 的可扩展性，显著促进了 Sora 的实现。可扩展性意味着模型的性能会随着变压器深度/宽度的增加（使模型更复杂）或输入标记数量的增加而提升。

![](../Images/38de39d61eef4b8bfb6e62a3d800e8df.png)

图 8 和 9：扩散变压器的可扩展性｜Peebles 和 Xie. (2023)

+   Gflops（计算性能）：衡量计算机运算速度的单位，等于每秒十亿次浮点运算。在本文中，网络复杂度通过 Gflops 来衡量。

+   FID（Fréchet 生成距离）：图像生成的评价指标之一，值越低表示准确度越高。它通过测量生成图像和真实图像之间特征向量的距离，定量评估生成图像的质量。

这一点在自然语言处理领域已经被观察到，如 Kaplan 等人（2020）和 Brown 等人（2020）所确认，支持了 ChatGPT 创新成功背后的关键特征。

> Kaplan 等人（2020），《神经语言模型的扩展法则》。
> 
> Brown等人（2020年），《语言模型是少量学习者》

这一显著特点，除了由于Transformer的优势在较低计算成本下生成高质量图像之外，还表明，通过更大的计算资源，甚至可以生成更高质量的图像。Sora将这一技术应用于视频生成。

![](../Images/2d63e2c57fef03126f80016284ea38cb.png)

视频生成的可扩展性｜图像来源（OpenAI）

*关于DiT的更详细技术解释：*

通过YouTube观看DiT的论文评审

# 这些研究成果为Sora提供的能力

## **可变的持续时间、分辨率、纵横比**

主要得益于NaViT，Sora可以采样宽屏1920x1080p视频、垂直1080x1920视频以及介于两者之间的所有内容。这意味着它能够为各种设备类型在任何分辨率下创建视觉效果。

**使用图像和视频进行提示**

目前，Sora生成的视频，如演示所示，是以文本到视频的格式创建的，其中指令通过文本提示给出。然而，正如从之前的研究中可以轻易预见的那样，也可以使用现有的图像或视频作为输入，而不仅仅是文本。这使得图像的动画化成为可能，或者Sora可以想象并输出现有视频的过去或未来作为视觉效果。

**三维一致性**

尽管尚不清楚上述研究如何直接参与其中，但Sora能够生成带有动态摄像机运动的视频。当摄像机移动和旋转时，人物和场景元素会在三维空间中一致地移动。

# Sora的未来

在这篇博文中，我已经解释了OpenAI用于生成视频的AI技术——Sora，这项技术已经震惊了全世界。一旦它公开发布并能被更广泛的用户访问，它势必将在全球范围内产生更为深远的影响。

这一突破的影响预计将跨越视频创作的各个方面，但预测它可能从视频演变为三维建模的进一步发展。如果真是如此，不仅是视频创作者，连虚拟空间（如元宇宙）中的视觉内容生产也可能很快由AI轻松生成。

这样的未来的到来已经在以下内容中有所暗示：

Martin Nebelong通过X发布的关于Micael Rublof产品的帖子

目前，Sora被视为“仅仅”一个视频生成模型，但来自Nvidia的Jim Fan暗示，它可能是一个基于数据的物理引擎。这表明，AI通过大量的现实世界视频以及（尽管未明确提及）像虚幻引擎那样考虑物理行为的视频，可能理解物理定律和现象。如果是这样，未来不久，文本到三维的出现也是高度可能的。

Jim Fan通过X发布的引人注目的帖子

***非常感谢您阅读这篇文章。

你对这篇文章的点赞和对***[***我的新闻通讯***](https://rkiuchir.medium.com/subscribe) ***的订阅会给我带来很大的动力！***
