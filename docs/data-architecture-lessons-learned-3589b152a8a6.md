# 数据架构：经验教训

> 原文：[https://towardsdatascience.com/data-architecture-lessons-learned-3589b152a8a6?source=collection_archive---------3-----------------------#2024-10-04](https://towardsdatascience.com/data-architecture-lessons-learned-3589b152a8a6?source=collection_archive---------3-----------------------#2024-10-04)

## 作为数据工程师和架构师，我在这段旅程中学到的三条重要经验

[](https://medium.com/@bernd.wessely?source=post_page---byline--3589b152a8a6--------------------------------)[![Bernd Wessely](../Images/e60e01c19412d8af8f8bddf78e561275.png)](https://medium.com/@bernd.wessely?source=post_page---byline--3589b152a8a6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3589b152a8a6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3589b152a8a6--------------------------------) [Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--3589b152a8a6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3589b152a8a6--------------------------------) ·阅读时间10分钟·2024年10月4日

--

![](../Images/d9963329f4e6663eb095bcd5f9e3b17c.png)

图片由[Michael SKOPAL](https://unsplash.com/@michael_skopal?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

仅仅过了几个月，我再次不得不经历我有时所说的“IT的自我满足”。

这听起来可能有些严厉，但不幸的是，**我一次又一次地经历了**这种情况。当看到**IT部门实际上与业务背道而驰**时，确实令人沮丧。

我记得有一个具体的案例，**一个正在运行的业务解决方案**必须**迁移**到另一个执行平台，仅仅是**因为‘技术’原因**。当然，业务部门被告知这个目标平台在维护上会便宜很多，但IT部门没有提供任何实质性的证据来支持这一说法。最终，迁移的决定是由**‘专家知识’和所谓的‘最佳实践’**推动的，但这完全是从IT中心的视角出发。迁移原本正常运行的系统花费了大量资金，最终发现**承诺的成本节约并未实现**，更糟糕的是，某些业务功能甚至退化了。

**IT专业人员**，不仅在特定的技术导向型公司中，**往往认为技术、IT工具，甚至如今的数据本身就是目的**。

没有什么比这更远离事实了。
