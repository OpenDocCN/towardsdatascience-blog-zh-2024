# Llama-2与Llama-3：模型间的井字游戏对决

> 原文：[https://towardsdatascience.com/llama-2-vs-llama-3-a-tic-tac-toe-battle-between-models-7301962ca65d?source=collection_archive---------5-----------------------#2024-04-27](https://towardsdatascience.com/llama-2-vs-llama-3-a-tic-tac-toe-battle-between-models-7301962ca65d?source=collection_archive---------5-----------------------#2024-04-27)

## 使用Python和Llama-CPP进行非科学性基准测试

[](https://dmitryelj.medium.com/?source=post_page---byline--7301962ca65d--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--7301962ca65d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7301962ca65d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7301962ca65d--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--7301962ca65d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7301962ca65d--------------------------------) ·阅读时长10分钟·2024年4月27日

--

![](../Images/b6acd24b61201d2f078a6988680091e7.png)

图片来源：[Solstice Hannan](https://unsplash.com/@darkersolstice)，来自Unsplash

在撰写本文的前一周，Meta发布了新的开源Llama-3模型，[可以在此查看](https://ai.meta.com/blog/meta-llama-3/)。Meta宣称，这些是“目前8B和70B参数规模上最好的模型。”例如，依据[HuggingFace模型页面](https://huggingface.co/meta-llama/Meta-Llama-3-70B-Instruct)，Llama-3 8B在MMLU（大规模多任务语言理解基准）中的得分为66.6，而Llama-2 7B为45.7。Llama-3还在常识问答数据集(CommonSense QA)中获得了72.6的得分，而Llama-2为57.6。经过指令调优的Llama-3 8B模型在数学基准测试中获得了30.0的得分，而Llama-2仅为3.8，这确实是一个令人印象深刻的改进。

学术基准很重要，但我们能看到真正的“实际差异”吗？显然，我们可以，而且这可能很有趣。让我们编写一个井字游戏，让两个模型对战，看看哪个会赢！在游戏过程中，我将测试所有的7B、8B和70B模型。同时，我还会收集一些关于模型性能和系统需求的数据。所有测试都可以在Google Colab中免费运行。

让我们开始吧！

## 加载模型

为了测试所有模型，我将使用[Llama-cpp](https://github.com/abetlen/llama-cpp-python) Python库，因为它可以在CPU和GPU上运行。我们…
