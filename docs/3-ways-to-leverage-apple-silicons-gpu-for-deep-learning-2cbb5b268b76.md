# 利用 Apple Silicon 的 GPU 进行深度学习的 3 种方法

> 原文：[https://towardsdatascience.com/3-ways-to-leverage-apple-silicons-gpu-for-deep-learning-2cbb5b268b76?source=collection_archive---------3-----------------------#2024-03-01](https://towardsdatascience.com/3-ways-to-leverage-apple-silicons-gpu-for-deep-learning-2cbb5b268b76?source=collection_archive---------3-----------------------#2024-03-01)

## Apple Silicon 提供了令人印象深刻的性能提升，并且具有极好的功率效率。我们可以将这些芯片用于深度学习吗？

[](https://medium.com/@jiri.moravcik?source=post_page---byline--2cbb5b268b76--------------------------------)[![Jiří Moravčík](../Images/2056f2f3f5b40732d5891a5d91b6a9d0.png)](https://medium.com/@jiri.moravcik?source=post_page---byline--2cbb5b268b76--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2cbb5b268b76--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2cbb5b268b76--------------------------------) [Jiří Moravčík](https://medium.com/@jiri.moravcik?source=post_page---byline--2cbb5b268b76--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2cbb5b268b76--------------------------------) ·阅读时间 4 分钟·2024年3月1日

--

![](../Images/bf7f41b799bfdffb1f7d5364a694b3ad.png)

作者提供的图片

Apple Silicon 提供了令人印象深刻的性能提升，并且具有出色的功率效率。那么这些芯片也能用于深度学习吗？绝对可以！

在本文中，我们将探索 3 种方法，利用 Apple Silicon 的 GPU 来处理各种深度学习任务。

# 1\. 在 PyTorch 中使用 MPS 后端

使用 GPU 进行深度学习的最简单方法是通过 [Metal 性能着色器 (MPS)](https://pytorch.org/docs/stable/notes/mps.html)。MPS 扩展了 [PyTorch](https://pytorch.org/) 框架，以便利用 Mac 上的 GPU。

要使用 MPS 后端，您需要：

+   macOS 12.3 或更高版本

+   Python 3.7 或更高版本

让我们创建一个脚本，检查 MPS 是否设置正确。首先，我们需要创建一个包含虚拟环境的目录，并安装 PyTorch：

```py
mkdir mps_test
cd mps_test
python3 -m venv .venv
source .venv/bin/activate
pip install torch
```

接下来，我们将创建一个 Python 文件，`check.py`，并使用以下代码：
