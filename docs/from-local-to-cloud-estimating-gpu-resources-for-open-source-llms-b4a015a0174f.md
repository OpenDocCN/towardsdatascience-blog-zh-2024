# 从本地到云端：估算开源 LLM 所需的 GPU 资源

> 原文：[`towardsdatascience.com/from-local-to-cloud-estimating-gpu-resources-for-open-source-llms-b4a015a0174f?source=collection_archive---------4-----------------------#2024-11-18`](https://towardsdatascience.com/from-local-to-cloud-estimating-gpu-resources-for-open-source-llms-b4a015a0174f?source=collection_archive---------4-----------------------#2024-11-18)

## *估算部署最新开源 LLM 所需的 GPU 内存*

[](https://medium.com/@maximejabarian?source=post_page---byline--b4a015a0174f--------------------------------)![Maxime Jabarian](https://medium.com/@maximejabarian?source=post_page---byline--b4a015a0174f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b4a015a0174f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4a015a0174f--------------------------------) [Maxime Jabarian](https://medium.com/@maximejabarian?source=post_page---byline--b4a015a0174f--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4a015a0174f--------------------------------) ·阅读时间 4 分钟·2024 年 11 月 18 日

--

![](img/bcea4003ded428dc0835ca4f0ec07375.png)

[来源](https://unsplash.com/fr/photos/gros-plan-dune-carte-video-sur-fond-jaune-ipVMl4H6g6o)

如果你像我一样，可能会对最新和最强大的开源大语言模型（LLM）感到兴奋——从像 Llama 3 这样的模型到更紧凑的 Phi-3 Mini。但在你跳进部署语言模型之前，有一个关键因素需要提前规划：**GPU 内存**。如果判断失误，你闪亮的新网页应用可能会崩溃、运行缓慢，或者产生高额的云计算费用。为了简化这一过程，我将为你解释什么是量化，并准备了一份***2024 年 GPU 内存规划备忘单***——这是市场上最新开源 LLM 的简明总结，以及部署前需要了解的事项。

如果你不是会员，[**请阅读此处**](https://medium.com/towards-data-science/from-local-to-cloud-estimating-gpu-resources-for-open-source-llms-b4a015a0174f)**。**

# **为什么要估算 GPU 内存？**

在部署 LLM 时，估算需要多少 GPU 内存是有风险的。内存过少，模型可能崩溃；内存过多，则是在浪费资源，导致不必要的开销。

> 预先了解这些内存需求，就像在公路旅行之前知道你能将多少行李塞进车里——这可以避免头疼问题并提高效率。
