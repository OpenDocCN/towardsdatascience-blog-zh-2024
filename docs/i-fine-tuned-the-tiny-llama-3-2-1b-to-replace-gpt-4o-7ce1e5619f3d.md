# 我将 Tiny Llama 3.2 1B 微调成替代 GPT-4o

> 原文：[`towardsdatascience.com/i-fine-tuned-the-tiny-llama-3-2-1b-to-replace-gpt-4o-7ce1e5619f3d?source=collection_archive---------0-----------------------#2024-10-15`](https://towardsdatascience.com/i-fine-tuned-the-tiny-llama-3-2-1b-to-replace-gpt-4o-7ce1e5619f3d?source=collection_archive---------0-----------------------#2024-10-15)

## 微调的努力是否比几次示例提示更有价值？

[](https://thuwarakesh.medium.com/?source=post_page---byline--7ce1e5619f3d--------------------------------)![Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--7ce1e5619f3d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7ce1e5619f3d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7ce1e5619f3d--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--7ce1e5619f3d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7ce1e5619f3d--------------------------------) ·阅读时长 8 分钟·2024 年 10 月 15 日

--

![](img/aebbd921b0305baa1fc782b987d9532a.png)

图片由作者使用 Flux.1.1-pro 制作

一位年轻的儿科医生和一位著名的内科医生，谁能更好地治疗宝宝的咳嗽？

虽然两者都是医生，并且都可以治疗孩子的咳嗽，但儿科医生是一个专科医生，可以更好地诊断婴儿，是吧？

这就是微调对较小模型的作用。它们让这些微小且较弱的模型比那些声称能做任何事的庞大模型更好地解决特定问题。

我最近遇到了一种情况，不得不做出选择。

我曾在构建一个查询路由机器人。它将用户的查询路由到正确的部门，由人工客服继续对话。在后台，它是一个简单的文本分类任务。

GPT-4o（以及迷你版）做得非常好，但它的灵活性差且成本高昂。它是一个封闭模型，因此你无法在自己的基础设施上对其进行微调。OpenAI 提供了其平台上的微调服务，但对我来说这成本太高。

训练 GPT-4o 的费用是$25/1M token。我的训练数据很快就达到几百万个 token。此外，**为微调后的模型提供服务的成本比常规模型高出大约 50%**。
