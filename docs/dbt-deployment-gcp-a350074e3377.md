# 在 Google Cloud 上大规模部署 dbt 项目

> 原文：[`towardsdatascience.com/dbt-deployment-gcp-a350074e3377?source=collection_archive---------3-----------------------#2024-07-29`](https://towardsdatascience.com/dbt-deployment-gcp-a350074e3377?source=collection_archive---------3-----------------------#2024-07-29)

## 使用 Artifact Registry、Cloud Composer、GitHub Actions 和 dbt-airflow 容器化并运行 dbt 项目

[](https://gmyrianthous.medium.com/?source=post_page---byline--a350074e3377--------------------------------)![Giorgos Myrianthous](https://gmyrianthous.medium.com/?source=post_page---byline--a350074e3377--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a350074e3377--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a350074e3377--------------------------------) [Giorgos Myrianthous](https://gmyrianthous.medium.com/?source=post_page---byline--a350074e3377--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a350074e3377--------------------------------) ·阅读时间 11 分钟·2024 年 7 月 29 日

--

![](img/abb51128060c153a45de8f3dcb7a5f56.png)

图片由 [Photo Boards](https://unsplash.com/@createandbloom?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源 [Unsplash](https://unsplash.com/photos/an-orange-and-blue-wall-with-peeling-paint-OiV3i01sLqE?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

管理大规模的数据模型是数据团队使用 [dbt（数据构建工具）](https://www.getdbt.com/) 常见的挑战。最初，团队通常从易于管理和部署的简单模型开始。然而，随着**数据量的增加**和**业务需求**的**发展**，这些模型的**复杂性**也在**增加**。

这种发展通常会导致一个**单体**的**仓库**，所有依赖关系交织在一起，使得不同团队之间的**协作**变得**困难**。为了解决这个问题，数据团队可能会发现将数据模型分布到多个 dbt 项目中是有益的。这种方法不仅促进了**更好的组织和模块化**，还增强了整个数据基础设施的可扩展性和可维护性。

由处理多个 dbt 项目引入的一个重要复杂性是它们的执行和部署方式。管理库依赖关系变得至关重要，尤其是当不同项目需要不同版本的 dbt 时。虽然 dbt Cloud 提供了一个强大的解决方案，用于调度和执行多仓库 dbt 项目，但它也需要巨大的投资，并非每个组织都能承受或找到合适的资源来使用……
