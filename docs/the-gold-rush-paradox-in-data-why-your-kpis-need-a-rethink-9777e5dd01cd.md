# 数据中的“淘金热悖论”：为什么你的KPI需要重新思考

> 原文：[https://towardsdatascience.com/the-gold-rush-paradox-in-data-why-your-kpis-need-a-rethink-9777e5dd01cd?source=collection_archive---------8-----------------------#2024-11-05](https://towardsdatascience.com/the-gold-rush-paradox-in-data-why-your-kpis-need-a-rethink-9777e5dd01cd?source=collection_archive---------8-----------------------#2024-11-05)

![](../Images/540a1d711a3d106c53ab78d950bf6796.png)

图片来源：[Scottsdale Mint](https://unsplash.com/@scottsdalemint?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于[Unsplash](https://unsplash.com/photos/a-group-of-gold-bars-sitting-on-top-of-a-table-IYg1hjCO5DA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

## 你做得可能没有你想象的那么好

[](https://medium.com/@hugolu87?source=post_page---byline--9777e5dd01cd--------------------------------)[![Hugo Lu](../Images/045de11463bb16ea70a816ba89118a9e.png)](https://medium.com/@hugolu87?source=post_page---byline--9777e5dd01cd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9777e5dd01cd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9777e5dd01cd--------------------------------) [Hugo Lu](https://medium.com/@hugolu87?source=post_page---byline--9777e5dd01cd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9777e5dd01cd--------------------------------) ·阅读时间：6分钟·2024年11月5日

--

不是Medium会员？在[这里](http://www.getorchestra.io/blog/the-gold-rush-paradox-in-data-why-your-kpis-need-a-rethink)免费阅读 🚀

# 引言

在过去的几年里，我在数据工程领域观察到一个有趣的悖论。

一方面，数据和人工智能被誉为[新石油](https://www.forbes.com/sites/nishatalagala/2022/03/02/data-as-the-new-oil-is-not-enough-four-principles-for-avoiding-data-fires)。数据工程社区正以[极快的速度](https://www.reddit.com/r/dataengineering/comments/1gfht9q/joined_this_subreddit_in_the_last_10m_why/)增长。部分数据工程师已经“成名”，并报道了[60万美元的年薪](https://podcasts.apple.com/au/podcast/124-meet-the-million-dollar-data-engineer-w-zach-wilson/id1547386535?i=1000666885321)。这肯定是取得了相当显著的成果…

尽管如此，数据仍然存在严重问题。在许多组织中，数据团队被视为一个[成本中心](https://www.bigeye.com/blog/transforming-your-data-team-from-cost-center-to-value-center)，许多人在[2022年](https://365datascience.com/trending/who-was-affected-by-the-2022-2023-tech-layoffs/)期间被裁员。数据重复或[模型蔓延](https://www.youtube.com/watch?v=7hdlZ6rDa5g)现象严重，[治理](https://www.park.edu/blog/the-importance-of-data-governance-in-todays-business-environment/#:~:text=The%20landscape%20of%20data%20governance,frameworks%20are%20implemented%20and%20maintained.)问题也在上升。

企业几乎无法有效利用它们的数据，行业内的高层管理人员和领导们普遍认为，目前所构建的所有数据的质量[相当低](https://govmarketnews.com/only-22-of-states-have-ai-ready-data-quality-programs-survey-finds/)，显然还未达到AI所需的标准。

这就导致了“淘金热”悖论：

> “淘金热悖论”概括了数据与AI之间的紧张关系——数据和AI被赋予的高价值（类似于现代的淘金热）与将数据真正转化为商业价值的巨大难题之间的矛盾。虽然有大量的资金和资源流入……
