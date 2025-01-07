# 我是如何使用Streamlit为我的学生创建类似Kaggle的平台，以及你也可以如何做到这一点

> 原文：[https://towardsdatascience.com/how-i-created-a-kaggle-like-platform-for-my-students-using-streamlit-and-how-you-can-do-it-as-well-5fd10671f559?source=collection_archive---------4-----------------------#2024-06-20](https://towardsdatascience.com/how-i-created-a-kaggle-like-platform-for-my-students-using-streamlit-and-how-you-can-do-it-as-well-5fd10671f559?source=collection_archive---------4-----------------------#2024-06-20)

## 使用Streamlit和Google Sheets将机器学习学生项目游戏化

[](https://medium.com/@luisfernandopa1212?source=post_page---byline--5fd10671f559--------------------------------)[![Luis Fernando PÉREZ ARMAS, Ph.D.](../Images/ca1b4775c2b278769c38542ea989ac92.png)](https://medium.com/@luisfernandopa1212?source=post_page---byline--5fd10671f559--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5fd10671f559--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5fd10671f559--------------------------------) [Luis Fernando PÉREZ ARMAS, Ph.D.](https://medium.com/@luisfernandopa1212?source=post_page---byline--5fd10671f559--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5fd10671f559--------------------------------) ·24分钟阅读·2024年6月20日

--

![](../Images/c7b5638ea2168f3466367312a06475a7.png)

排行榜和积分可以成为一种很好的动力来源（图片由DALLE-3生成）

我喜欢Kaggle，并且相信它在传播数据科学和机器学习方面的贡献是无价的。作为一个视频游戏和游戏化的爱好者，我非常欣赏Kaggle的排名和积分系统，它鼓励参与者通过健康的、建设性的竞争来提升自己的模型。Kaggle已经变得如此受欢迎，以至于许多讲师将Kaggle作为他们教授机器学习时的首选工具。

然而，作为一名商学院讲师，我教授一些机器学习课程，我发现将Kaggle作为评估学生最终机器学习项目的工具有些繁琐。首先，跟踪学生的提交工作是繁重且手动的，而且我的学生（请注意，他们大多数是数据科学和编程的初学者）经常会感到沮丧，因为他们的努力成果排名总是排在Kaggle排行榜的底部。尽管如此，我认为有必要承认Kaggle并非为教学工具而设计，这也解释了它在这一背景下的局限性。
