# promptrefiner：使用GPT-4为你的本地LLM创建完美的系统提示

> 原文：[https://towardsdatascience.com/promptrefiner-using-gpt-4-to-create-perfect-system-prompt-8e2f1b6cb758?source=collection_archive---------7-----------------------#2024-04-09](https://towardsdatascience.com/promptrefiner-using-gpt-4-to-create-perfect-system-prompt-8e2f1b6cb758?source=collection_archive---------7-----------------------#2024-04-09)

[](https://medium.com/@amirarsalan.rajabi?source=post_page---byline--8e2f1b6cb758--------------------------------)[![Amirarsalan Rajabi](../Images/a75915c2c393266b049a797b191e28ca.png)](https://medium.com/@amirarsalan.rajabi?source=post_page---byline--8e2f1b6cb758--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8e2f1b6cb758--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8e2f1b6cb758--------------------------------) [Amirarsalan Rajabi](https://medium.com/@amirarsalan.rajabi?source=post_page---byline--8e2f1b6cb758--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8e2f1b6cb758--------------------------------) ·9分钟阅读·2024年4月9日

--

![](../Images/0d710c44dad39f0f9ea50f94f2b489ae.png)

图片由DALL·E 3创建

在本教程中，我们将探索`promptrefiner`：一个我创建的小型Python工具，通过使用GPT-4模型的帮助，为你的本地LLM创建完美的系统提示。

本文中的Python代码可以在这里获取：

[https://github.com/amirarsalan90/promptrefiner.git](https://github.com/amirarsalan90/promptrefiner.git)

为你的程序创建一个有效且详细的系统提示是一个具有挑战性的过程，通常需要多次试验和错误，特别是在使用较小的LLM时，比如7b语言模型，它通常能够解释并跟随较不详细的提示。像Mistral 7b这样的较小的大型语言模型会对你的系统提示更加敏感。

让我们假设一个场景，你正在处理一段文本。这段文本讨论了一些个人，讨论他们的贡献或角色。现在，你希望让本地语言模型，例如Mistral 7b，将这些信息提炼成一组Python字符串，每个字符串将一个名字与其在文本中的相关细节配对。以下段落是一个例子：

![](../Images/720c6e508343b840d0583dcd5b9e3add.png)

输入文本的截图。图片由作者创建
