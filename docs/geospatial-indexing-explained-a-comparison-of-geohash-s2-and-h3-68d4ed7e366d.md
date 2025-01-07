# 地理空间索引解释：Geohash、S2 与 H3 的比较

> 原文：[`towardsdatascience.com/geospatial-indexing-explained-a-comparison-of-geohash-s2-and-h3-68d4ed7e366d?source=collection_archive---------1-----------------------#2024-01-01`](https://towardsdatascience.com/geospatial-indexing-explained-a-comparison-of-geohash-s2-and-h3-68d4ed7e366d?source=collection_archive---------1-----------------------#2024-01-01)

[](https://medium.com/@benfeifke?source=post_page---byline--68d4ed7e366d--------------------------------)![Ben Feifke](https://medium.com/@benfeifke?source=post_page---byline--68d4ed7e366d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--68d4ed7e366d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--68d4ed7e366d--------------------------------) [Ben Feifke](https://medium.com/@benfeifke?source=post_page---byline--68d4ed7e366d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--68d4ed7e366d--------------------------------) ·12 分钟阅读·2024 年 1 月 1 日

--

地理空间索引，或称地理编码，是将经纬度对索引到地理空间的小细分单元的过程，这是我们数据科学家在面对地理空间数据时经常使用的技术。

尽管首个流行的地理空间索引技术“Geohash”直到 2008 年才被发明，但将经纬度对索引到可管理的空间子细分并非一个新概念。几百年来，政府一直在将土地划分为州、省、县和邮政编码等区域，用于各种应用，如人口普查和选举投票的汇总。

与政府使用的手动技术不同，我们数据科学家使用现代计算技术来执行这种空间细分，并且是为了我们自己的目的：分析、特征工程、按地理细分的精细化 AB 测试、地理空间数据库的索引等。

地理空间索引是计算机科学的一个成熟领域，地理空间索引工具可以为我们的模型和分析带来强大的功能和丰富的内涵。让地理空间索引技术更加令人兴奋的是，深入了解它们的“引擎盖”下，揭示了其他数学工具的多样结合，例如空间填充曲线、地图投影、镶嵌图等！

本文将探讨今天最流行的三种地理空间索引工具——它们的起源……
