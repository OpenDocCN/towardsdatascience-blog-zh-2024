# 访问之战：克服（无意的）数据监狱

> 原文：[https://towardsdatascience.com/overcoming-unintended-data-jails-9051c78e29f3?source=collection_archive---------5-----------------------#2024-06-17](https://towardsdatascience.com/overcoming-unintended-data-jails-9051c78e29f3?source=collection_archive---------5-----------------------#2024-06-17)

## 即使你能看到数据，它也可能完全无用。

[](https://medium.com/@chris.lydick?source=post_page---byline--9051c78e29f3--------------------------------)[![Chris Lydick](../Images/dbb5f2102f618f38061ae8f4e0f31a8a.png)](https://medium.com/@chris.lydick?source=post_page---byline--9051c78e29f3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9051c78e29f3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9051c78e29f3--------------------------------) [Chris Lydick](https://medium.com/@chris.lydick?source=post_page---byline--9051c78e29f3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9051c78e29f3--------------------------------) ·5分钟阅读·2024年6月17日

--

![](../Images/df27892ed1d28591395777d804055ffc.png)

感谢ChatGPT 4o对数据监狱图像的解释，接下来我会更好地定义这个概念……

> 更好的数据胜过巧妙的算法，但更多的数据胜过更好的数据。
> 
> — 彼得·诺维格

我[做了一个东西](https://sps-budget-analysis-2023.streamlit.app/)。这很有趣，我认为它带来了（或者希望它将带来）价值。但它也付出了[代价](https://github.com/chrislydick/sps-budget-analysis/blob/main/elementary_school_data_extract.ipynb)，这是我在我的行业中变得非常熟悉的代价。数据难以访问不应该是（而且不必是）常态。*我把这个称为* ***数据监狱***。* 数据很容易输入，但很难取出。而且在许多情况下，数据监狱的“铁栏”是透明的。你并不知道它很难访问，直到你真的需要它。

**定义‘数据监狱’**

让我首先确保我们都清楚我所说的**数据监狱**是什么意思。基本上，**数据监狱**描述的是这样一种情形：尽管数据在技术上是可用的，但它被困在格式中，限制了其轻松访问、分析和有效使用。常见的罪魁祸首包括PDF和其他未设计为便于数据提取和处理的文档格式。

**我正在解决问题的背景**

西雅图公立学校（SPS）在2023/2024学年接近尾声时[宣布](https://www.seattletimes.com/education-lab/seattle-could-close-about-a-quarter-of-its-elementary-schools/)，由于预算缺口超过每年1亿美元且持续增长，他们无法克服这一困境。随后，[一个项目](https://www.seattleschools.org/resources/well-resourced-schools/)和分析启动，旨在确定并关闭西雅图近70所小学中的最多20所。

我是其中一所小学学生的家长。像许多其他在没有太多预警的情况下被推向这个项目的家长一样，尽管学区通过其网页指向了[多个](https://www.seattleschools.org/departments/finance/budget/current-budget/)PDF文件，提供了相关数据，但我仍然对数据的开放性和可用性感到沮丧。

当然，也可以有人去逐一复制粘贴每个PDF中的数据，但这将花费大量的时间。

当然，也有人可以查看那些已经公开的先前分析（同样是通过PDF提供），但这些分析可能只是间接相关。

当然，有人可以通过CSV请求这些数据，但这些请求仅由[2个兼职工作人员](https://www.seattleschools.org/departments/legal-department/public-records-requests/)支持，获取数据的时间通常是以月为单位，而非天。

因此，我花了一些时间来获取我认为任何人都需要的数据，以便合理判断哪些学校（如果有的话）应该关闭。显而易见的信息，如预算、入学人数和设施数据——过去3年每所学校的相关数据。

幸运的是，我不需要手动复制粘贴数据。相反，我使用[Python](https://github.com/chrislydick/sps-budget-analysis/blob/main/elementary_school_data_extract.ipynb)来抓取PDF，从而获得一个任何人都可以用来进行强有力分析的数据集。尽管如此，这仍然花费了很长时间。

**当数据被解锁时，可能发生的事情**

从我开始收集数据的几周后，你可以看到最终的产品。[我开发的应用程序](https://sps-budget-analysis-2023.streamlit.app/)托管在[Streamlit](https://streamlit.io)平台上，这是一个非常简洁的平台，提供了所有的框架和支持，能够快速实现数据探索或为你的代码提供用户界面。你可以将精力集中在解决问题上，而不是纠结于按钮、HTML等细节。

![](../Images/3d293d92cbeb31b7e0be2fd75d0bbd35.png)

该应用程序的默认设置是没有学校关闭，提供了一个基线。用户可以选择学校，查看在学校关闭后的前后数据，包括指标和地图视角，以了解学生如何被重新分配到其他学校。图片由作者提供。

我的探索开始时是对预算和招生本身的检查，但很快转变为一种理解关闭学校所带来的影响的方式——具体来说，学生如何根据招生边界之间的现有关系以及学生在这些边界内外的就读情况进行重新分配。

所以，这就成了我所创建内容的主要使用场景：

> 作为社区成员，从容量角度来看，特定的学校关闭情景如何影响其他周围学校？

![](../Images/e76390f392d7dfdb9efb8defa40b172c.png)

通过加载第一个示例，我们可以看到16所学校被标记为关闭，要求将3400多名学生重新分配到其他学校，并且大多数学校的容量百分比都显著增加。这种情况导致另外24所学校的容量超过了100%。图像来源：作者。

所有数据都可以通过下面的表格快速下载，用户可以迅速操作并观察自己的场景。例如：“如果他们关闭了我的学校，会怎么样？”

**一个无意的顿悟！**

在分析这些数据时，我确实做出了一个有趣的观察。这个观察是在完成一个相对简单的线性回归后做出的。**回归的y轴截距大约为76万美元**，这代表了学校开放的预估基准成本。简单来说，通过关闭一所学校，重新分配员工和预算，学区可能会看到每所学校平均节省约76万美元。因此，关闭多达20所学校，保持人员水平并重新分配学生，可能会节省超过1500万美元。但这与关闭所需弥补的1亿美元赤字之间存在很大差距。这可能需要进一步的分析——如果我能接触到更好（甚至更多）的数据就好了……

**突破困境是一个选择**

当我进行这个练习时，越来越明显的是，信息自由法案（FOIA）和公共记录法为突破数据监禁提供了一个机会（也许是无意的），当一些简单的抓取技能无法发挥作用时。

其他人可能已经请求过这些数据，获得了必要的批准，并收到了这些数据。尽管共享给请求者的数据被视为公共数据，但它并没有以便捷的方式提供给其他人。这就是问题所在。*为什么我不能直接查看并使用别人已经请求并获得的数据呢？*

**总结**

所以——我[做了一个东西](https://sps-budget-analysis-2023.streamlit.app/)。我通过[使用一个工具](https://github.com/chrislydick/sps-budget-analysis/blob/main/elementary_school_data_extract.ipynb)从PDF中抓取数据。但我也向西雅图公立学校和Seattle.gov提出了请求，要求获取过去两年内通过公共请求和信息自由法案（FOIA）提供的所有公共学校数据。这些响应和请求本身也是公共记录。

但是，对于那些没有编写代码抓取数据技能的人来说，这些数据仍然触手可及，却被PDF、网页和图片所锁住。事情不必是这样的，也不应该是这样的。

当然，有关于首先统一数据格式的讨论是非常必要的。像[Delta Lake](https://delta.io/)这样的标准表格格式，看起来是一个非常可扩展且合理的解决方案（感谢[Robert Dale Thompson](https://medium.com/u/55be6f6be025?source=post_page---user_mention--9051c78e29f3--------------------------------)），但即使是使过去的FOIA（信息自由法案）和公共记录请求的数据，在现有网站如[data.seattle.gov](https://data.seattle.gov/)上可访问，也似乎是最基本的要求。

让我们携手解锁公共数据的潜力。查看我的[Streamlit 应用程序](https://sps-budget-analysis-2023.streamlit.app/)，了解如何通过易于访问的数据带来实际的变化。通过联系当地代表并支持推动透明度的倡议，加入我一起倡导开放数据。与您的社区分享自己的经验和知识，传播意识并推动变革。我们共同努力，可以打破这些数据监狱，确保信息真正对每个人都可获取。

一旦我获得数据，就会有更多内容发布。
