# 我的通勤时间超过四小时。每单程。

> 原文：[`towardsdatascience.com/my-commute-to-work-is-more-than-4-hours-each-way-fc8d7c2392ad?source=collection_archive---------1-----------------------#2024-09-27`](https://towardsdatascience.com/my-commute-to-work-is-more-than-4-hours-each-way-fc8d7c2392ad?source=collection_archive---------1-----------------------#2024-09-27)

## 我疯了吗，还是数据给出了不同的故事？

[](https://medium.com/@joparga3?source=post_page---byline--fc8d7c2392ad--------------------------------)![Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--fc8d7c2392ad--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc8d7c2392ad--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc8d7c2392ad--------------------------------) [Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--fc8d7c2392ad--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc8d7c2392ad--------------------------------) ·10 分钟阅读·2024 年 9 月 27 日

--

![](img/91d5a8896cc4cfdedadaf6f59428de2b.png)

图片由[Yusuf Mansoor](https://unsplash.com/@yusuf337?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/two-trains-parked-next-to-each-other-in-a-train-station-Sr1OtQwvVjk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

我的通勤时间超过四小时。*每单程*。这意味着我每天要花费 8 小时通勤，再加上全天的工作。有些人可能会认为我疯了，但事实是：这些数据是有道理的。因此，当我和家人分析这些数据时，我们决定不搬到靠近我办公室的城市。这就是这一决定如何展开的故事——以及数据对此的反映。

*附言 1：我与任何进行调查的公司或使用我计算通勤数据的公司没有任何关系。*

*附言 2：除非另有说明，所有图片均由我本人创作。*

# 与我的数据科学团队的对话促使我写下了这篇文章。

在分享我的通勤故事之前，我觉得有必要介绍一下我为什么开始写这篇文章。随着亚马逊最近宣布[让更多员工回到办公室](https://fortune.com/2024/09/16/andy-jassy-5-days-return-to-office-order-amazon-work-culture-concerns/)，我们数据科学和数据工程团队有些不安。科技行业是那些远程办公和混合工作形式更为普遍的行业之一。在 Skyscanner，我们的数据科学团队有 40 多位成员，分布在伦敦、爱丁堡、巴塞罗那和深圳。其他团队也有类似的情况……
