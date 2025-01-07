# 使用苹果的 MLX 框架在本地部署 LLM

> 原文：[`towardsdatascience.com/deploying-llms-locally-with-apples-mlx-framework-2b3862049a93?source=collection_archive---------2-----------------------#2024-01-20`](https://towardsdatascience.com/deploying-llms-locally-with-apples-mlx-framework-2b3862049a93?source=collection_archive---------2-----------------------#2024-01-20)

## 对新的深度学习库 MLX 的技术深入探讨

[](https://heiko-hotz.medium.com/?source=post_page---byline--2b3862049a93--------------------------------)![Heiko Hotz](https://heiko-hotz.medium.com/?source=post_page---byline--2b3862049a93--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2b3862049a93--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2b3862049a93--------------------------------) [Heiko Hotz](https://heiko-hotz.medium.com/?source=post_page---byline--2b3862049a93--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2b3862049a93--------------------------------) ·9 分钟阅读·2024 年 1 月 20 日

--

![](img/11ece286cb8fbcacee9ff6dbb426b6b9.png)

图片由作者提供（使用 DALL-E 3）

# 这是什么内容？

2023 年 12 月，苹果发布了他们的新[MLX 深度学习框架](https://github.com/ml-explore/mlx)，这是一个为苹果自家芯片设计的机器学习数组框架，由他们的机器学习研究团队开发。本文将探讨该框架，并演示如何在 MacBook Pro（MBP）上本地部署 Mistral-7B 模型。我们将设置一个本地聊天界面，与部署的模型进行交互，并测试其推理性能，即每秒生成的 tokens 数量。此外，我们还将深入了解 MLX API，理解可用的控制手段，用于调整模型行为并影响生成的文本。

一如既往，代码可在公开的 GitHub 仓库中获取：[`github.com/marshmellow77/mlx-deep-dive`](https://github.com/marshmellow77/mlx-deep-dive)

# 为什么这很重要？

苹果的新机器学习框架 MLX，在其统一内存架构方面提供了相较于其他深度学习框架的显著优势，专为苹果自家芯片上的机器学习设计。与传统框架（如 PyTorch 和 Jax）不同，后者需要在 CPU 和 GPU 之间进行昂贵的数据复制，MLX 则将数据保存在可供两者访问的共享内存中。这个设计消除了数据复制的开销……
