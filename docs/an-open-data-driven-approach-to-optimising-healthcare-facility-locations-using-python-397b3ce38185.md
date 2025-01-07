# 基于开放数据的优化医疗设施位置的Python方法

> 原文：[https://towardsdatascience.com/an-open-data-driven-approach-to-optimising-healthcare-facility-locations-using-python-397b3ce38185?source=collection_archive---------10-----------------------#2024-06-11](https://towardsdatascience.com/an-open-data-driven-approach-to-optimising-healthcare-facility-locations-using-python-397b3ce38185?source=collection_archive---------10-----------------------#2024-06-11)

## 使用开放数据栈的Python教程

[](https://parvathykrishnank.medium.com/?source=post_page---byline--397b3ce38185--------------------------------)[![Parvathy Krishnan](../Images/5b64648b73371b69d3a31e7fb56a9cb8.png)](https://parvathykrishnank.medium.com/?source=post_page---byline--397b3ce38185--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--397b3ce38185--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--397b3ce38185--------------------------------) [Parvathy Krishnan](https://parvathykrishnank.medium.com/?source=post_page---byline--397b3ce38185--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--397b3ce38185--------------------------------) ·阅读时间：13分钟·2024年6月11日

--

![](../Images/d246a081892d5477ad655a73bb1d8de8.png)

该图像由作者在[Midjourney](https://www.midjourney.com/app/)中生成

*这项工作是与* [*Joaquim Gromicho*](https://www.linkedin.com/in/joaquim-gromicho-a891643) *教授以及* [*Kai Kaiser*](https://medium.com/u/ea66398a2a31)*共同合作完成的。作者对所有错误和遗漏负责。*

> 根据2020年发布的关于[全球前往医疗设施的旅行时间地图](https://www.nature.com/articles/s41591-020-1059-1)的研究，43.3%（约31.6亿人）无法在一小时内步行到达医疗设施。 

准确计算前往医疗设施的旅行时间是评估医疗可达性的基础，尤其是在[可达性障碍](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5178808/)可能显著影响公共健康结果的地区。这些计算对于资源分配、医疗服务利用、公平的医疗访问以及未来设施的战略规划至关重要。然而，要进行这些计算，需要大量的数据处理，包括医院位置、人口分布以及基于道路网络数据（如OpenStreetMaps）或API（如Google或Mapbox）的旅行时间计算。

地理变异性，如不同的地形、道路状况和天气，也会影响计算结果…
