# 将你的 Python 函数通过一行代码变成装饰器

> 原文：[`towardsdatascience.com/turn-your-python-function-into-a-decorator-with-one-line-of-code-1ebd738f31c0?source=collection_archive---------0-----------------------#2024-05-23`](https://towardsdatascience.com/turn-your-python-function-into-a-decorator-with-one-line-of-code-1ebd738f31c0?source=collection_archive---------0-----------------------#2024-05-23)

## 一种编写更简洁、更清晰、更易读的 Python 装饰器的新方法，同时也充当上下文管理器

[](https://mikehuls.medium.com/?source=post_page---byline--1ebd738f31c0--------------------------------)![Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--1ebd738f31c0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1ebd738f31c0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ebd738f31c0--------------------------------) [Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--1ebd738f31c0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ebd738f31c0--------------------------------) ·阅读时间 4 分钟 ·2024 年 5 月 23 日

--

![](img/819b3ce51931583081a67238f9efb95b.png)

装饰器简化（图片由 [Tobias Bjørkli](https://www.pexels.com/@tobiasbjorkli/) 提供，来自 [Pexels](https://www.pexels.com/photo/multicolored-graffiti-2119706/)）

想写一个装饰器，却记不住语法吗？装饰器的语法相当复杂，涉及许多样板代码。在这篇文章中，我们将展示一种更简单的编写装饰器的方法。这种新方法会简短、清晰、易读。让我们开始编码吧！

# 创建装饰器的默认方式

下面的代码是创建装饰器的默认方式。它会记录被装饰函数运行的时间。查看[这篇文章](https://mikehuls.medium.com/six-levels-of-python-decorators-1f12c9067b23)，深入了解装饰器。

```py
def timer(name:str) -> Callable:
    def decorator(func:Callable) -> Callable:
        @wraps(func)
        def decorator_implementation(*args, **kwargs):
            try:
                print(f"TIMER:   {name} start")
                strt = time.perf_counter()
                return func(*args, **kwargs)
            finally:
                print(f"TIMER:   {name} finished in {time.perf_counter() - strt}")
        return decorator_implementation
    return decorator
```

这样我们就可以像这样使用我们的代码：

```py
@timer(name="test")
def…
```
