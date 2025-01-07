# PAGA 解释：单细胞数据的图形抽象

> 原文：[https://towardsdatascience.com/paga-explained-graphical-abstractions-of-single-cell-data-23d3fc76fc3e?source=collection_archive---------7-----------------------#2024-06-06](https://towardsdatascience.com/paga-explained-graphical-abstractions-of-single-cell-data-23d3fc76fc3e?source=collection_archive---------7-----------------------#2024-06-06)

## 如何从更广阔的视角看待数据，进而获得对其深层含义的洞察

[](https://medium.com/@jashahir?source=post_page---byline--23d3fc76fc3e--------------------------------)[![Jamshaid Shahir博士](../Images/3f57e94cb3987f09667326df0cb66b5d.png)](https://medium.com/@jashahir?source=post_page---byline--23d3fc76fc3e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--23d3fc76fc3e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--23d3fc76fc3e--------------------------------) [Jamshaid Shahir博士](https://medium.com/@jashahir?source=post_page---byline--23d3fc76fc3e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--23d3fc76fc3e--------------------------------) ·阅读时长6分钟·2024年6月6日

--

![](../Images/c9438e0f6ff1126cd16e370f5bca2382.png)

图片来源：[Clint Adair](https://unsplash.com/@clintadair?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在[Unsplash](https://unsplash.com/photos/green-and-black-rope-BW0vK-FA3eg?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在单细胞基因组学数据中，我们对个体细胞的数万个特征进行分析，无论是在基因表达、蛋白质表达，还是其他全基因组测量模式下，我们通常都会努力从数据中提取出高层次的总结。这可以通过多种方式实现，例如差异基因表达，我们通过对细胞群体进行统计检验，比较不同群体之间哪些基因在统计上具有显著性；或者数据可视化，我们将由多个特征测量所带来的高维数据压缩成2或3个维度，从而帮助我们理解数据。

理解单细胞数据的一种有效方式是将其转化为图——即一组由节点和边组成的元组。在这种情况下，节点代表细胞，边则定义了细胞之间的连接。这种基于元组的数据结构提供了一种灵活的方式来将细胞分组，探索不同类型细胞之间的关系（例如，病变细胞与正常细胞的区别，不同发展阶段的细胞），以及可视化数据集的整体结构。
