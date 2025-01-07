# 提升生产力：利用 AWS 生成 AI 在几秒钟内总结会议记录

> 原文：[`towardsdatascience.com/scale-your-productivity-leveraging-aws-gen-ai-to-summarize-meeting-notes-in-seconds-31f348879dc2?source=collection_archive---------11-----------------------#2024-08-06`](https://towardsdatascience.com/scale-your-productivity-leveraging-aws-gen-ai-to-summarize-meeting-notes-in-seconds-31f348879dc2?source=collection_archive---------11-----------------------#2024-08-06)

## 这是一份关于如何利用 AWS Lambda、Bedrock 和 S3 创建一个工作流来总结会议记录的全面指南

[](https://medium.com/@jaingle77?source=post_page---byline--31f348879dc2--------------------------------)![Jacob Ingle](https://medium.com/@jaingle77?source=post_page---byline--31f348879dc2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--31f348879dc2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--31f348879dc2--------------------------------) [Jacob Ingle](https://medium.com/@jaingle77?source=post_page---byline--31f348879dc2--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--31f348879dc2--------------------------------) ·阅读时长 16 分钟·2024 年 8 月 6 日

--

![](img/5562012447b5e96753696101c3ee8595.png)

图片由 DALL-E 生成

*免责声明：本文使用的会议记录完全是虚构的，仅用于说明和教育目的，并不反映任何实际的对话、事件或个人。任何与实际人物或事件的相似之处纯属巧合。*

*免责声明：本文中概述的项目涉及使用多个 Amazon Web Services (AWS)，具体包括 Amazon S3、Amazon API Gateway、AWS Lambda 和 Amazon Bedrock。虽然 AWS 提供了一个免费套餐，可以提供有限的部分服务访问，但超出这些限制或使用不包含在免费套餐中的某些服务将产生费用。在使用 AWS 之前，务必查看 Amazon S3、Amazon API Gateway、AWS Lambda、Amazon Bedrock 及任何其他 AWS 服务的定价详情，以便了解相关费用。您有责任承担在执行此项目过程中产生的所有费用。请确保清楚了解 AWS 的定价政策，并监控您的使用情况，以避免出现意外费用。*

# 引言
