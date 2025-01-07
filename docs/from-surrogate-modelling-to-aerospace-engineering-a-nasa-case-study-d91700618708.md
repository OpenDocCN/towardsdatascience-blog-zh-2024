# 从替代建模到航空航天工程：一个NASA案例研究

> 原文：[https://towardsdatascience.com/from-surrogate-modelling-to-aerospace-engineering-a-nasa-case-study-d91700618708?source=collection_archive---------3-----------------------#2024-08-15](https://towardsdatascience.com/from-surrogate-modelling-to-aerospace-engineering-a-nasa-case-study-d91700618708?source=collection_archive---------3-----------------------#2024-08-15)

## 这是替代建模如何从理论到实践，彻底改变航空航天工程领域的方式。

[](https://piero-paialunga.medium.com/?source=post_page---byline--d91700618708--------------------------------)[![Piero Paialunga](../Images/de2185596a49484698733e85114dd1ff.png)](https://piero-paialunga.medium.com/?source=post_page---byline--d91700618708--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d91700618708--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d91700618708--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--d91700618708--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d91700618708--------------------------------) ·阅读时间10分钟·2024年8月15日

--

![](../Images/558e84b4783aaed6c2c84402210b0a9b.png)

图片由作者使用DALL·E制作。

想象一下，你因为腹痛去看**医生**。想象一下，为了告诉你疼痛的原因，医生需要进行数值模拟。这个数值模拟解决了你身体的微分方程，并且近似误差非常低，几乎达到100%的准确度：你将**准确地**知道为什么会有腹痛，完全没有错误的余地（我知道这听起来很疯狂，但请耐心听我解释）。***你会使用这个数值模拟吗？*** 当然会，为什么不呢？

现在想象一下，**因为**这个数值模拟需要解决非常复杂的微分方程，为了得到计算机的响应，你需要等待**35年的GPU运行时间**。这会立即降低这种方法的吸引力。如果需要这么长时间才能得到响应，这种方法有什么用呢？当然，它可能是100%准确的，但计算成本太高。

好吧，工程师们***想到了一个解决方案***。这个解决方案被称为[**替代建模**](https://en.wikipedia.org/wiki/Surrogate_model#:~:text=A%20surrogate%20model%20is%20an,the%20outcome%20is%20used%20instead.)。顾名思义，这种方法是使用原始模拟的***替代物***…
