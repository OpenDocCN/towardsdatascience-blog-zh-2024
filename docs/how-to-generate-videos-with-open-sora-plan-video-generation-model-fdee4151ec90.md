# 如何使用Open-Sora-Plan视频生成模型生成视频

> 原文：[https://towardsdatascience.com/how-to-generate-videos-with-open-sora-plan-video-generation-model-fdee4151ec90?source=collection_archive---------9-----------------------#2024-04-15](https://towardsdatascience.com/how-to-generate-videos-with-open-sora-plan-video-generation-model-fdee4151ec90?source=collection_archive---------9-----------------------#2024-04-15)

## 学习如何使用最新的图像生成模型之一生成图像。

[](https://oieivind.medium.com/?source=post_page---byline--fdee4151ec90--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--fdee4151ec90--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fdee4151ec90--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fdee4151ec90--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--fdee4151ec90--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fdee4151ec90--------------------------------) ·阅读时间10分钟·2024年4月15日

--

在本文中，你将学习如何使用[Open-Sora-Plan视频生成](https://github.com/PKU-YuanGroup/Open-Sora-Plan?tab=readme-ov-file)模型[1]，这是一个强大的模型，允许你像在[OpenAI Sora](https://openai.com/sora)中看到的那样，创建你自己的视频。我将讨论可以应用该模型的不同任务，例如生成动画视频和创建合成数据集。然后，我会分享对该模型性能的看法，并讨论它的优点和缺点。最终，你应该能更清楚地了解从这个模型中能获得的实际效用。

![](../Images/3d27ba7fe39c90cb3e73d1abc7db6ac4.png)

Open-Sora-Plan根据提示“写一篇文章”生成了图像。模型似乎理解了该提示，因为它展示了纸上的手写字迹，但图像质量较低。图像由作者使用Open-Sora-Plan模型制作。

# 动机

我写这篇文章的动机是，作为数据科学家，重要的一部分工作就是跟上机器学习领域内最新的模型。紧跟人工智能的不断进展需要时间和精力。因此，我在一系列文章中提到了一些我在[PapersWithCode](https://paperswithcode.com/)、[GitHub](https://github.com/)和[HuggingFace](https://huggingface.co/blog)等页面上发现的有前景的模型。在发现一个模型有趣之后，我会亲自测试它，看看它是否能帮助我正在进行的工作或个人项目。如果该模型可能有益，我就会考虑将其应用到我需要的任务中。
