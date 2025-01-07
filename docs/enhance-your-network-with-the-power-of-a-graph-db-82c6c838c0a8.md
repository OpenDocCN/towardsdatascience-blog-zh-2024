# 利用图形数据库的强大功能提升你的网络分析

> 原文：[https://towardsdatascience.com/enhance-your-network-with-the-power-of-a-graph-db-82c6c838c0a8?source=collection_archive---------2-----------------------#2024-05-04](https://towardsdatascience.com/enhance-your-network-with-the-power-of-a-graph-db-82c6c838c0a8?source=collection_archive---------2-----------------------#2024-05-04)

![](../Images/c66d7785a547d61415e242e357f044a1.png)

## 在5分钟内通过图形数据库和交互式可视化设置完成，所有相关代码已为你编写。

[](https://medium.com/@bl3e967?source=post_page---byline--82c6c838c0a8--------------------------------)[![Benjamin Lee](../Images/a369931c73019143609812354c773151.png)](https://medium.com/@bl3e967?source=post_page---byline--82c6c838c0a8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--82c6c838c0a8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--82c6c838c0a8--------------------------------) [Benjamin Lee](https://medium.com/@bl3e967?source=post_page---byline--82c6c838c0a8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82c6c838c0a8--------------------------------) ·阅读时长9分钟·2024年5月4日

--

# 目录

1.  **介绍** *(如果你愿意，可以跳过此部分)*

1.  **设置与安装**

1.  **从** `**networkx**` **迁移到Memgraph DB**

1.  **按特征值调整节点大小**

1.  **按特征值着色边**

1.  **下一步**

# 介绍

到目前为止，我向你展示了在Python中以尽可能少的代码创建完全交互式网络可视化的最便捷方法。

现在是时候更进一步了——将图形数据库整合到我们的网络可视化中。

在本文中，我向你介绍了一种与Python兼容的图形数据库，**你可以在5分钟内完成设置。**

它将允许你获得图形数据库的所有好处，同时还包括：

+   允许你创建一个完全交互的可视化，你可以点击节点和边并查看其属性，还可以拖拽它们。

+   便于实现——…
