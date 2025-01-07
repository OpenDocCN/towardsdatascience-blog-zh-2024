# 利用人工智能革新烹饪体验：介绍FIRE（Food Image to REcipe生成）🔥

> 原文：[https://towardsdatascience.com/revolutionizing-culinary-experiences-with-ai-introducing-fire-food-image-to-recipe-generation-d7b010ee2eb2?source=collection_archive---------9-----------------------#2024-01-31](https://towardsdatascience.com/revolutionizing-culinary-experiences-with-ai-introducing-fire-food-image-to-recipe-generation-d7b010ee2eb2?source=collection_archive---------9-----------------------#2024-01-31)

## 从视觉享受到烹饪食谱：人工智能如何将食物图像转化为食谱？

[](https://medium.com/@prateekchhikara?source=post_page---byline--d7b010ee2eb2--------------------------------)[![Prateek Chhikara](../Images/4cabb40cbab34038c0f762b45d58bbba.png)](https://medium.com/@prateekchhikara?source=post_page---byline--d7b010ee2eb2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d7b010ee2eb2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d7b010ee2eb2--------------------------------) [Prateek Chhikara](https://medium.com/@prateekchhikara?source=post_page---byline--d7b010ee2eb2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d7b010ee2eb2--------------------------------) ·阅读时间11分钟·2024年1月31日

--

![](../Images/28ec61f09782221ff6bb62c3825991f5.png)

图1. 给定一张可能未见过的图像，我们的方法FIRE生成相应的食谱，包含标题、食材和烹饪说明。（图片来源：作者提供）

# 引言：

食物是人体所需的基本营养来源，也是我们文化身份的组成部分，反映了我们的生活方式、传统和社会关系[1]。一个人的外貌和认知能力通常能反映出其饮食习惯，因为选择营养丰富的食物有助于身心的整体健康[2]。社交媒体的迅速发展使每个人都能分享他们所食用美味食物的惊艳视觉。简单地搜索诸如#food或#foodie这样的标签，会显示出数百万的帖子，突显了食物在我们社会中的巨大价值[3]。食物的重要性，以及大量公开可得的食物数据集，推动了与食物相关的计算应用，尤其是将食物图像与符号知识联系起来。食物计算的一个雄心勃勃的目标是生成给定食物图像的食谱，包括根据用户偏好进行食物推荐、根据文化或宗教因素定制食谱，以及自动化烹饪操作以提高效率和精准度[4]。

> “告诉我你吃什么，我就能告诉你你是谁。”这句话强调了个人饮食选择反映其身份的观点。

在深入探讨我们提出的工作之前，我想提到这项工作已被发表，并可以在***IEEE/CVF计算机视觉应用冬季会议（WACV）- 2024***上找到，这是一个因其对计算机视觉的贡献和进展而备受认可的会议。✨✨✨

> **海报：** [https://drive.google.com/file/d/1zf2NA6ga8PWndZAgu5QjSwO8EPsvt-yt/view](https://drive.google.com/file/d/1zf2NA6ga8PWndZAgu5QjSwO8EPsvt-yt/view)
> 
> **论文：** [https://openaccess.thecvf.com/content/WACV2024/html/Chhikara_FIRE_Food_Image_to_REcipe_Generation_WACV_2024_paper.html](https://openaccess.thecvf.com/content/WACV2024/html/Chhikara_FIRE_Food_Image_to_REcipe_Generation_WACV_2024_paper.html)

# 这项工作的灵感来源：

我们创建一种从食物图像生成食谱的端到端方法的动机，源自计算机视觉（CV）社区对食物计算的兴趣。计算机视觉已被用于食物质量保证约三十年[5]。尽管深度学习技术在食物图像处理方面取得了进展，现有方法在从给定食物图像中提取成分方面的表现仍然有限[6, 7]。此外，从一组成分生成食谱可以看作是一个语言生成任务，或者更准确地说，是一个序列到序列（seq-to-seq）的应用场景，而这一点在现有文献中仍未被充分探讨。进一步来说，之前的方法未能彻底结合计算机视觉和自然语言处理（NLP）研究，设计出一个综合系统，将食物图像转化为完整的食谱。因此，目前的食物计算方法尚未利用NLP和计算机视觉中的最新突破，如视觉变换器和先进的语言建模技术。正是由于这种技术和应用上的空白，我们激发了开发端到端管道的动力，我们命名其为FIRE（🔥），旨在将这些技术结合起来，推动食物计算的边界。FIRE是一个多模态模型，旨在根据给定的食物图像生成完整的食谱，包括食物名称、成分和烹饪指令，如图1所示。

我们论文的贡献如下：

1.  我们的方法使用视觉变换器（ViT）从食物图像中提取详细的嵌入表示，然后作为基于注意力的解码器的输入，进一步识别食谱成分。

1.  我们开发了一个详细的设计，用于生成食谱标题和烹饪指令，采用最先进的视觉（BLIP）和语言（T5）模型。

1.  我们的多模态方法在成分提取准确性和生成的烹饪指令质量方面，超过了现有模型的表现。

1.  我们通过两个创新应用展示了FIRE的多功能性，（i）*食谱定制* 和 (ii) *食谱到代码生成*，展示了其在使用少量提示集成大型语言模型（LLM）方面的重要性。

# 揭开FIRE的面纱：分解过程

FIRE 包含三个组件：（1）使用最先进的图像描述技术从食品图像中生成**标题**，（2）使用视觉转换器和带有注意力的解码器层从图像中提取**成分**，（3）基于生成的标题和提取的成分使用编码-解码模型生成**烹饪说明**。有关所提出架构的更多细节，请参见图 2。

![](../Images/32c90148e7f474d6e6cd5fe4b0b3a8cc.png)

图 2. 提出架构，用于从食品图像中提取成分、生成菜谱标题和烹饪说明。（成分及其数量仅在训练时传递）（来源：作者提供的图像）

## **1. 标题生成**

使用 BLIP 模型，我们从食品图像中生成菜谱标题 [8]。在我们使用现成的 BLIP 模型进行的初步实验中，我们观察到 BLIP 的预测准确性较低，因为其训练数据与食品领域之间存在领域转移。由于 BLIP 最初设计用于为各种场景提供全面的图像描述，它倾向于捕捉与我们目标无关的额外细节。举个例子，当我们展示一张松饼的图像时，BLIP 生成了描述“*一块松饼放在木制切菜板上*”。为了更好地使生成的描述与菜谱标题对齐，我们使用 Recipe1M 数据集的一个子集对 BLIP 模型进行了微调。我们观察到，经过微调后的 BLIP 模型在生成准确、对齐且相关的食品图像标题方面显示出了良好的改进。对于相同的松饼图像，经过微调的 BLIP 模型提供了更简短的字符串“*松饼*”，去除了多余的信息。

## 2. 成分提取

从给定的食品图像中提取成分是一项具有挑战性的任务，因为食品组成的固有复杂性和变化性。我们开发了一个成分提取流程（如图 2 所示），该流程基于文献[7]中提出的方法。

**特征提取器：** 我们使用 ViT [9] 提取图像的特征。ViT 的注意力机制以稳定且显著高的分辨率处理特征表示。该能力精确地满足了密集预测任务的需求，例如从食品图像中提取成分。

**成分解码器：** 特征提取器生成图像嵌入。我们将这些图像嵌入通过三个归一化层（layerNorm），然后将输出传递给我们的成分解码器，负责提取成分。解码器由四个连续的模块组成，每个模块包含多个顺序层：自注意力、条件注意力、两个全连接层和三个归一化层。在最后一步，解码器的输出通过一个全连接层处理，该层的节点数等于词汇表的大小，从而生成预测的成分集合。

## 3. 烹饪说明生成

考虑到语言模型（LMs）在自然语言应用（如文本生成和问答）中的显著成就[10]，我们将烹饪指导生成视为一种语言建模任务。

![](../Images/b4d3515db1cb21ea3c9c5eb24b6ea211.png)

图3. 为标题和一组配料生成烹饪指导。（带数量的配料仅在T5的微调过程中出现）（来源：作者提供的图片）

对语言模型（LMs）进行下游任务微调在各种NLP任务中取得了显著成果。虽然我们预计大规模的语言模型经过微调后能够生成烹饪指导，但考虑到其庞大的参数量，它们需要大量的计算资源。基于可用资源和我们的研究目标，我们采用了流行的编码器-解码器模型T5[11]来生成烹饪指导。在微调过程中，我们将食谱的标题和配料作为格式化字符串传入（见图3），灵感来自于先前的研究[12]。

T5 在三种输入上进行微调：标题、配料和带数量的配料，以最大化地从数据集中获取信息。然而，在推理时我们没有带数量的配料；因此，我们只传递标题和配料。此外，排除数量信息确保了与先前方法的公平比较。我们调查了我们的模型优势是否源于良好的结构化架构，而非仅依赖于额外知识的增强。通过去除推理过程中数量信息的影响，我们旨在突出T5的固有能力及其生成高质量烹饪指导的能力。

# FIRE 结果：

表1中的结果显示，FIRE表现优于SotA基线模型InverseCooking[7]和Chef Transformer[13]。这些结果证明了我们提出的流程在生成精确且连贯的食谱方面的能力，验证了FIRE的有效性，并强调了语言生成模型在高质量食谱生成中的价值。这些结果也支持我们的预期，即FIRE方法即使在推理时没有提供配料数量信息，仍然能很好地泛化，即使在训练过程中提供了这些信息。同时，使用额外信息进行训练能减少幻觉现象，特别是关于配料数量（例如2汤匙盐）和烹饪时间（例如加热10-12分钟）。

![](../Images/eb3500ec8e3e77538caf3a9d4332d770.png)

表1. 测试数据集上的食谱生成比较。我们报告了10次实验的均值和标准差。粗体表示最佳模型。(+)表示在真实标题和配料上测试模型以生成食谱。（来源：作者提供的图片）

在本文中，我们主要关注我们提出的工作中的关键方面，考虑到文章篇幅的限制。我们鼓励读者查看我们的详细论文，以便深入了解我们的实验结果和全面分析。😁

## 错误分析

为了进一步了解我们的食谱生成方法的表现，我们检查了其在个别图片上的表现。FIRE 通常能够为与 Recipe1M 数据集中存在的菜肴相似的菜肴生成正确的食谱。对于 Pav Bhaji（一道在 Recipe1M 数据集中没有的受欢迎的印度菜肴），它给出的结果与预期的菜肴无关，如图 4 所示。因此，我们想强调开发更好的评估指标的重要性，因为传统的评估指标，如 SacreBLEU 和 ROUGE，未能捕捉生成的食谱的准确性并检测到特定的文本幻觉。

![](../Images/efe451638ebff8231061c973651d9421.png)

图 4. FIRE 对 Pav Bhaji 图片的食谱预测。（来源：作者提供的图片）

# 超越厨房：FIRE 在食品计算中的未来：

虽然 FIRE 在从图片生成食谱这一具有挑战性的任务上达到了最先进的表现，我们更进一步，探索其在食品计算应用中集成到更大管道中的可能性。具体来说，考虑到大规模语言模型的少量提示能力，我们描述了 FIRE 和大型语言模型如何集成，以支持***食谱定制***和***食谱到机器代码生成***。

![](../Images/085bb19ddc9aebf21cb6f74efd267815.png)

图 5. FIRE 的应用：食谱定制与食谱到代码生成。（来源：作者提供的图片）

## 1. 食谱定制

食谱定制至关重要，因为它涉及到食物、习惯和个人偏好。此外，当涉及过敏或饮食限制时，食谱定制变得尤为重要。令人惊讶的是，尽管有明显需求，现有文献在食谱定制方面缺乏专门的研究。我们的工作旨在填补这一研究空白，通过考虑个人口味档案和饮食限制来实现个性化食谱定制。

为了指导未来在这一领域的研究，我们展示了 FIRE 支持一种食谱定制方法的能力，重点关注广泛的主题（例如，食材替换、口味调整、卡路里调整、烹饪时间适配），以全面测试少量样本的表现。如图 5 中紫色部分所示，我们从食谱中删除了土豆成分。删除了与土豆相关的两句话，并对其中一句进行了修改以确保一致性。具体来说，我们进行了食材添加，将‘*奶酪*’替换为‘*切达奶酪*’，并识别到应在烘烤前加入，从而得到了修改后的句子‘*撒上半量的切达奶酪和洋葱*’。

## 2. 为基于图片的食谱生成机器代码

将食谱转换为机器代码能够实现自动化、可扩展性，并与各种现有系统进行集成，从而减少人工干预、节省劳动力成本并减少准备食物时的人为错误。为了简化这一任务，我们将FIRE的食谱生成能力与大型语言模型（LM）在结构任务中操控代码风格提示的能力结合起来[14]。我们展示了一个示例方法，用于通过提示GPT-3生成由FIRE开发的食谱的Python风格代码表示（请参阅图5中的橙色部分）。

# 结论与未来工作：

我们介绍了FIRE，一种专为食品计算量身定制的方法，专注于仅通过图像输入生成食物标题、提取食材和生成烹饪指令。我们利用了近期计算机视觉（CV）和语言建模的进展，取得了优于稳固基准的卓越表现。此外，我们展示了FIRE在*食谱定制*和*食谱到代码生成*中的实际应用，展示了我们方法的适应性和自动化潜力。

我们列出了未来研究中应解决的三大挑战：

1.  现有的食谱生成模型和我们提出的模型缺乏一种可靠的机制来验证生成食谱的准确性。传统的评估指标在这方面存在不足。因此，我们希望创建一种新的评估标准，评估食谱的连贯性和可信度，提供更为全面的评价。

1.  食谱的多样性和可获得性受到地理、气候和宗教因素的影响，这些因素可能会限制其适用性。结合考虑这些背景因素和食材关系的知识图谱，可以提供替代食材建议，从而解决这一问题。

1.  使用语言和视觉模型生成食谱时的幻觉问题构成了一个重大挑战。未来的研究将探讨状态追踪方法，以改进生成过程，确保生成更为真实和准确的食谱。

# 行动号召：

希望这个概述能为您提供有关FIRE的灵感和发展的见解，这是一款将食物图像转化为详细食谱的创新工具。若想更深入地了解我们的方法，欢迎查阅我们完整的论文，该论文将在*IEEE/CVF冬季计算机视觉应用会议（WACV）-2024*上发表。如果我们的研究对您的工作有所帮助，欢迎引用我们的论文。😊

> **论文链接：** [https://openaccess.thecvf.com/content/WACV2024/html/Chhikara_FIRE_Food_Image_to_REcipe_Generation_WACV_2024_paper.html](https://openaccess.thecvf.com/content/WACV2024/html/Chhikara_FIRE_Food_Image_to_REcipe_Generation_WACV_2024_paper.html)

```py
@InProceedings{Chhikara_2024_WACV,
    author    = {Chhikara, Prateek and Chaurasia, Dhiraj and Jiang, Yifan and Masur, Omkar and Ilievski, Filip},
    title     = {FIRE: Food Image to REcipe Generation},
    booktitle = {Proceedings of the IEEE/CVF Winter Conference on Applications of Computer Vision (WACV)},
    month     = {January},
    year      = {2024},
    pages     = {8184-8194}
}
```

# 参考文献：

[1] Weiqing Min, Shuqiang Jiang, Linhu Liu, Yong Rui, 和 Ramesh Jain. 食品计算的综述. ACM Comput. Surv., 52(5), 2019年9月。

[2] Sutter Health. 为心理健康吃得好. [https://www.sutterhealth.org/health/nutrition/eating-wellfor-mental-health.](https://www.sutterhealth.org/health/nutrition/eating-wellfor-mental-health.) 访问日期：2023年3月24日。

[3] Kiely Kuligowski. 使用Instagram为您的业务的12个理由. [https://www.business.com/articles/10-reasons-touse-instagram-for-business/.](https://www.business.com/articles/10-reasons-touse-instagram-for-business/.) 访问日期：2023年5月12日。

[4] Dim P. Papadopoulos、Enrique Mora、Nadiia Chepurko、Kuan Wei Huang、Ferda Ofli 和 Antonio Torralba. 食品图像和烹饪食谱的程序表示学习，2022年。

[5] Sundaram Gunasekaran. 食品质量保证的计算机视觉技术. 《食品科学与技术趋势》，7(8)：245–256，1996年。

[6] Yoshiyuki Kawano 和 Keiji Yanai. 基于深度卷积特征的食品图像识别. 见于《2014年ACM国际联合会议论文集：普适计算与无处不在计算：附录出版物》，第589–593页，2014年。

[7] Amaia Salvador、Michal Drozdzal、Xavier Giro-i Nieto 和 Adriana Romero. 逆向烹饪：从食品图像生成食谱. 见于《IEEE/CVF计算机视觉与模式识别会议论文集》，第10453–10462页，2019年。

[8] Junnan Li、Dongxu Li、Caiming Xiong 和 Steven Hoi. Blip：用于统一视觉语言理解与生成的语言图像预训练引导. 见于《国际机器学习会议》，第12888–12900页。PMLR，2022年。

[9] Alexey Dosovitskiy、Lucas Beyer、Alexander Kolesnikov、Dirk Weissenborn、Xiaohua Zhai、Thomas Unterthiner、Mostafa Dehghani、Matthias Minderer、Georg Heigold、Sylvain Gelly 等. 一张图像相当于16x16个词：大规模图像识别的变换器. arXiv预印本 arXiv:2010.11929，2020年。

[10] Prateek Chhikara、Ujjwal Pasupulety、John Marshall、Dhiraj Chaurasia 和 Shweta Kumari. 面向在线心理健康风险评估的隐私意识问答系统. 见于《第22届生物医学自然语言处理与BioNLP共享任务研讨会论文集》，第215–222页，加拿大多伦多，2023年7月。计算语言学协会。

[11] Colin Raffel、Noam Shazeer、Adam Roberts、Katherine Lee、Sharan Narang、Michael Matena、Yanqi Zhou、Wei Li 和 Peter J Liu. 探索统一的文本到文本变换器在迁移学习中的极限. 《机器学习研究期刊》，21(1)：5485–5551，2020年。

[12] Chunting Zhou、Graham Neubig、Jiatao Gu、Mona Diab、Francisco Guzman、Luke Zettlemoyer 和 Marjan Ghazvininejad. 在条件神经序列生成中检测幻觉内容. 见于《计算语言学协会发现：ACL-IJCNLP 2021》，第1393–1404页，2021年。

[13] Mehrdad Farahani、Kartik Godawat、Haswanth Aekula、Deepak Pandian 和 Nicholas Broad. Chef Transformer. [https://huggingface.co/flax-community/t5-](https://huggingface.co/flax-community/t5-) 食谱生成。访问时间：2023年4月12日。

[14] Aman Madaan, Shuyan Zhou, Uri Alon, Yiming Yang, 和 Graham Neubig. 代码语言模型是少量示例的常识学习者。载于《计算语言学协会发现：EMNLP 2022》，2022年。
