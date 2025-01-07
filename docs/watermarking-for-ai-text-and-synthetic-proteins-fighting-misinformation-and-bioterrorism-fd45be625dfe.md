# 人工智能文本和合成蛋白质的水印

> 原文：[https://towardsdatascience.com/watermarking-for-ai-text-and-synthetic-proteins-fighting-misinformation-and-bioterrorism-fd45be625dfe?source=collection_archive---------9-----------------------#2024-11-07](https://towardsdatascience.com/watermarking-for-ai-text-and-synthetic-proteins-fighting-misinformation-and-bioterrorism-fd45be625dfe?source=collection_archive---------9-----------------------#2024-11-07)

![](../Images/285ec832e68ca552b538789de92bbab8.png)

彩色病毒突变图像，作者Nataliya Smirnova，来源：[UnSplash](https://unsplash.com/photos/a-group-of-colorful-butterflies-de3yM8H00vo)

## 理解生物学中人工智能应用对机器学习工程师的意义

[](https://medium.com/@meghanheintz?source=post_page---byline--fd45be625dfe--------------------------------)[![Meghan Heintz](../Images/9eaae6d3d8168086d83ff7100329c51f.png)](https://medium.com/@meghanheintz?source=post_page---byline--fd45be625dfe--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fd45be625dfe--------------------------------)[![数据科学前沿](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fd45be625dfe--------------------------------) [Meghan Heintz](https://medium.com/@meghanheintz?source=post_page---byline--fd45be625dfe--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--fd45be625dfe--------------------------------) ·阅读时间：7分钟·2024年11月7日

--

虚假信息和生物恐怖主义并非新兴威胁，但它们能够被迅速释放的规模和便利性大大增加。大型语言模型（LLMs）使得制造[自主聊天机器人](https://open.clemson.edu/mfh_reports/7/)来散播纷争变得轻而易举，而生成性蛋白质设计模型则极大地扩展了能够从事[生物战争](https://press.asimov.com/articles/dna-screening)的行为体的数量。我们作为社会所需要的工具多种多样，但其中一个重要组成部分将是我们发现这些威胁存在的能力。这就是*水印*的作用所在。

水印或数字水印，与那个会挟持你孩子学校照片的物理水印不同，是一种用于标识所有权的秘密信号。有效的水印必须具有鲁棒性，能够在不被专门方法探测的情况下承受修改。它们在各个创意领域得到广泛应用，从保护版权的数字图像和视频到确保文件的完整性。如果我们能为生成性人工智能（GenAI）开发出有效的水印技术，我们就能在应对虚假信息和生物恐怖主义的斗争中获得一项强有力的工具。

在我们的[系列](https://medium.com/@meghanheintz/list/understanding-ai-applications-in-bio-for-ml-engineers-7b9e9551bb7f)中，我们探讨了其他生成性文本和生物学突破如何依赖相关的架构突破，而当前的水印提案也不例外。谷歌在2024年10月宣布了[SynthID-Text](https://www.nature.com/articles/s41586-024-08025-4)，这是一种已准备投入生产的文本水印方案，作为Gemini的一部分部署。它们的方法通过应用一个秘密的随机函数来修改最终的采样过程或推理，[马里兰大学帕克分校团队](https://www.biorxiv.org/content/10.1101/2024.05.02.591928v1.full.pdf)的生成蛋白质设计水印提案也采用了类似的方法。

![](../Images/30872b035f04d87a16ec188a40299e80.png)![](../Images/9e464a2dae90abb6d5560c8c114a2a93.png)

（左）一幅来自罗马帝国的早期虚假信息示例，[《亚克提乌姆战役》由洛伦佐·A·卡斯特罗](https://simple.wikipedia.org/wiki/Battle_of_Actium#/media/File:Castro_Battle_of_Actium.jpg)绘制，这幅画作为凯撒死后宣传运动的一部分。 （右）本作者记得的第一个生物安全事件，[2001年炭疽邮件袭击](https://en.wikipedia.org/wiki/2001_anthrax_attacks)。巧合的是，这也是一个虚假信息的例子，因为犯罪者试图将责任嫁祸给穆斯林。

# 水印技术中的关键理想特性

**鲁棒性** — 它应该能够承受水印文本/结构的扰动。

如果最终用户在发布之前可以简单地替换几个词，或者蛋白质可以发生突变并变得无法检测到，那么水印就不足够。

**可检测性** — 它应该能够通过特殊方法可靠检测，但不能通过其他方法检测。

对于文本，如果水印可以在没有秘密密钥的情况下被检测到，通常意味着文本已经被严重扭曲，读者会觉得它很奇怪。对于蛋白质设计，如果可以裸露地检测到水印，可能会导致设计质量的下降。

# 文本水印和Synthtext-ID

让我们**深入**探讨这个话题。如果你像我一样，花太多时间在[Twitter](https://x.com/JeremyNguyenPhD/status/1774021645709295840)上，你可能已经意识到，很多人发现ChatGPT过度使用某些词汇。其中一个就是“delve”（深入），其过度使用正在被用来分析[学术文章](https://www.medrxiv.org/content/10.1101/2024.05.14.24307373v2.full-text)是否由ChatGPT撰写或在其帮助下完成。这本身就是一种“脆弱”的水印技术，因为它可以帮助我们识别由大型语言模型（LLM）撰写的文本。然而，随着这一点成为常识，查找和替换“delve”实例变得太容易了。但SynthText-ID背后的理念是可以的，我们可以通过选词的概率来区分AI和人类写作的文本。

![](../Images/2e0e368c6108ad47a07caea5cc20fbd6.png)

[深入探讨PubMed记录：随着ChatGPT的到来，一些医学写作中的术语发生了巨大变化](https://www.medrxiv.org/content/10.1101/2024.05.14.24307373v2.full-text)

SynthText-ID使用“比赛抽样”根据随机水印函数修改令牌被选中的概率。这是一种高效的水印方法，因为它可以在推理过程中完成，而不需要更改训练过程。这种方法改进了[吉布尔抽样](/what-is-gumbel-softmax-7f6d9cdcb90e)，该方法在抽样步骤之前对LLM的概率分布进行随机扰动。

在论文的示例中，序列“my favorite tropical fruit is”可以通过候选令牌中的任何一个（如芒果、榴莲、荔枝等）来圆满完成。这些候选者是从LLM的概率分布中抽样的，条件是前面的文本。构建括号后，选取的令牌将在一个水印函数的基础上进行评分，该函数基于上下文窗口和水印密钥。这个过程将统计签名引入生成的文本中，稍后可进行测量。

![](../Images/046beaccc13fe2d19af424f81d9d2165.png)

来自《可扩展水印技术用于识别大型语言模型输出》的比赛抽样示例，演示了比赛抽样技术。[Dathathri, S., See, A., Ghaisas, S., Huang, P., McAdam, R., Welbl, J., … & Kohli, P. (2024). 可扩展水印技术用于识别大型语言模型输出. *Nature, 631*(7624), 755–759](https://www.nature.com/articles/s41586-024-08025-4#MOESM1)

为了检测水印，每个令牌都会用水印函数进行评分，平均得分越高，文本来自LLM的可能性就越大。应用一个简单的阈值来预测文本的来源。

该签名的强度由几个因素控制：

+   比赛的轮数（m）（通常为m=30），每一轮都会加强签名（并且也减少得分方差）。

+   LLM的熵。低熵模型无法提供足够的随机性，导致比赛无法选择得分较高的候选者。*FWIW，这对作者来说似乎是个大问题，因为他从未使用过除temperature=0以外的任何设置。*

+   文本的长度；较长的序列包含更多的证据，因此统计上的确定性更高。

+   是否使用非扭曲和扭曲配置。

扭曲是指在保持文本质量与检测之间所做的权衡。非扭曲配置优先考虑文本质量，以换取可检测性的牺牲。扭曲配置则相反，使用比每轮比赛更多的令牌，从而允许更多的自由度选择得分最高的令牌。谷歌表示，他们将在Gemini中实现一个非扭曲版本的该算法。

非扭曲版本在400个标记序列的情况下，达到了接近90%的TPR（真正阳性率），假阳性率为1%，这大约是1-2段文字。一个（非付费）推文或X帖子限制为280个字符或大约70-100个标记。该长度下的TPR只有大约50%，这引发了人们对这种方法在实际应用中效果的质疑。*也许它对于抓捕懒惰的大学生有用，但在选举期间对于外国势力就不一定有效了？*

![](../Images/974488bdfd75a3bcd670c133215adc62.png)

水印可检测性是通过在假阳性率（FPR）设置为1%时，使用真正阳性率（TPR）来衡量的，适用于SynthText-ID和Gumble采样。[Dathathri, S., See, A., Ghaisas, S., Huang, P., McAdam, R., Welbl, J., … & Kohli, P. (2024). 可扩展水印技术用于识别大型语言模型输出。*Nature, 631*(7624), 755–759](https://www.nature.com/articles/s41586-024-08025-4#MOESM1)

# 生成性蛋白质水印与生物安全性

生物安全性是一个你可能在新冠疫情后开始频繁听到的词汇。我们可能永远无法确切知道病毒是来自湿市场还是实验室泄漏。然而，借助更好的水印工具和生物安全实践，我们可能能够追溯到下一个潜在大流行的具体研究人员。为此目的已有现存的数据库日志记录方法，但希望通过生成性蛋白质水印技术，能够追踪到即使是新的或修改过的序列，这些序列可能与现有的危险特征不匹配，同时水印也能够更强大地抵抗突变。这还将带来提升研究人员隐私保护和简化知识产权过程的好处。

当文本被水印过程扭曲时，可能会让读者感到困惑或听起来很奇怪。更严重的是，生成性蛋白质设计中的扭曲可能会使蛋白质变得完全没有价值或功能上发生改变。为了避免扭曲，水印必须不会改变设计蛋白质的总体统计属性。

水印过程与SynthText-ID相似。它不是修改标记的概率分布，而是调整氨基酸残基的概率分布。这个过程是通过一个无偏重的重加权函数（Gumble采样，而不是锦标赛采样）实现的，该函数根据研究人员的私钥派生的水印码，将原始残基的概率分布进行转换。Gumble采样被认为是无偏的，因为它专门设计用于以一种保持原始分布统计属性而不引入系统性错误的方式，近似地求得一组值的最大值；或者平均而言，引入的噪声会相互抵消。

![](../Images/14be44d35960ebbef7bc29fed5d25f2a.png)

(a) 现有的生物安全流程，IGSC记录所有发送进行DNA合成的序列。(b) 提议的水印过程，通过Gumble采样添加无偏噪声。(c) 研究人员如何使用他们的私钥给蛋白质添加水印，并帮助IGSC识别可疑蛋白质。[Chen, Y., Hu, Z., Wu, Y., Chen, R., Jin, Y., Chen, W., & Huang, H. (2024). 增强生物安全性与水印蛋白质设计. *bioRxiv*.](https://www.biorxiv.org/content/10.1101/2024.05.02.591928v1.full.pdf)

研究人员通过使用[ProteinMPNN](https://www.science.org/doi/10.1126/science.add2187)，一个基于深度学习的蛋白质序列设计模型，验证了重加权函数在实验中的无偏性。随后，使用[ESMFold](https://github.com/facebookresearch/esm)（进化尺度建模）在水印前后预测pLDDT或预测的局部距离差异测试。结果表明，性能没有变化。

![](../Images/8011317d932da19c3bacfbe92bed70fb.png)

蓝色和红色分别代表原始的ProteinMPNN和带水印的ProteinMPNN。每个小提琴图包含来自60个不同单体设计任务的3000个pLDDT分数，每个任务有50个不同的设计，结果显示没有变化。[Chen, Y., Hu, Z., Wu, Y., Chen, R., Jin, Y., Chen, W., & Huang, H. (2024). 增强生物安全性与水印蛋白质设计. *bioRxiv*.](https://www.biorxiv.org/content/10.1101/2024.05.02.591928v1.full.pdf)

类似于低温LLM设置下的检测，当可能的高质量设计数量较少时，检测变得更加困难。结果产生的低熵使得在不引入显著变化的情况下嵌入可检测水印变得困难。然而，这一限制可能比LLM的类似限制轻微。低熵设计任务可能只有少数几种蛋白质符合要求，这使得使用现有数据库方法追踪它们变得更容易。

# 主要结论

+   LLM和蛋白质设计的水印方法正在改进，但仍然需要进一步提高！（不能仅依赖它们来检测机器人军团！）

+   两种方法都专注于修改采样过程；这非常重要，因为它意味着我们不需要编辑训练过程，且它们的应用计算效率较高。

+   温度和文本长度是影响水印可检测性的两个重要因素。当前的方法（SynthText-ID）在1%假阳性率下，对于1至2段长度的序列，召回率（TPR）大约为90%。

+   一些蛋白质具有有限的可能结构，因此这些蛋白质更难以加水印。然而，现有方法应该能够使用数据库检测这些序列。
