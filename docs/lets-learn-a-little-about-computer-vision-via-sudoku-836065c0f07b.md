# 让我们通过数独了解一下计算机视觉

> 原文：[https://towardsdatascience.com/lets-learn-a-little-about-computer-vision-via-sudoku-836065c0f07b?source=collection_archive---------2-----------------------#2024-12-14](https://towardsdatascience.com/lets-learn-a-little-about-computer-vision-via-sudoku-836065c0f07b?source=collection_archive---------2-----------------------#2024-12-14)

## 解数独是编程中的一个有趣挑战，将计算机视觉技术添加到数独解题中，可以将其与流行的机器学习技术相结合

[](https://medium.com/@broepke?source=post_page---byline--836065c0f07b--------------------------------)[![Brian Roepke](../Images/0b7ef72cbfc9acda69fde14127d65dcf.png)](https://medium.com/@broepke?source=post_page---byline--836065c0f07b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--836065c0f07b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--836065c0f07b--------------------------------) [Brian Roepke](https://medium.com/@broepke?source=post_page---byline--836065c0f07b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--836065c0f07b--------------------------------) ·阅读时间8分钟·2024年12月14日

--

![](../Images/81e3ba184f32ab3733ba55a7a898f130.png)

图片由作者使用HubSpot AI制作

# 介绍

这一切最初只是一个有趣的小实验，目的是编写另一个解谜工具，类似于我最近写的[Wordle](https://dataknowsall.com/blog/wordle.html)解答器。数独是一个非常适合计算机求解的问题。它是通过简单的迭代方式来寻找独特的解法。可能有成千上万的例子，因此虽然我会提到我最终如何解决这个谜题，但我更想关注我在这款游戏中采用的机器学习（ML）和人工智能（AI）方法。我想，为什么不将计算机视觉（CV）和光学字符识别（OCR）加入其中，让你能够上传谜题的图像，机器将读取并从那里开始解决剩下的部分。结果这是一次非常棒的学习体验，我很乐意和大家分享！

到现在为止，我们大概都熟悉数独是什么了，那么让我们深入了解如何从图像中提取数字吧！
