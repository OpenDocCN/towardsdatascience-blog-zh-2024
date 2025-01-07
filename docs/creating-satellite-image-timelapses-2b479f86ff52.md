# 创建卫星图像时间推移

> 原文：[https://towardsdatascience.com/creating-satellite-image-timelapses-2b479f86ff52?source=collection_archive---------6-----------------------#2024-03-24](https://towardsdatascience.com/creating-satellite-image-timelapses-2b479f86ff52?source=collection_archive---------6-----------------------#2024-03-24)

![](../Images/153eaa5de9156add5e69398e0bd340a7.png)

## 你将从新的ESA Sentinel Hub API下载卫星图像，并使用纯Python将它们合并为动画GIF。

[](https://medium.com/@janosovm?source=post_page---byline--2b479f86ff52--------------------------------)[![Milan Janosov](../Images/b7ede67b165cdd368d96f13f46c68ccb.png)](https://medium.com/@janosovm?source=post_page---byline--2b479f86ff52--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2b479f86ff52--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2b479f86ff52--------------------------------) [Milan Janosov](https://medium.com/@janosovm?source=post_page---byline--2b479f86ff52--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2b479f86ff52--------------------------------) ·阅读时长9分钟·2024年3月24日

--

前不久，我在我的[文章](/deep-dive-into-esas-sentinel-api-e6ff4f9d0730)《深入了解ESA的Sentinel API》中总结了如何收集和准备来自欧洲航天局Sentinel卫星的卫星图像数据。从那时起，ESA对Sentinel Hub进行了多年来未曾见过的重要更新，并更新了API访问方法。因此，我简要回顾了如何从当前API获取数据。作为额外的应用案例，我还展示了如何使用纯Python将下载的卫星图像快照合并为动画GIF。本文旨在帮助你入门，并根据特定的使用案例和数据集，提供进一步探索API的可能性。

*所有图像均由作者创作。*

# 1\. 入门

首先，你需要安装sentinelhub Python库，你可以在Jupyter Notebook中运行以下代码单元来完成安装：

```py
import sys
!{sys.executable} -m pip install sentinelhub --upgrade 
```

然后，你还需要[注册](https://docs.sentinel-hub.com/api/latest/api/overview/authentication/#registering-oauth-client)并创建你的访问令牌。你可以在此处阅读如何操作，并让你的账户顺利运行…
