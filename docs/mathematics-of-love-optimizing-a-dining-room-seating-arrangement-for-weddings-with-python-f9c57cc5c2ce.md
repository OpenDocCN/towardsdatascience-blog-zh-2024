# 爱的数学：使用 Python 优化婚礼宴会厅座位安排

> 原文：[`towardsdatascience.com/mathematics-of-love-optimizing-a-dining-room-seating-arrangement-for-weddings-with-python-f9c57cc5c2ce?source=collection_archive---------3-----------------------#2024-09-02`](https://towardsdatascience.com/mathematics-of-love-optimizing-a-dining-room-seating-arrangement-for-weddings-with-python-f9c57cc5c2ce?source=collection_archive---------3-----------------------#2024-09-02)

## 使用数学编程和 Python 解决限制性二次多重背包问题（RQMKP）

[](https://medium.com/@luisfernandopa1212?source=post_page---byline--f9c57cc5c2ce--------------------------------)![Luis Fernando PÉREZ ARMAS, Ph.D.](https://medium.com/@luisfernandopa1212?source=post_page---byline--f9c57cc5c2ce--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f9c57cc5c2ce--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9c57cc5c2ce--------------------------------) [Luis Fernando PÉREZ ARMAS, Ph.D.](https://medium.com/@luisfernandopa1212?source=post_page---byline--f9c57cc5c2ce--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9c57cc5c2ce--------------------------------) ·16 分钟阅读·2024 年 9 月 2 日

--

![](img/b5fc001e7e65f793b533ae5d6dc12713.png)

请跟随我来到屋顶接待会……（图像由 DALLE-3 生成）

[Hannah Fry](https://medium.com/u/eb923e7ddfb0?source=post_page---user_mention--f9c57cc5c2ce--------------------------------)博士的书，*《爱的数学》* [1]*，是一本难得的好书，既聪明又幽默，而且非常容易阅读。我非常喜欢，以至于把书送给了我的一些亲密朋友。他们也很喜欢，但其中有几个在第八章遇到了难题，而讽刺的是，那一章正是他们最期待的章节。

本章深入探讨了组织婚礼座位安排的棘手问题，使用数学编程和优化方法来确定如何安排座位，使每位宾客都能度过一个尽可能愉快的时光。听起来很酷，对吧？但这里有个问题，本书实际上并没有教你如何设置或解决这个问题，这让我的朋友们感到有些迷茫。

现在，我知道你们在想什么：婚礼座位安排？真的吗？但别被愚弄了，这其实是一个非常难以解决的问题，而且它的解决方案不仅限于婚礼（尽管这个问题已经很重要）。想象一下，在游轮上安排餐桌[3]、组建运动队或工作小组、优化投资组合，甚至在我作为商学院教授的世界里……
