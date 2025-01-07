# Open Food Facts如何利用开源LLM修复OCR提取的成分？

> 原文：[https://towardsdatascience.com/how-did-open-food-facts-use-open-source-llms-to-enhance-ingredients-extraction-d74dfe02e0e4?source=collection_archive---------4-----------------------#2024-10-06](https://towardsdatascience.com/how-did-open-food-facts-use-open-source-llms-to-enhance-ingredients-extraction-d74dfe02e0e4?source=collection_archive---------4-----------------------#2024-10-06)

## 深入了解一个端到端的机器学习项目，旨在提升Open Food Facts数据库的质量。

[](https://medium.com/@jeremyarancio?source=post_page---byline--d74dfe02e0e4--------------------------------)[![Jeremy Arancio](../Images/37c4c41e71eb91cfffc7e4ff2bb4394a.png)](https://medium.com/@jeremyarancio?source=post_page---byline--d74dfe02e0e4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d74dfe02e0e4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d74dfe02e0e4--------------------------------) [Jeremy Arancio](https://medium.com/@jeremyarancio?source=post_page---byline--d74dfe02e0e4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d74dfe02e0e4--------------------------------) ·阅读时间：13分钟·2024年10月6日

--

![](../Images/36288a16e720c9435e278bbc7b280c5a.png)

使用Flux1生成的图像

Open Food Facts的目标是创建全球最大的**开源食品数据库**。至今，它已经收录了超过300万种产品及其相关信息，得益于社区贡献者的帮助。

营养价值、生态评分、产品来源……各种定义每个产品的数据，为消费者和研究人员提供关于他们所放置在餐盘上的食品的深刻见解。

这些信息由用户和贡献者社区提供，他们通过[移动应用](https://play.google.com/store/apps/details?id=org.openfoodfacts.scanner&hl=en_US&pli=1)积极添加产品数据、拍照，并填写数据库中任何缺失的数据。

使用产品图片，Open Food Facts通过光学字符识别（OCR）技术提取通常位于包装背面的成分列表。然后对产品成分进行解析并将其添加到数据库中。

![](../Images/7c922462d16ef15165355663e856bf04.png)

产品包装上的成分列表

然而，文本提取过程往往并不顺利……

```py
Ingrédients: Jambon do porc, sel, dextrose, arôme naturels, antioxydant: E316, conservateur: E250
                     ^
```

这些拼写错误看起来可能微不足道，但当成分列表被解析以提取单个成分时，**此类错误会导致无法识别的成分**，从而影响数据库的质量。光线反射、包装折叠、低质量图片等因素都使得成分解析过程变得更加复杂。

![](../Images/dd0d3821089e8b68007112d12a755161.png)![](../Images/6074f6e9c6d559cc94eab9cbd049ab7f.png)

OCR失败的包装图片示例（来自Open Food Facts数据库）

**Open Food Facts多年来一直尝试使用正则表达式和现有解决方案（如Elasticsearch的校正器）来解决这个问题，但未成功。直到最近。**

感谢人工智能的最新进展，我们现在可以使用强大的**大语言模型**（Large Language Models），也称为**LLMs**。

通过训练我们自己的模型，我们创建了**成分拼写检查**，不仅在这个任务上超越了如**GPT-4o**或**Claude 3.5 Sonnet**等专有LLM，而且还将数据库中未识别的成分数量减少了**11%**。

**本文将带您了解项目的不同阶段，并展示我们如何利用机器学习提高数据库质量。**

享受阅读！

# 定义问题

当一个产品由贡献者添加时，它的图片会经过一系列过程以提取所有相关信息。其中一个关键步骤是提取**成分列表**。

当一个词被识别为成分时，它会与包含预定义成分列表的**分类法**进行交叉引用。如果该词与分类法中的某个条目匹配，它就会被标记为成分并添加到产品信息中。

这个标签化过程确保了成分的标准化，且易于搜索，为消费者和分析工具提供准确的数据。

**但是，如果某个成分未被识别，过程就会失败。**

![](../Images/f91a23509ebc0f515c471094f2bf15bb.png)

成分“Jambon do porc”（猪肉火腿）未被解析器识别（来自产品编辑页面）

因此，我们在过程中过引入了一个额外的层次：**成分拼写检查**，旨在在成分解析器处理之前修正成分列表。

一种更简单的方法是[Peter Norvig算法](https://norvig.com/spell-correct.html)，它通过应用一系列字符删除、添加和替换操作来处理每个单词，从而识别潜在的拼写纠正。

然而，由于几个原因，这种方法证明对我们的用例不够充分：

+   **特殊字符和格式**：诸如逗号、括号和百分号等元素在成分列表中具有重要作用，影响产品组成和过敏原标签*(例如，“盐（1.2%）”)。*

+   **多语言挑战**：数据库包含来自世界各地的产品，语言种类繁多。这进一步使得像诺尔维格的字符基础方法那样的语言无关的方法变得复杂。

相反，我们转向了机器学习的最新进展，特别是**大语言模型（LLMs）**，它们在包括拼写纠正在内的各种**自然语言处理（NLP）**任务中表现优异。

这是我们决定采取的路径。

# 评估

> 你无法改善你无法衡量的东西。

**什么是好的更正？如何衡量更正者的性能，LLM 或非 LLM？**

我们的第一步是理解并分类成分解析器遇到的各种错误。

此外，评估错误是否应该被更正也是至关重要的。有时，试图更正错误可能会适得其反：

```py
flour, salt (1!2%)
# Is it 1.2% or 12%?...
```

基于这些原因，我们创建了**拼写检查指南**，这是一套限制更正范围的规则。*这些指南将在项目的各个阶段为我们提供帮助，从数据集生成到模型评估。*

该指南特别用于创建[**拼写检查基准**](https://huggingface.co/datasets/openfoodfacts/spellcheck-benchmark)，这是一个经过精心策划的数据集，包含大约300个手动更正的成分列表。

这个基准是**项目的基石**。它使我们能够在我们的用例中评估任何解决方案，无论是机器学习还是简单的启发式方法。

它与**评估算法**一起使用，这是我们开发的自定义解决方案，能够将一组更正转化为可度量的指标。

## 评估算法

大多数现有的文本相关任务评估算法通过计算参考与预测之间的相似性来评估性能，例如用于语言翻译或总结的[BLEU](https://en.wikipedia.org/wiki/BLEU)或[ROUGE](https://en.wikipedia.org/wiki/ROUGE_(metric))分数。

然而，在我们的案例中，这些指标并不完全适用。

我们希望评估拼写检查算法如何识别和修正成分列表中的正确单词。因此，我们将**精确度**和**召回率**指标适配到我们的任务中：

> **精确度** = 模型正确更正的单词数 / 模型所做的总更正数
> 
> **召回率** = 模型正确更正的单词数 / 错误总数

然而，我们没有细粒度的视角来查看哪些单词应该被更正……我们只能访问：

+   **原始数据：数据库中现有的成分列表；**

+   **参考数据：我们期望这个列表被更正的方式；**

+   **预测：模型的更正。**

> 是否有方法计算被正确更正的错误数、拼写检查未能更正的错误，以及最终被错误更正的错误数？

答案是肯定的！

```py
Original:       "Th cat si on the fride,"
Reference:      "The cat is on the fridge."
Prediction:     "Th big cat is in the fridge."
```

根据上述示例，我们可以轻松地找出应该被更正的单词：`The`、`is` 和 `fridge`；以及哪些单词被错误更正：`on` 被更正为 `in`。最后，我们看到添加了一个额外的单词：`big`。

如果我们将这三组序列配对对齐：`original-reference` 和 `original-prediction`，我们可以检测出哪些单词应该被更正，哪些没有被更正。这种对齐问题在生物信息学中是典型的，称为[序列对齐](https://en.wikipedia.org/wiki/Sequence_alignment)，其目的是识别相似性区域。

这是我们拼写检查评估任务的完美类比。

```py
Original:       "Th    -   cat   si   on   the   fride,"
Reference:      "The   -   cat   is   on   the   fridge."
                  1    0    0    1    0    0     1

Original:       "Th    -   cat   si   on   the   fride,"
Prediction:     "Th   big  cat   is   in   the   fridge."
                  0    1    0    1    1    0     1
                FN    FP         TP   FP         TP
```

通过为每一对标注`0`或`1`，表示单词是否发生了变化，我们可以计算模型正确修正错误的次数**（真正例 — TP）**、错误改变了正确单词的次数**（假正例 — FP）**，以及漏掉应该修正的错误的次数**（假负例 — FN）**。

换句话说，我们可以计算拼写检查的**精确度**和**召回率**！

我们现在拥有一个强大的算法，能够评估任何拼写检查解决方案！

你可以在[项目仓库](https://github.com/openfoodfacts/openfoodfacts-ai/blob/develop/spellcheck/src/spellcheck/evaluation/evaluator.py)中找到该算法。

# 大型语言模型

大型语言模型（LLMs）已被证明在各个行业中处理自然语言任务时提供了极大的帮助。

它们构成了我们必须为使用案例探索的路径。

> 许多LLM供应商在排行榜上炫耀他们模型的表现，但它们在修正配料清单中的错误表现如何呢？因此，我们对它们进行了评估！

我们使用我们的自定义基准测试和评估算法，评估了**OpenAI**的**GPT-3.5**和**GPT-4o**、**Anthropic**的**Claude-Sonnet-3.5**以及**Google**的**Gemini-1.5-Flash**。

我们提供了详细的指令，以便将修正方向定向到我们的自定义指南。

![](../Images/83eadd9bceeee0e567b618410e3800c1.png)

LLM在我们的基准测试上的评估（图来自作者）

**GPT-3.5-Turbo**在指标和人工评审方面都提供了最好的性能，优于其他模型。特别提到的是**Claude-Sonnet-3.5**，它在修正错误方面表现出色（高召回率），但常常提供额外的无关解释，降低了其精确度。

太好了！我们有了一个有效的LLM！是时候在应用程序中创建这个功能了！

***好吧，*** 别急…

使用私有LLM会带来许多挑战：

1.  **缺乏所有权**：我们变得依赖于供应商及其模型。新版本的模型频繁发布，改变了模型的行为。这种不稳定性，主要是因为该模型设计是为了通用目的，而非针对我们特定的任务，从而使长期维护变得复杂。

1.  **模型删除风险**：我们没有防范措施来应对供应商删除旧模型的情况。例如，尽管GPT-3.5是完成此任务的最佳模型，但它正逐渐被更高效的模型替代！

1.  **性能限制**：私有LLM的性能受限于其提示。换句话说，我们改进输出的唯一方式是通过更好的提示，因为我们无法通过在我们自己的数据上训练来修改模型的核心权重。

***基于这些原因，我们选择将精力集中在开源解决方案上，这将使我们能够完全控制并超越通用LLM。***

# 训练我们自己的模型

![](../Images/bb3dbfab4d007c69b8ac90063f64f12f.png)

模型训练工作流程：从数据集提取到模型训练（图来自作者）

任何机器学习解决方案都始于数据。在我们的案例中，数据就是经过修正的配料列表。

然而，并非所有的配料列表都是一样的。有些列表没有未识别的配料，而有些则难以阅读，甚至没有必要纠正它们。

因此，我们通过选择包含**10%到40%未识别配料**的配料列表找到了一个完美的平衡。我们还确保数据集内没有重复项，并且与基准数据也没有重复，以防在评估阶段出现数据泄漏。

我们使用[DuckDB](https://duckdb.org/)从Open Food Facts数据库中提取了6000个未修正的列表，DuckDB是一个快速的进程内SQL工具，能够在一秒钟内处理数百万行数据。

然而，这些提取的列表还没有被纠正，手动标注它们将需要大量时间和资源……

**然而，我们可以访问已经在相同任务上进行评估的LLMs。因此，我们提示GPT-3.5-Turbo，这是我们基准上的最佳模型，按照我们的指南修正每一个列表。**

该过程用了不到一个小时，费用几乎为**2$**。

然后，我们使用[Argilla](https://argilla.io/)手动审查数据集，Argilla是一个开源标注工具，专门用于自然语言处理任务。这个过程确保数据集质量足够高，以训练出可靠的模型。

**现在我们手头有一个** [**训练数据集**](https://huggingface.co/datasets/openfoodfacts/spellcheck-dataset) **和一个** [**评估基准**](https://huggingface.co/datasets/openfoodfacts/spellcheck-benchmark) **，可以用来训练我们自己的拼写检查模型。**

## 训练

对于这一阶段，我们决定使用**序列到序列语言模型**。换句话说，这些模型以文本作为输入，并返回文本作为输出，适合拼写检查的过程。

有几种模型适合这个角色，例如**T5系列**，由谷歌于2020年开发，或当前的开源LLMs，如**Llama**和**Mistral**，它们专为文本生成和执行指令而设计。

模型训练包括一系列步骤，每个步骤都需要不同的资源分配，例如云GPU、数据验证和日志记录。出于这个原因，我们决定使用[Metaflow](https://metaflow.org/)来协调训练，它是一个专为数据科学和机器学习项目设计的管道编排工具。

训练管道的组成如下：

+   配置和超参数是从配置的 YAML 文件导入到管道中的；

+   训练任务通过[AWS Sagemaker](https://aws.amazon.com/sagemaker/)在云端启动，使用一组模型超参数和自定义模块（如评估算法）。一旦任务完成，模型工件将存储在AWS S3桶中。所有训练细节都通过[Comet ML](https://www.comet.com/site/)进行跟踪；

+   然后使用评估算法在**基准**上评估微调后的模型。根据模型的大小，这个过程可能非常漫长。因此，我们使用了[vLLM](https://github.com/vllm-project/vllm)，一个旨在加速LLM推理的Python库；

+   针对基准的预测结果，存储在AWS S3中，发送到[Argilla](https://argilla.io/)进行人工评估。

在多次迭代数据精炼和模型训练之后，我们在拼写检查任务上达到了**与专有LLM相媲美**的性能，F1分数为**0.65**。

![](../Images/7fdb68e96e8e460f5871149e4f6a61df.png)![](../Images/407f381cc9c76a371fa2ee8a89f453ca.png)

LLMs在我们基准上的评估（图像来源：作者）

该模型是微调后的[Mistral-7B-Base-v0.3](https://huggingface.co/mistralai/Mistral-7B-v0.3)，可在Hugging Face平台上获取，并且是公开可用的，连同其[数据集](https://huggingface.co/datasets/openfoodfacts/spellcheck-dataset)和评估[基准](https://huggingface.co/datasets/openfoodfacts/spellcheck-benchmark)。

[](https://huggingface.co/openfoodfacts/spellcheck-mistral-7b?source=post_page-----d74dfe02e0e4--------------------------------) [## openfoodfacts/spellcheck-mistral-7b · Hugging Face

### 我们正在努力通过开源和开放科学推动并普及人工智能。

huggingface.co](https://huggingface.co/openfoodfacts/spellcheck-mistral-7b?source=post_page-----d74dfe02e0e4--------------------------------)

此外，**我们估计拼写检查减少了11%的未识别成分，这很有前景！**

现在进入项目的最后阶段：将模型集成到Open Food Facts中。

# 部署与集成

**我们的模型很大！**

70亿个参数，这意味着在**float16**格式下运行时需要**14 GB**内存，还不包括**20%的开销因子**。

此外，大型模型通常意味着**推理期间吞吐量低**，这可能使其不适合实时服务。我们需要配备大内存的GPU来在生产环境中运行该模型，例如[Nvidia L4](https://www.nvidia.com/en-us/data-center/l4/)，其配备了24GB的显存。

**但在云端运行这些实例的成本相当昂贵……**

然而，提供实时体验的可能性，而不需要24/7运行GPU实例，就是**批处理推理**。

成分列表定期通过模型进行批处理处理，然后存储在数据库中。**这样，我们只需为批处理过程中使用的资源付费！**

## 批处理作业

我们开发了一个批处理系统，通过[Google Batch Job](https://cloud.google.com/batch/docs/get-started)高效地处理大规模文本处理任务。

![](../Images/e0c7d92fc5854b223efc5af25ed260ec.png)

批处理系统（图像来源：作者）

该过程首先通过使用DuckDB从Open Food Facts数据库提取数据，**在不到2分钟的时间内处理43GB的数据！**

提取的数据随后发送到Google Bucket，触发Google批处理作业。

该作业使用一个预先准备的Docker镜像，包含所有必要的依赖项和算法。为了优化资源密集型的LLM处理，我们重用了vLLM，取得了令人印象深刻的表现，**仅用一块GPU L4就能在20分钟内修正10,000个食材列表！**

经过成功处理后，修正后的数据保存在一个中间数据库中，包含Open Food Facts中所有模型的预测，由[Robotoff](https://github.com/openfoodfacts/robotoff)提供服务。

当贡献者修改产品详情时，他们将看到拼写检查的修正建议，**确保用户仍然是Open Food Facts数据质量过程中的关键决策者。**

该系统使Open Food Facts能够利用先进的AI能力来提高数据质量，同时保持其社区驱动的方法。

![](../Images/d73441d66e04c9ab650600565b6475d7.png)

GCP中的批处理作业（图片来自作者）

# 结论

在这篇文章中，我们带您了解了**食材拼写检查**的开发和集成，这是一个由LLM驱动的功能，用于修正OCR提取的食材列表。

我们首先制定了一套规则，**拼写检查指南**，来限制修正范围。我们创建了一个经过修正的食材列表基准，并通过自定义评估算法来评估任何解决方案。

在这种设置下，我们评估了各种私有LLM，并确定**GPT-3.5-Turbo**是最适合我们特定用例的模型。然而，我们也展示了依赖私有LLM带来的显著限制，包括缺乏所有权和对如此大型模型（1750亿个参数）进行改进或微调的机会受限。

为了应对这些挑战，我们决定**开发我们自己的模型**，并在从数据库提取的合成修正文本上进行微调。经过几轮迭代和实验，我们成功地通过一个**开源模型**达到了良好的表现。我们不仅匹配了私有LLM的性能，还解决了我们面临的所有权问题，使我们对模型拥有完全控制权。

我们随后**将该模型集成到Open Food Facts**中，使用**批处理推理部署**，使我们能够定期处理成千上万的食材列表。预测结果存储在Robotoff数据库中作为**洞察**，然后由贡献者验证，**将OFF的数据质量所有权留给贡献者**。

# 下一步

**拼写检查集成仍在进行中**。我们正在设计用户界面，以提供机器学习生成的修正建议，并让贡献者接受、拒绝或修改这些修正。**我们预计将在年底前完成该功能的完全集成。**

![](../Images/4873fa37ebe6d27a18074cf083f3e87d.png)

用户验证的拼写检查已内置于[Hugging Face Space](https://huggingface.co/spaces/openfoodfacts/ingredients-spellcheck-annotate)

此外，我们计划通过持续的迭代改进来不断完善模型。通过提高训练数据的质量并结合用户反馈，可以显著提升其性能。这种方法将使我们能够持续微调模型，确保其始终保持高效，并与实际应用场景高度契合。

该模型及其数据集可以在官方[Hugging Face仓库](https://huggingface.co/openfoodfacts)中找到。用于开发该模型的代码可以在[OpenFoodFacts-ai/spellcheck](https://github.com/openfoodfacts/openfoodfacts-ai/tree/develop/spellcheck)的Github仓库中找到。

感谢你阅读到这里！我们希望你喜欢这篇文章。

如果你也想为Open Food Facts做出贡献，你可以：

+   为Open Food Facts的[GitHub](https://github.com/openfoodfacts)做贡献：探索与您技能相匹配的开放问题，

+   下载Open Food Facts的[移动应用](https://world.openfoodfacts.org/open-food-facts-mobile-app?utm_source=off&utf_medium=web&utm_campaign=search_and_links_promo_en)：通过扫描条形码，轻松添加新产品或改进现有产品。

+   加入Open Food Facts的[Slack](https://slack.openfoodfacts.org/)，并与其他贡献者在OFF社区中开始讨论。

我们迫不及待地想看到你加入社区！

不要犹豫，查看我们的其他文章：

[](https://medium.com/@jeremyarancio/duckdb-open-food-facts-the-largest-open-food-database-in-the-palm-of-your-hand-0d4ab30d0701?source=post_page-----d74dfe02e0e4--------------------------------) [## DuckDB与Open Food Facts：掌中最大的开放食品数据库 🦆🍊

### 利用DuckDB的强大功能，探索食品市场中最大的开放数据库。

medium.com](https://medium.com/@jeremyarancio/duckdb-open-food-facts-the-largest-open-food-database-in-the-palm-of-your-hand-0d4ab30d0701?source=post_page-----d74dfe02e0e4--------------------------------)
