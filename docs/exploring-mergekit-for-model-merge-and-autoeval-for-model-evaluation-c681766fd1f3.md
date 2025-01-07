# 探索 mergekit 进行模型合并，AutoEval 进行模型评估，以及 DPO 进行模型微调

> 原文：[`towardsdatascience.com/exploring-mergekit-for-model-merge-and-autoeval-for-model-evaluation-c681766fd1f3?source=collection_archive---------5-----------------------#2024-01-19`](https://towardsdatascience.com/exploring-mergekit-for-model-merge-and-autoeval-for-model-evaluation-c681766fd1f3?source=collection_archive---------5-----------------------#2024-01-19)

## 我在实验模型合并、评估和两种模型微调技术时的观察结果

[](https://medium.com/@wenqiglantz?source=post_page---byline--c681766fd1f3--------------------------------)![Wenqi Glantz](https://medium.com/@wenqiglantz?source=post_page---byline--c681766fd1f3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c681766fd1f3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c681766fd1f3--------------------------------) [Wenqi Glantz](https://medium.com/@wenqiglantz?source=post_page---byline--c681766fd1f3--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c681766fd1f3--------------------------------) ·14 分钟阅读·2024 年 1 月 19 日

--

![](img/fbfcc3ab489becdd52f76fbc342fc138.png)

由作者通过 DALL-E 3 生成的图像

让我们继续学习[Maxime Labonne](https://medium.com/u/dc89da634938?source=post_page---user_mention--c681766fd1f3--------------------------------)的[llm-course](https://github.com/mlabonne/llm-course)，这对社区来说是纯粹的宝藏。这一次，我们将重点关注模型合并、评估和微调。

Maxime 有一篇很棒的文章，标题是[用 mergekit 合并大型语言模型](https://medium.com/towards-data-science/merge-large-language-models-with-mergekit-2118fb392b54)。我强烈推荐你先去阅读一下。我们不会重复他在文章中已经列出的步骤，但我们会探索一些我遇到的细节，这些可能对你有帮助。

# 高层次概述

我们将在以下步骤中进行模型合并、模型评估和模型微调的实验：

+   使用[LazyMergekit](https://colab.research.google.com/drive/1obulZ1ROXHjYLn6PPZJwRR6GzgQogxxb?usp=sharing)，我们合并了来自 Hugging Face hub 的两个模型，`mistralai/Mistral-7B-Instruct-v0.2`和`jan-hq/trinity-v1`。

+   对基础模型`mistralai/Mistral-7B-Instruct-v0.2`运行 AutoEval。

+   对合并后的模型`MistralTrinity-7b-slerp`运行 AutoEval。

+   使用 QLoRA 对合并后的模型进行监督微调。对微调后的模型运行 AutoEval。
