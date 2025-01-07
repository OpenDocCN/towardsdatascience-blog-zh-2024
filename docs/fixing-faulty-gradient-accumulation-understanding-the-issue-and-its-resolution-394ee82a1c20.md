# 修复故障的梯度累积：理解问题及其解决方案

> 原文：[https://towardsdatascience.com/fixing-faulty-gradient-accumulation-understanding-the-issue-and-its-resolution-394ee82a1c20?source=collection_archive---------3-----------------------#2024-10-23](https://towardsdatascience.com/fixing-faulty-gradient-accumulation-understanding-the-issue-and-its-resolution-394ee82a1c20?source=collection_archive---------3-----------------------#2024-10-23)

## 多年的次优模型训练？

[](https://medium.com/@bnjmn_marie?source=post_page---byline--394ee82a1c20--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--394ee82a1c20--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--394ee82a1c20--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--394ee82a1c20--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--394ee82a1c20--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--394ee82a1c20--------------------------------) ·阅读时间：10分钟·2024年10月23日

--

![](../Images/3f3d8e3d9c8058731aa4e2b5202de6ed.png)

图片由作者提供

当在本地微调大型语言模型（LLM）时，由于其巨大的GPU内存消耗，使用大批量往往是不可行的。为了解决这个限制，通常使用一种叫做*梯度累积*的技术来模拟更大的批量。梯度累积不是在处理每个批次后立即更新模型权重，而是通过在多个较小的迷你批次上累积梯度。只有在处理完预定数量的这些迷你批次后，才会更新模型权重。这种方法有效地模拟了使用较大批量训练的效果，而不会带来通常与大批量训练相关的内存开销。

例如，设置迷你批次大小为1，并在32个迷你批次上累积梯度，应该等效于使用完整批量大小为32进行训练。然而，我发现，与使用较大的实际批量大小进行训练相比，梯度累积通常会导致性能显著下降，尤其是在使用像Transformers这样的流行深度学习框架时。

在[推特](https://x.com/bnjmn_marie/status/1842202652672671964)和[Reddit](https://www.reddit.com/r/LocalLLaMA/comments/1g0dy0k/finetuning_with_small_batch_sizes_and_gradient/)上分享这个问题后，[Unsloth AI](https://unsloth.ai/)的Daniel Han复制了这个问题。他发现这个问题不仅影响梯度累积，还影响到多GPU的设置。在这种情况下...
