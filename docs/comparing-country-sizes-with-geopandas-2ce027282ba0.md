# 使用GeoPandas比较国家大小

> 原文：[https://towardsdatascience.com/comparing-country-sizes-with-geopandas-2ce027282ba0?source=collection_archive---------4-----------------------#2024-06-02](https://towardsdatascience.com/comparing-country-sizes-with-geopandas-2ce027282ba0?source=collection_archive---------4-----------------------#2024-06-02)

## 快速成功数据科学

## 如何投影、移动和旋转地理空间数据

[](https://medium.com/@lee_vaughan?source=post_page---byline--2ce027282ba0--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--2ce027282ba0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2ce027282ba0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2ce027282ba0--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--2ce027282ba0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2ce027282ba0--------------------------------) ·12分钟阅读·2024年6月2日

--

![](../Images/1c68c3ac6e020281bbb1ae46bf679d01.png)

我住在德州（耶哈！），我们喜欢将德州与其他州、国家、岛屿、小行星等等进行比较。我们甚至会把德州与德州自己进行比较：

![](../Images/1a620aa0df32a83194c83cacb92386d5.png)

没错，伙计们，德州比德州大！（作者）

我喜欢将一个地理区域的大小与另一个区域进行比较的梗图。尽管我对地理学有相当的了解，但我常常会感到惊讶。例如，澳大利比巴西大吗？新西兰比意大利小吗？德州和德国、印度、智利相比如何？以下是答案：

![](../Images/01a628740a8069af7966e3b06ea6d78a.png)

巴西略大于澳大利亚（作者）

![](../Images/a3c15cb9dd8e2172cfc7b34abd51f43c.png)

意大利略大于新西兰（作者）

![](../Images/b0d8e1a11243e6d61a9cca025cde2b5c.png)

德州比德国大（作者）
