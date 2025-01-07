# 人工智能映射：使用神经网络识别房屋号码

> 原文：[`towardsdatascience.com/ai-mapping-using-neural-networks-to-identify-house-numbers-12f194a95d75?source=collection_archive---------12-----------------------#2024-04-16`](https://towardsdatascience.com/ai-mapping-using-neural-networks-to-identify-house-numbers-12f194a95d75?source=collection_archive---------12-----------------------#2024-04-16)

## 比较人工神经网络和卷积神经网络在分类 Google 街景房屋号码中的表现

[](https://medium.com/@john_lenehan?source=post_page---byline--12f194a95d75--------------------------------)![John Lenehan](https://medium.com/@john_lenehan?source=post_page---byline--12f194a95d75--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--12f194a95d75--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--12f194a95d75--------------------------------) [John Lenehan](https://medium.com/@john_lenehan?source=post_page---byline--12f194a95d75--------------------------------)

·发表于[面向数据科学](https://towardsdatascience.com/?source=post_page---byline--12f194a95d75--------------------------------) ·阅读时间 7 分钟·2024 年 4 月 16 日

--

![](img/0895d5087b437859b91d9633506b7987.png)

图片来自[christian koch](https://unsplash.com/@xt1an?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的分享

# 介绍

深度学习中最有趣的任务之一是识别自然场景中的物体。通过机器学习算法解读视觉数据具有重要的实际价值，这在广泛的应用中都有体现（从自动驾驶车辆到人脸识别）。一个这样的例子是在地图上根据房屋号码定位房屋。

[Google 街景房屋号码数据集](https://www.kaggle.com/datasets/stanfordu/street-view-house-numbers)包含超过 60 万个从街景照片中提取的标记数字——它是最受欢迎的图像识别数据集之一。谷歌利用该数据集中的图像，通过神经网络自动提取地址号码，从而提升地图准确性。这些模型的输出与已知街道地址相结合，有助于在 Google Maps 中精准定位地址。

![](img/b17070dcbb4bc198fba5571830ec0de5.png)

图片来自[Alex Skobe](https://unsplash.com/@alex_skobe?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的分享
