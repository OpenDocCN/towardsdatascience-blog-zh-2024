# Apple M3 机器学习速度测试

> 原文：[`towardsdatascience.com/apple-m3-machine-learning-speed-test-f1346e23a1b2?source=collection_archive---------0-----------------------#2024-01-09`](https://towardsdatascience.com/apple-m3-machine-learning-speed-test-f1346e23a1b2?source=collection_archive---------0-----------------------#2024-01-09)

## 苹果的 M3、M3 Pro 和 M3 Max 与 TensorFlow 和 PyTorch 相比如何？

[](https://mrdbourke.medium.com/?source=post_page---byline--f1346e23a1b2--------------------------------)![Daniel Bourke](https://mrdbourke.medium.com/?source=post_page---byline--f1346e23a1b2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f1346e23a1b2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f1346e23a1b2--------------------------------) [Daniel Bourke](https://mrdbourke.medium.com/?source=post_page---byline--f1346e23a1b2--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f1346e23a1b2--------------------------------) ·10 分钟阅读·2024 年 1 月 9 日

--

![](img/a52dada3bd31832fb9b8bad3cd432838.png)

四台 MacBook Pro 对比八个机器学习测试。哪台机器学习最快？来源：作者的客厅。

在过去的两年里，我一直在使用我的 M1 Pro MacBook Pro 14 英寸。

我购买了升级版，增加了更多内存、GPU 核心和存储，以确保未来使用。

而且它一直表现稳定。

但苹果最新发布的 M3 系列让我产生了好奇。

我观看了演示，并看到了一堆关于它们是近年来 GPU 性能跃升最大的一些图表。

作为一名机器学习工程师，自然，我对它们在机器学习角度的表现产生了好奇。

我的 M1 Pro 在日常使用中无可匹敌。

我喜欢它。

但是我不会在其上训练更大规模的机器学习模型。

M3 系列能改变这一点吗？

我做了一些测试来找出答案。

# 资源

+   [GitHub 上的代码](https://github.com/mrdbourke/mac-ml-speed-test) — 我用来设置并运行机器测试的所有代码都可以在 GitHub 上找到。

+   [视频演示](https://youtu.be/cpYqED1q6ro) — 我还在 YouTube 上制作了一个视频演示，展示了所有结果并给出了一些建议和推荐。
