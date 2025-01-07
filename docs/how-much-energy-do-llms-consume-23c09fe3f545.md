# 大语言模型消耗多少能源？

> 原文：[`towardsdatascience.com/how-much-energy-do-llms-consume-23c09fe3f545?source=collection_archive---------4-----------------------#2024-07-02`](https://towardsdatascience.com/how-much-energy-do-llms-consume-23c09fe3f545?source=collection_archive---------4-----------------------#2024-07-02)

## 我们使用 EnergyMeter 这一 Python 工具来测量不同 LLM（大语言模型）的能耗，包括 Llama、Dolly 和 BLOOM。

[](https://medium.com/@mauriciofadelargerich?source=post_page---byline--23c09fe3f545--------------------------------)![Mauricio Fadel Argerich](https://medium.com/@mauriciofadelargerich?source=post_page---byline--23c09fe3f545--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--23c09fe3f545--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--23c09fe3f545--------------------------------) [Mauricio Fadel Argerich](https://medium.com/@mauriciofadelargerich?source=post_page---byline--23c09fe3f545--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--23c09fe3f545--------------------------------) ·12 分钟阅读·2024 年 7 月 2 日

--

![](img/5630ac576e53b591e0392028054cfb64.png)

回答所有这些问题需要大量的能源！[由 AI 制作 [Designer](https://www.bing.com/images/create?form=SBCATT)。]

大语言模型（LLM）正在成为我们日常执行的几个任务的新主流：搜索日常问题的答案、总结文本、制作幻灯片或为本文创建图像；LLM 是强大的工具，可以说它们的使用将在未来几年增加。它们惊人的能力部分得益于它们拥有的（非常）大量可训练参数；举个例子，ChatGPT 背后的模型（GPT-3）有超过 1750 亿个参数。训练如此庞大的模型需要极其强大的计算资源（特别是 GPU），这意味着购买或租用这些资源的成本非常高，进而带来很高的能源需求，以供给这些资源的动力。例如，据估计，训练 GPT-3 需要 1287 MWh，相当于 420 人的年平均能源消耗[1]。

但是，为了保持这些 LLM 的运行，进行推理时消耗的能量更多。实际上，OpenAI 宣布每周有 1 亿人使用 ChatGPT[2]，即使我们假设每个用户每周只提问四个问题，这意味着 ChatGPT 至少…
