# Gradio: 高级布局

> 原文：[`towardsdatascience.com/gradio-beyond-the-interface-f37a4dae307d?source=collection_archive---------9-----------------------#2024-04-30`](https://towardsdatascience.com/gradio-beyond-the-interface-f37a4dae307d?source=collection_archive---------9-----------------------#2024-04-30)

![](img/e2ffcfddba171e60f70d274bf0702430.png)

图片由 AI 创建（Ideogram）

## 不要使用 Interface，改用 Blocks

[](https://medium.com/@thomas_reid?source=post_page---byline--f37a4dae307d--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--f37a4dae307d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f37a4dae307d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f37a4dae307d--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--f37a4dae307d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f37a4dae307d--------------------------------) ·6 分钟阅读·2024 年 4 月 30 日

--

当你第一次使用 Gradio 时，你很可能会使用 Interface 方法来展示你的 GUI。之所以如此，是因为它提供了简单性，如果你的页面布局不复杂，这基本上就足够了。在本文中，我们将探讨当你的 Gradio 布局超出 Interface 时该如何处理。

在继续之前，如果你是第一次接触 Gradio，请查看我之前的另一篇文章（链接见下方），在文章中我介绍了 Gradio 是什么。简单来说，它是一个非常实用且易于编码的基于 Python 的 GUI，可以让你快速搭建一个本地前端，方便用户与你编写的代码进行交互。

[](https://ai.gopubby.com/gradio-rapid-gui-prototyping-a0091c28116b?source=post_page-----f37a4dae307d--------------------------------) [## Gradio: 快速 GUI 原型设计

### 使用 Python 在几分钟内创建直观的网页界面

ai.gopubby.com](https://ai.gopubby.com/gradio-rapid-gui-prototyping-a0091c28116b?source=post_page-----f37a4dae307d--------------------------------)

此外，由于我们将进行一些编码，最佳实践是设置一个新的开发环境，以便我们所做的工作不会干扰到其他项目。我大多数的文章编程使用 Python 和 Jupyter，并且我有一篇 [文章在这里](https://medium.com/@thomas_reid/setting-up-a-python-dev-environment-63e502c793a7) 介绍了如何设置我的开发环境，文中解释了你需要做什么。
