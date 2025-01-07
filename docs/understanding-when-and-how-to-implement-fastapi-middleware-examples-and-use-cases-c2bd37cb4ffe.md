# 理解何时以及如何实现 FastAPI 中间件（示例和使用案例）

> 原文：[`towardsdatascience.com/understanding-when-and-how-to-implement-fastapi-middleware-examples-and-use-cases-c2bd37cb4ffe?source=collection_archive---------1-----------------------#2024-12-25`](https://towardsdatascience.com/understanding-when-and-how-to-implement-fastapi-middleware-examples-and-use-cases-c2bd37cb4ffe?source=collection_archive---------1-----------------------#2024-12-25)

## 通过中间件增强你的 FastAPI：实用使用案例和示例

[](https://mikehuls.medium.com/?source=post_page---byline--c2bd37cb4ffe--------------------------------)![Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--c2bd37cb4ffe--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c2bd37cb4ffe--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c2bd37cb4ffe--------------------------------) [Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--c2bd37cb4ffe--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c2bd37cb4ffe--------------------------------) ·阅读时间 4 分钟·2024 年 12 月 25 日

--

![](img/83504ecc1edcb903ad4358a9edf9521b.png)

图片来自 ChatGPT

中间件位于 API 路由器与其路由之间，充当一个层，在此层中可以在请求处理之前和之后运行代码。在本文中，我们将探讨 FastAPI 中间件的两个关键使用案例，展示它是*如何*工作的以及*为什么*它很有用。让我们开始编码吧！

# A. 设置

首先，让我们创建一个简单的 API，作为我们中间件示例的基础。下面的应用程序只有一个路由：`test`，它通过在返回“OK”之前暂停几毫秒来模拟实际的工作。

```py
import random
import time

from fastapi import FastAPI
app = FastAPI(title="My API")

@app.get('/test')
def test_route() -> str:
    sleep_seconds:float = random.randrange(10, 100) / 100
    time.sleep(sleep_seconds)
    return "OK"
```

## 什么是中间件？

中间件充当传入 HTTP 请求和应用程序处理之间的过滤器。可以把它想象成机场安检：每位乘客在登机前后都必须经过安检。同样，所有 API 请求都会经过中间件：在处理之前和之后都要经过中间件。
