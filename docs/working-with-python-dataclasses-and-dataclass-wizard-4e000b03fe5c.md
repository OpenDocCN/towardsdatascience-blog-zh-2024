# 使用Python的Dataclasses和Dataclass Wizard

> 原文：[https://towardsdatascience.com/working-with-python-dataclasses-and-dataclass-wizard-4e000b03fe5c?source=collection_archive---------1-----------------------#2024-02-26](https://towardsdatascience.com/working-with-python-dataclasses-and-dataclass-wizard-4e000b03fe5c?source=collection_archive---------1-----------------------#2024-02-26)

## 让我们用几行代码来创建Python数据对象吧！

[](https://medium.com/@jodhernandezbemj?source=post_page---byline--4e000b03fe5c--------------------------------)[![Jose D. Hernandez-Betancur](../Images/fc2be8064501a06c2e363f6fb7d93be7.png)](https://medium.com/@jodhernandezbemj?source=post_page---byline--4e000b03fe5c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4e000b03fe5c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4e000b03fe5c--------------------------------) [Jose D. Hernandez-Betancur](https://medium.com/@jodhernandezbemj?source=post_page---byline--4e000b03fe5c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4e000b03fe5c--------------------------------) ·阅读时间10分钟·2024年2月26日

--

![](../Images/cf81445a041a00083618f5b388c6ab93.png)

图片由作者使用Gencraft生成。

如果你是Python开发者，你可能已经听说过Zen。它的19条准则中有三条指出：“显式优于隐式”，“可读性很重要”，以及“简单优于复杂”。当你在创建或集成现有的Python包时，你的目标是找到最符合Python风格的方式来高效、功能性地完成任务。Python的[dataclasses](https://docs.python.org/3/library/dataclasses.html)库提供了一种快速而简便的创建对象的方式。这个包包含了一系列工具，能够加速并使你的代码更具可读性，无论你是在进行数据科学还是软件开发项目。然而，既然没有巫师就没有魔法棒，[dataclass wizard](https://dataclass-wizard.readthedocs.io/en/latest/overview.html)包为*dataclasses*提供了额外的功能，能够以Pythonic的风格增强你的代码。在这篇文章中，我们将深入探讨这两个包，帮助我们将工作提升到一个新的层次。

# Dataclasses包

要使用*dataclasses*，我们需要导入并应用`@dataclass`装饰器。这个装饰器让我们能够指定实例是否应该被冻结（`frozen=True`）、是否需要初始化（`init=True`）或使用插槽（`slots=True`）。此外，虽然`field`对象在创建*dataclasses*对象时不是必需的，但我们可以使用它来…
