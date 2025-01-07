# physipy：使Python具备单位意识

> 原文：[https://towardsdatascience.com/physipy-make-python-unit-aware-846162522889?source=collection_archive---------4-----------------------#2024-04-24](https://towardsdatascience.com/physipy-make-python-unit-aware-846162522889?source=collection_archive---------4-----------------------#2024-04-24)

## 第1部分：physipy将米和焦耳引入Python

[](https://mocquin.medium.com/?source=post_page---byline--846162522889--------------------------------)[![Yoann Mocquin](../Images/b30a0f70c56972aabd2bc0a74baa90bb.png)](https://mocquin.medium.com/?source=post_page---byline--846162522889--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--846162522889--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--846162522889--------------------------------) [Yoann Mocquin](https://mocquin.medium.com/?source=post_page---byline--846162522889--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--846162522889--------------------------------) ·阅读时长9分钟·2024年4月24日

--

你是否曾经使用Python进行工程/科学计算，却迷失或困惑于变量的单位，例如“这个值是米还是毫米？”或者你意识到在某一时刻你把电流与电阻相加了——这是不可能的？正如每个物理教师曾经说过的那样：你不能把胡萝卜和西红柿加在一起。

好的，[**physipy**](https://physipy.readthedocs.io/en/latest/)正是为了解决这些问题而存在的。

![](../Images/64c1fa6887982d4d16ccc8d8e44f661c.png)

图片由[Artturi Jalli](https://unsplash.com/@artturijalli?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **目录：**

**·** [**什么是physipy?**](#c2e8) **·** [**一步一步理解physipy**](#993c)

∘ [使用physipy计算体重指数BMI](#f986)

∘ [使用numpy数组的牛顿运动定律](#af26)

∘ [使用NumPy函数的欧姆定律](#5b07)

∘ [爱因斯坦的质量-能量等价关系，适用于常见粒子，使用favunit](#6d8b)

∘ [自由落体与内置favunit](#b84f)

∘ [使用Matplotlib绘制物体位置和速度](#bf9d)

**·** [**总结**](#f32d)

*所有图片由作者提供。*

# 什么是physipy?
