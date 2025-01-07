# 基于哈希的替代键碰撞风险

> 原文：[https://towardsdatascience.com/collision-risk-in-hash-based-surrogate-keys-4c87b716cbcd?source=collection_archive---------10-----------------------#2024-11-20](https://towardsdatascience.com/collision-risk-in-hash-based-surrogate-keys-4c87b716cbcd?source=collection_archive---------10-----------------------#2024-11-20)

## 分析使用MD5、SHA-1和SHA-256算法计算替代键时哈希碰撞发生的各种方面及其现实生活类比。

[](https://medium.com/@krzysztof.kornel?source=post_page---byline--4c87b716cbcd--------------------------------)[![Krzysztof K. Zdeb](../Images/4531b37707bf6a01ef635e4b9ecfc03f.png)](https://medium.com/@krzysztof.kornel?source=post_page---byline--4c87b716cbcd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4c87b716cbcd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4c87b716cbcd--------------------------------) [Krzysztof K. Zdeb](https://medium.com/@krzysztof.kornel?source=post_page---byline--4c87b716cbcd--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c87b716cbcd--------------------------------) ·12分钟阅读·2024年11月20日

--

![](../Images/6e1a1702cee57ac9923c534412b1c73b.png)

低概率事件在多维空间中的抽象可视化。不要被奇怪的骰子迷惑！| 图片来自DALL·E

在数据库中生成替代键（特别是在数据仓库和湖仓中）的一种方法依赖于哈希函数，通过哈希函数从自然键计算出哈希键。这种方法有许多优点，但也存在显著的风险：哈希函数无法保证唯一输出，从而导致**哈希键碰撞**的可能性。

哈希碰撞是指不同的输入值经过哈希函数处理后返回相同的输出值（即相同的哈希）。这种事件发生的概率主要取决于所使用的哈希函数生成的哈希键的长度。哈希键越长，碰撞的风险越低。

# 哈希函数

目前使用最广泛的三种哈希函数是：

+   **MD5**（消息摘要算法5）— 由罗纳德·里维斯特于1991年开发，是一种广为人知的哈希函数，产生一个128位（16字节）的哈希。最初设计用于数据完整性和身份验证，MD5由于其简便性和速度而迅速流行开来。
