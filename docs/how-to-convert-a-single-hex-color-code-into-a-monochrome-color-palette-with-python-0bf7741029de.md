# 如何使用Python将单一的HEX颜色代码转换为单色调色板

> 原文：[https://towardsdatascience.com/how-to-convert-a-single-hex-color-code-into-a-monochrome-color-palette-with-python-0bf7741029de?source=collection_archive---------4-----------------------#2024-09-27](https://towardsdatascience.com/how-to-convert-a-single-hex-color-code-into-a-monochrome-color-palette-with-python-0bf7741029de?source=collection_archive---------4-----------------------#2024-09-27)

## 剧透：这比你想象的要难。

[](https://medium.com/@radecicdario?source=post_page---byline--0bf7741029de--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--0bf7741029de--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0bf7741029de--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0bf7741029de--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--0bf7741029de--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0bf7741029de--------------------------------) ·8分钟阅读·2024年9月27日

--

![](../Images/aa510cbb07f703987996efe03f125a70.png)

文章缩略图（作者提供的图片）

颜色很难处理，尤其是当你没有设计眼光时。

我们大多数技术专业人士并没有。好的一面是，Python可以为你完成大部分繁重的工作。它可以生成一整套单色调色板，在任何堆叠的图表上看起来都很惊艳。

不利的一面是，达到目标需要编写相当多的代码。你需要编写自定义函数将颜色从HEX转换为HSL，反之亦然，还要弄清楚起始颜色是否过于明亮，以及每个下一个颜色应该变得多么更亮。

**更糟糕的是，我还没找到一个完全能实现这个任务的Python库。**

这就是本文的重点所在。

如果你是我的[Substack订阅者](https://darioradecic.substack.com)，你可以跳过阅读，直接[下载笔记本](https://github.com/darioradecic/Data-Doodles-with-Python/tree/main/018_hex_to_monochrome_color_palette)。

# HEX、HSL、RGB —— 这些术语是什么意思？

让我们快速了解你需要知道的三种颜色格式：

+   **HEX** —— 一个六位数字的代码，通常用于网页开发和图形设计。颜色代码以`#`开始，后面跟着六个十六进制数字。每对……
