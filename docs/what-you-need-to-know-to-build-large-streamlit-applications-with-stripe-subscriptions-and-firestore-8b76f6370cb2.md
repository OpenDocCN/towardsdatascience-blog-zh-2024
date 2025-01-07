# 构建具有 Stripe 订阅和 Firestore 集成的大型 Streamlit 应用程序所需了解的内容。

> 原文：[`towardsdatascience.com/what-you-need-to-know-to-build-large-streamlit-applications-with-stripe-subscriptions-and-firestore-8b76f6370cb2?source=collection_archive---------2-----------------------#2024-08-02`](https://towardsdatascience.com/what-you-need-to-know-to-build-large-streamlit-applications-with-stripe-subscriptions-and-firestore-8b76f6370cb2?source=collection_archive---------2-----------------------#2024-08-02)

## 将想法转化为软件产品的能力是一项很棒的技能。在这篇博客中，我将描述需要什么以及如何将各部分组合在一起创建一个软件产品，而无需开始成本，但具有订阅模式和 Firestore 集成。

[](https://erdogant.medium.com/?source=post_page---byline--8b76f6370cb2--------------------------------)![Erdogan Taskesen](https://erdogant.medium.com/?source=post_page---byline--8b76f6370cb2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8b76f6370cb2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b76f6370cb2--------------------------------) [Erdogan Taskesen](https://erdogant.medium.com/?source=post_page---byline--8b76f6370cb2--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b76f6370cb2--------------------------------)·阅读 15 分钟·2024 年 8 月 2 日

--

![](img/2ce9e54430dbffa4e81cbdbc32d794d8.png)

照片由[Shane Aldendorff](https://unsplash.com/@pluyar?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)在[Unsplash](https://unsplash.com/photos/gray-and-black-metal-electronic-device-lot-3AzL-IR3v7Y?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)上拍摄。

无论您是数据科学家、数据工程师还是其他软件开发领域的人员，仅使用笔记本电脑将您的想法转化为真正的工作软件产品可能是最重要的技能。各种软件开发领域在这个过程中汇聚在一起，从 UX、前端到后端开发、数据处理、可视化、云/服务器配置等等。这是一个来回的过程。*挑战在于决定从哪个想法开始，并且如何避免（开始）成本，直到人们想要您的产品为止。* 在这篇博客中，我将讨论不同类型的想法，并展示我如何使用*Streamlit*创建和部署***SkyWalk***，使用*Stripe*进行订阅，以及使用*Google Firestore*进行数据存储。

# 构建一个大型 Streamlit…
