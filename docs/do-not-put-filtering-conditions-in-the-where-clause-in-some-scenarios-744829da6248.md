# 在某些情况下，不要将过滤条件放在“WHERE”子句中

> 原文：[`towardsdatascience.com/do-not-put-filtering-conditions-in-the-where-clause-in-some-scenarios-744829da6248?source=collection_archive---------2-----------------------#2024-02-26`](https://towardsdatascience.com/do-not-put-filtering-conditions-in-the-where-clause-in-some-scenarios-744829da6248?source=collection_archive---------2-----------------------#2024-02-26)

![](img/a26ed6bc410146dc65158abccc19dff7.png)

图片由[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2139592)的[NoName_13](https://pixabay.com/users/noname_13-2364555/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2139592)提供

## 如果我们在 LEFT JOIN ON 之后放置条件，会发生什么？

[](https://christophertao.medium.com/?source=post_page---byline--744829da6248--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--744829da6248--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--744829da6248--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--744829da6248--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--744829da6248--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--744829da6248--------------------------------) ·7 分钟阅读·2024 年 2 月 26 日

--

只要您点击进入这篇文章，我相信您应该了解 SQL。您还必须明白，在 SELECT 查询中，我们应该将条件放在 WHERE 子句中。然而，让我问你一个问题，看看你是否能立即回答。

> 如果我们在 LEFT JOIN … ON …子句中放置过滤条件会发生什么？

```py
SELECT *
FROM Employee e LEFT JOIN Department d
  ON e.dept_id = d.id
  AND e.name = 'Chris'
```

如果您对上述查询的行为不确定，或者认为它等同于以下查询，请阅读我的文章，我会告诉您它们之间的区别。

```py
SELECT *
FROM Employee e LEFT JOIN Department d
  ON e.dept_id = d.id
WHERE e.name = 'Chris'
```

# 1\. 验证结果

![](img/c636f6f625036e3463e36d30b90eb824.png)

图片由[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2139481)的[NoName_13](https://pixabay.com/users/noname_13-2364555/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2139481)提供

为了演示目的，我创建了两个带有简单虚拟数据的表如下。

## 员工表
