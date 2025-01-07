# 增强数据科学工作流：掌握 Jupyter Notebook 的版本控制

> 原文：[https://towardsdatascience.com/enhancing-data-science-workflows-mastering-version-control-for-jupyter-notebooks-b03c839e25ec?source=collection_archive---------3-----------------------#2024-01-11](https://towardsdatascience.com/enhancing-data-science-workflows-mastering-version-control-for-jupyter-notebooks-b03c839e25ec?source=collection_archive---------3-----------------------#2024-01-11)

## 一份实践指南，帮助通过 Jupytext、nbstripout 和 nbconvert 实现协作与可复现性

[](https://medium.com/@le_Tomassini?source=post_page---byline--b03c839e25ec--------------------------------)[![Alessandro Tomassini](../Images/e5bf527fafd933239bff6b87005ba457.png)](https://medium.com/@le_Tomassini?source=post_page---byline--b03c839e25ec--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b03c839e25ec--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b03c839e25ec--------------------------------) [Alessandro Tomassini](https://medium.com/@le_Tomassini?source=post_page---byline--b03c839e25ec--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b03c839e25ec--------------------------------) ·8分钟阅读·2024年1月11日

--

![](../Images/a4d0c2b233241726f6bb8978d78ffd82.png)

图像由 DALL-E 生成

对于数据科学家来说，使用版本控制系统有效管理 Jupyter notebooks 是至关重要的。这不仅是为了保持工作流的有序，还为了确保结果的可复现性，并促进团队成员之间的协作。在本指南中，我们将探索三个关键工具——Jupytext、nbstripout 和 nbconvert——每个工具都有其独特的特点。我将提供详细的描述、实用示例，以及一个（希望是）平衡的观点，帮助你确定最适合你特定需求的 notebook 版本控制工具。

# 理解 Jupyter notebooks 和版本控制的挑战

Jupyter notebooks 虽然非常适合进行探索性数据分析和可视化，但在版本控制方面却带来了挑战。这主要是因为这些 notebooks 不仅仅是普通的文本文件，而是以 JSON 文档的形式进行结构化。尽管这种格式非常适合维持代码、文本和输出数据之间复杂的相互关系，但在处理版本控制时却面临诸多挑战……
