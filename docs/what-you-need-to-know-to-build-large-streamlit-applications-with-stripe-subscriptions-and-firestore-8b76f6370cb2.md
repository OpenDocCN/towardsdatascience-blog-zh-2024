# 构建具有Stripe订阅和Firestore集成的大型Streamlit应用程序所需了解的内容。

> 原文：[https://towardsdatascience.com/what-you-need-to-know-to-build-large-streamlit-applications-with-stripe-subscriptions-and-firestore-8b76f6370cb2?source=collection_archive---------2-----------------------#2024-08-02](https://towardsdatascience.com/what-you-need-to-know-to-build-large-streamlit-applications-with-stripe-subscriptions-and-firestore-8b76f6370cb2?source=collection_archive---------2-----------------------#2024-08-02)

## 将想法转化为软件产品的能力是一项很棒的技能。在这篇博客中，我将描述需要什么以及如何将各部分组合在一起创建一个软件产品，而无需开始成本，但具有订阅模式和Firestore集成。

[](https://erdogant.medium.com/?source=post_page---byline--8b76f6370cb2--------------------------------)[![Erdogan Taskesen](../Images/f6b52ed5acb60bb16d42257a3a1232de.png)](https://erdogant.medium.com/?source=post_page---byline--8b76f6370cb2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8b76f6370cb2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8b76f6370cb2--------------------------------) [Erdogan Taskesen](https://erdogant.medium.com/?source=post_page---byline--8b76f6370cb2--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b76f6370cb2--------------------------------)·阅读15分钟·2024年8月2日

--

![](../Images/2ce9e54430dbffa4e81cbdbc32d794d8.png)

照片由[Shane Aldendorff](https://unsplash.com/@pluyar?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)在[Unsplash](https://unsplash.com/photos/gray-and-black-metal-electronic-device-lot-3AzL-IR3v7Y?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)上拍摄。

无论您是数据科学家、数据工程师还是其他软件开发领域的人员，仅使用笔记本电脑将您的想法转化为真正的工作软件产品可能是最重要的技能。各种软件开发领域在这个过程中汇聚在一起，从UX、前端到后端开发、数据处理、可视化、云/服务器配置等等。这是一个来回的过程。*挑战在于决定从哪个想法开始，并且如何避免（开始）成本，直到人们想要您的产品为止。* 在这篇博客中，我将讨论不同类型的想法，并展示我如何使用*Streamlit*创建和部署***SkyWalk***，使用*Stripe*进行订阅，以及使用*Google Firestore*进行数据存储。

# 构建一个大型Streamlit…
