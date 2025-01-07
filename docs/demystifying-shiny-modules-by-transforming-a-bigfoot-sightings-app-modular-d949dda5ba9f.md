# 通过大脚怪目击应用程序转型揭开 R Shiny 模块的神秘面纱

> 原文：[https://towardsdatascience.com/demystifying-shiny-modules-by-transforming-a-bigfoot-sightings-app-modular-d949dda5ba9f?source=collection_archive---------10-----------------------#2024-05-03](https://towardsdatascience.com/demystifying-shiny-modules-by-transforming-a-bigfoot-sightings-app-modular-d949dda5ba9f?source=collection_archive---------10-----------------------#2024-05-03)

## 深入学习如何使用模块构建 Shiny 应用程序的指南

[](https://medium.com/@menghani.deepsha?source=post_page---byline--d949dda5ba9f--------------------------------)[![Deepsha Menghani](../Images/56a6ed8597c36e8c76d8a29a449325a4.png)](https://medium.com/@menghani.deepsha?source=post_page---byline--d949dda5ba9f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d949dda5ba9f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d949dda5ba9f--------------------------------) [Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--d949dda5ba9f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d949dda5ba9f--------------------------------) ·9 分钟阅读·2024年5月3日

--

![](../Images/3528c96646cbc51862cb2a7418cf6b86.png)

图片由 [Luke Chesser](https://unsplash.com/@lukechesser?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/graphs-of-performance-analytics-on-a-laptop-screen-JKUTrJ4vK00?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

当我多年前发现 Shiny 时，我立刻就被吸引了。[Shiny](https://shiny.posit.co/) 是一个 R 包，用于构建可以在后台运行 R 代码的互动 web 应用程序。我被它所提供的能力所吸引，它使我能够快速创建数据分析和建模原型，并与我的利益相关者分享，以便让他们参与其中。一个这样的例子是我创建的[异常检测仪表盘](https://hzon6a-deepsha-menghani.shinyapps.io/Sales_forecast_anomalies/)，它帮助我的利益相关者理解我对分析方法的理解，并在我们将该方法投入生产之前，带领他们一起走过这一过程。

在这篇文章中，我将带你了解如何使用模块化方法学习构建 Shiny，尤其是如果你之前以单体方式学习过的话。我将通过一个简单的大脚怪目击应用程序进行讲解，应用程序基于一个合成数据集，用户可以选择一个州，并查看该州大脚怪目击最多的前十个县以及目击的时间变化。这个商业场景是什么呢？好吧，我是一个大脚怪爱好者，我想知道我应该搬到哪个县生活，因此这是一个非常关键的仪表盘。
