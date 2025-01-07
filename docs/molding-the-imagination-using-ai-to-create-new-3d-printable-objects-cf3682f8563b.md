# 塑造想象力：使用 AI 创建新的 3D 可打印物体

> 原文：[https://towardsdatascience.com/molding-the-imagination-using-ai-to-create-new-3d-printable-objects-cf3682f8563b?source=collection_archive---------8-----------------------#2024-02-13](https://towardsdatascience.com/molding-the-imagination-using-ai-to-create-new-3d-printable-objects-cf3682f8563b?source=collection_archive---------8-----------------------#2024-02-13)

## 将你的创意转化为具体的物品，使用 Midjourney 和开源项目：Shap-E、MVDream 和 threestudio

[](https://robgon.medium.com/?source=post_page---byline--cf3682f8563b--------------------------------)[![Robert A. Gonsalves](../Images/96b4da0f602a1cd9d1e1d2917868cbee.png)](https://robgon.medium.com/?source=post_page---byline--cf3682f8563b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cf3682f8563b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cf3682f8563b--------------------------------) [Robert A. Gonsalves](https://robgon.medium.com/?source=post_page---byline--cf3682f8563b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cf3682f8563b--------------------------------) ·21 分钟阅读·2024年2月13日

--

![](../Images/2caf9a013805f0440a8433cbcf189c76.png)

**AI 系统根据文本提示生成的 3D 打印物体**，图片由作者提供

如果你一直在阅读我在 Medium 上的文章，你就会知道我喜欢通过使用 AI 进行创作实验并撰写我的经验。我已经涵盖了图像生成、创意写作和音乐创作等领域。第一次，我将我的研究拓展到了第三维度。我研究了使用商业和开源 AI 工具来创建新的物理对象，并使用 3D 打印机将它们打印出来。在本文中，我将展示我使用各种商业和开源工具设计和打印四种不同 3D 网格的步骤。你将在附录的 3D 画廊中看到这四个物体。

# 概述

在接下来的章节中，我将带领你了解我使用不同工具进行 3D 对象生成的四个实验，并展示结果。第一个实验使用了商业工具：[Midjourney](https://www.midjourney.com/) 来创建 2D 图像，以及一个名为 [3dMaker.ai](https://3dmaker.ai/) 的网站来提取 3D 网格，这是一种包含几何形状的文件，用于在 3D 打印和显示中使用。第二个实验使用了 OpenAI 的开源 AI 模型 Shape-E [1]。第三个实验使用了…
