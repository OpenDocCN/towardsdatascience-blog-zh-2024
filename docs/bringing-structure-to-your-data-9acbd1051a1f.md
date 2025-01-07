# 为你的数据带来结构

> 原文：[https://towardsdatascience.com/bringing-structure-to-your-data-9acbd1051a1f?source=collection_archive---------4-----------------------#2024-10-14](https://towardsdatascience.com/bringing-structure-to-your-data-9acbd1051a1f?source=collection_archive---------4-----------------------#2024-10-14)

## 使用路径模型检验假设

[](https://medium.com/@doriandrost?source=post_page---byline--9acbd1051a1f--------------------------------)[![Dorian Drost](../Images/1795395ad0586eafd83d3e2f7b975ca8.png)](https://medium.com/@doriandrost?source=post_page---byline--9acbd1051a1f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9acbd1051a1f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9acbd1051a1f--------------------------------) [Dorian Drost](https://medium.com/@doriandrost?source=post_page---byline--9acbd1051a1f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9acbd1051a1f--------------------------------) ·11分钟阅读·2024年10月14日

--

![](../Images/699faed9c69cc5392024f88bcb54515a.png)

在复杂的路径模型中，可能很难找到方向。图片由[Deva Darshan](https://unsplash.com/@darshan394?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

数据科学家常常收集大量变量，并寻找它们之间的关系。在这个过程中，假设和假说有助于理解变量之间的具体关系。一个学生准备下一次考试的动力是否会影响他们的成绩？还是良好的成绩会激励他们去学习？那么，究竟是什么样的行为模式会促使那些有动力的人最终取得好成绩？

为了给像上述问题提供一些结构，并提供一个工具来进行实证检验，我想在本文中解释**路径模型**，也叫做**结构方程模型**（**SEM**）。虽然在社会科学领域，如心理学中，路径模型被广泛使用，但我觉得它们在数据科学和计算机科学等其他领域并不那么突出。因此，我想概述**路径分析**的主要概念，并介绍**semopy**，这是一个用于在Python中应用路径分析的包。在整篇文章中，我们将分析人工数据，以展示可以通过路径模型解决的典型问题，并介绍**调节变量**和**中介变量**的概念。请注意，这些数据是为演示目的生成的，可能在每个细节上并不完全符合现实。

# 研究问题

![](../Images/6cdfe9a9cc52258ea24150f4549a83c5.png)

在分析数据之前，我们需要对我们要寻找的内容有所了解。图片来源：[Ansia Lasa](https://unsplash.com/@ansia_lasa?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果我们想要分析数据，我们需要有一个研究问题，想要调查的内容。对于本文，我们假设调查的是学校儿童及其所获得的成绩。我们可能对促进学习和获得好成绩的因素感兴趣。可能的因素包括他们在学校的乐趣、他们对班级的归属感、对学科的兴趣、他们在班级里的朋友数量、与老师的关系、他们的智力等。于是我们进入不同的学校，通过发放问卷收集数据，问卷内容涉及归属感、与老师的关系、对学科的兴趣以及学生在学校的乐趣，我们还进行智商测试，并询问他们有多少朋友。当然，我们还会收集他们的考试成绩。

## 一切从数据开始

我们现在拥有了以下所有变量的数据：

![](../Images/5739918f543b62f0a6cb408917f34e52.png)

我们的下一步是调查，*到底*这些变量如何影响成绩。我们可以对这些影响做出不同的假设，并用数据来验证这些假设。让我们从最简单的情况开始，假设每个变量对成绩都有直接影响，而且这种影响与其他变量无关。例如，我们会假设智力越高，成绩越好，不论对学科的兴趣或在学校的乐趣如何。我们对其他变量也会假设类似的关系。用图形显示，这种关系是这样的：

![](../Images/1b64a11ded44737b91d9a4448f124893.png)

假设所有变量都直接影响变量成绩*。图片来源：作者。*

每个箭头表示变量之间的影响。我们也可以将这种关系表示为加权和，像这样：

grades = a**feeling_of_belonging* + b**number_of_friends* + c**relationship_with_teacher* + d**fun_in_school* + e**intelligence* + f**interest_in_topic*

这里的a、b、c、d、e和f是权重，告诉我们不同变量对我们结果*成绩*的影响有多强。好的，这是我们的假设。现在我们想要根据数据来测试这个假设。假设我们有一个名为*data*的数据框架，其中每个列代表上述的一个变量。然后我们可以像这样在Python中使用semopy：

```py
import semopy 

path = """
grades ~ intelligence + interest_in_topic 
+ feeling_of_belonging + relationship_with_teacher 
+ fun_in_school + number_of_friends
"""

m = semopy.Model(path)
m.fit(data)
```

在最后几行中，我们创建了一个*semopy.Model*对象并用数据进行拟合。最有趣的部分是前面提到的变量*path*。在这里，我们指定了我们刚刚提出的假设，即变量*grades*是所有其他变量的组合。在波浪号（~）左边的部分是我们期望依赖于波浪号右边变量的那个变量。请注意，我们没有明确指定权重a、b、c、d、e和f。这些权重实际上是我们想要了解的内容，所以让我们运行以下代码行以获得结果：

```py
m.inspect()
```

![](../Images/288f1d1a5a7d95f8d419748c33f4b1a8.png)

假设所有变量直接影响成绩变量的结果。图片来自作者。

权重a、b、c、d、e和f是我们在*Estimate*列中看到的内容。我们可以从这个表格中提取什么信息呢？首先，我们看到一些权重大，一些权重小。例如，*feeling_of_belonging*的权重最大（0.40），这表明它有最强的影响力。例如，*interest_in_topic*的影响要小得多（0.08），而像*intelligence*和*number_of_friends*这样的其他变量的权重几乎为零。

此外，请查看*p-value*列。如果你熟悉统计测试，可能已经知道如何解读这个值。如果不熟悉，别担心。关于如何理解[**显著性**](https://en.wikipedia.org/wiki/Statistical_significance)（这正是此列所表示的内容）的文献非常丰富，我鼓励你深入了解它。然而，暂时我们可以说，这一列给我们提供了一些关于我们发现的效应是否只是随机噪声的可能性的线索。例如，*number_of_friends*对成绩的影响非常小（-0.01），而且很可能（0.42）只是巧合。因此我们会说没有**效应**，尽管权重不完全为零。反之，如果p值接近零，我们可以假设我们确实找到了一个不只是巧合的效应。

好的，根据我们的分析，有三个变量对成绩产生了影响，它们是*interest_in_topic*（0.08）、*feeling_of_belonging*（0.40）和*relationship_with_teacher*（0.19）。其他变量没有影响。这是我们的最终答案吗？

结果并非如此！请记住，semopy执行的计算受我们给定假设的影响。我们假设所有变量都直接影响成绩，且彼此独立。但如果实际关系有所不同呢？变量之间可能有许多不同的相互影响方式，因此让我们提出一些不同的假设，从而探索**中介变量**和**调节变量**的概念。

# 中介变量

![](../Images/4c0a1f6a78fb9ba86852b4021058d8f7.png)

调解变量可以像台球一样，一个推动另一个。照片来源：[Steve Mushero](https://unsplash.com/@steve_mushero?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

与其说*朋友数量*和*归属感*直接影响成绩，不如换个方向思考。如果你在班里没有任何朋友，你就不会感到归属感，对吧？这种（不）归属的感觉可能会影响成绩。所以关系应该更像这样：

![](../Images/8330d79ef552182c20b9da174ccac5bb.png)

模型假设朋友数量影响归属感，归属感反过来影响成绩。图像来源：作者。

请注意，*朋友数量*对*成绩*的直接影响已经消失，但我们现在看到*朋友数量*对*归属感*的影响，*归属感*反过来影响*成绩*。我们可以拿这个假设让semopy来测试：

```py
path = """
feeling_of_belonging ~ number_of_friends
grades ~ feeling_of_belonging
"""
m = semopy.Model(path)
m.fit(data)
```

在这里我们说到，*归属感*取决于*朋友数量*，而*成绩*则取决于*归属感*。你可以看到下面的输出。现在，*归属感*和*成绩*之间仍然有0.40的权重，但我们也有0.29的权重连接*朋友数量*和*归属感*。看起来我们的假设是有效的。朋友的数量影响归属感，而归属感又反过来影响成绩。

![](../Images/1f378a7e073ab5a5a4d63537cf4de2cb.png)

朋友数量影响归属感的假设结果。图像来源：作者。

我们在这里建模的这种影响叫做**中介**，因为一个变量在另一个变量的影响过程中充当了中介。换句话说，*朋友数量*对*成绩*没有直接影响，而是通过*归属感*间接影响成绩。

中介分析可以帮助我们理解一些变量是如何及通过哪些过程相互影响的。有明确目标和想法的学生不太可能辍学，但到底是什么行为模式导致了在学校中表现良好呢？是学习更多吗？是如果不理解某个主题就寻求帮助吗？这些都可能是中介变量，部分解释了明确目标对学术成就的影响。

# 调节变量

![](../Images/9815aa6f25875b7f31257e1b1e824262.png)

调节变量可以像一个阀门，只允许一定量的流量通过。照片来源：[Igal Ness](https://unsplash.com/@igalness?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们刚才看到，假设变量之间存在不同的关系帮助更有效地描述数据。也许我们可以做类似的事情来解释为什么数据中智力对成绩没有影响。这是令人惊讶的，因为我们通常会认为更聪明的学生平均应该能获得更高的成绩，不是吗？然而，如果一个学生对某个主题不感兴趣，他可能就不会投入多少精力，不是吗？也许智力对成绩没有直接的影响，但智力和兴趣之间存在某种联合作用。如果学生对某个主题感兴趣，那么智力更高的学生将获得更高的成绩；但如果他们不感兴趣，那就无所谓了，因为他们没有投入任何努力。我们可以这样来可视化这种关系：

![](../Images/5b1518740959175a4cbe2918fc9a8721.png)

假设*对主题的兴趣*调节*智力*对*成绩*的影响。图片由作者提供。

也就是说，我们假设*智力*对*成绩*有影响，但这个影响受*对主题的兴趣*的影响。如果兴趣很高，学生会充分利用自己的认知能力，从而获得更高的成绩；但如果兴趣很低，他们就不会这样做。

如果我们想在semopy中检验这个假设，我们需要创建一个新变量，它是*智力*和*对主题的兴趣*的乘积。你能看出将变量相乘是如何反映我们刚才的想法的吗？如果*对主题的兴趣*接近零，那么整个乘积也接近零，不管*智力*如何。但如果*对主题的兴趣*很高，那么乘积主要由*智力*的高低决定。所以，我们在数据框中计算一个新列，叫做*智力_x_兴趣*，然后将我们假设的这种关系输入到semopy中，与成绩之间的关系如下：

```py
path = """
grades ~ intellgence_x_interest
"""
m = semopy.Model(path)
m.fit(data)
```

然后我们发现一个影响：

![](../Images/caccfe4c42e37ed16aa2ed026cb363b1.png)

假设智力和兴趣的乘积影响成绩的结果。图片由作者提供。

之前，*智力*对*成绩*没有影响，而*对主题的兴趣*几乎没有影响（0.08）。但是，如果我们将这两个因素结合起来，我们会发现一个很大的影响值0.81。看起来这两个变量的结合能够更好地描述我们的数据。

这种变量间的交互作用叫做**调节作用**。我们可以说，*对主题的兴趣*调节了*智力*对*成绩*的影响，因为*智力*与*成绩*之间的关系强度取决于*兴趣*。调节作用对于理解变量之间关系如何在不同情境或不同参与者群体之间有所不同非常重要。例如，工作经验更长通常会正面影响薪水，但对于男性而言，这种影响比女性要强。在这种情况下，性别是工作经验对薪水影响的调节因素。

# 总结

如果我们将之前的所有步骤结合起来，我们的新模型看起来是这样的：

![](../Images/6b6afe9978fc8a769b9bd5e1137ca139.png)

包含所有先前假设的完整模型。图片由作者提供。

![](../Images/136f86dc292ae0b157a2e61087332838.png)

完整模型的结果。图片由作者提供。

现在我们为数据构建了一个更加复杂且更具 plausibility（合理性）的结构。注意，*fun_in_school*仍然没有对*grades*产生影响（因此在上面的可视化中，我给它加上了虚线）。要么数据中不存在这种影响，要么我们尚未找到与其他变量的正确互动关系。我们甚至可能缺少一些有趣的变量。就像*intelligence*只有与*interest_in_topic*结合时才有意义一样，或许还有另一个变量是理解*fun_in_school*对成绩影响所必需的。这向你展示了，对于路径分析来说，理解你的数据并明确你想要调查的内容是非常重要的。一切都始于你从理论中（有时是凭直觉）得出的假设，然后通过数据来验证这些假设，从而更好地理解数据。

这就是路径模型的含义。让我们总结一下我们刚才学到的内容。

+   路径模型使我们能够测试变量之间如何相互影响的假设。

+   **中介效应**出现的情况是，当变量*a*对变量*c*没有直接影响，但通过影响另一个变量*b*，再由*b*影响*c*。

+   我们称之为**调节效应**，如果变量*a*对变量*c*的影响强度根据另一个变量*b*的不同而有所变化。这个现象可以通过计算变量的乘积来建模。

+   **Semopy**可以用来在python中测试给定数据的路径模型。

我希望我已经能够让你相信路径模型的有用性。尽管如此，我所展示的仅仅是它的冰山一角。通过路径模型或从中衍生的其他模型，还可以测试更多复杂的假设，这些假设远远超出了本文的范围。

# 参考文献

你可以在这里找到semopy：

+   [https://semopy.com/index.html](https://semopy.com/index.html)

如果你想了解更多关于路径分析的内容，维基百科可以是一个很好的入门点：

+   [https://en.wikipedia.org/wiki/Structural_equation_modeling](https://en.wikipedia.org/wiki/Structural_equation_modeling)

我使用这本书作为统计学背景参考（不幸的是，它仅提供德语版本）：

+   Eid, M., Gollwitzer, M., & Schmitt, M. (2015). Statistik und Forschungsmethoden.

这是本文数据生成的方式：

```py
import numpy as np
import pandas as pd

np.random.seed(42)

N = 7500

def norm(x):
    return (x - np.mean(x)) / np.std(x)

number_of_friends = [int(x) for x in np.random.exponential(2, N)]

# let's assume the questionairs here had a range from 0 to 5
relationship_with_teacher = np.random.normal(3.5,1,N)
relationship_with_teacher = np.clip(relationship_with_teacher, 0,5)
fun_in_school = np.random.normal(2.5, 2, N)
fun_in_school = np.clip(fun_in_school, 0,5)

# let's assume the interest_in_topic questionaire goes from 0 to 10
interest_in_topic = 10-np.random.exponential(1, N)
interest_in_topic = np.clip(interest_in_topic, 0, 10)

intelligence = np.random.normal(100, 15, N)
# normalize variables
interest_in_topic = norm(interest_in_topic)
fun_in_school = norm(fun_in_school)
intelligence = norm(intelligence)
relationship_with_teacher = norm(relationship_with_teacher)
number_of_friends = norm(number_of_friends)

# create dependend variables
feeling_of_belonging = np.multiply(0.3, number_of_friends) + np.random.normal(0, 1, N)
grades = 0.8 * intelligence * interest_in_topic + 0.2 * relationship_with_teacher + 0.4*feeling_of_belonging + np.random.normal(0,0.5,N)

data = pd.DataFrame({
    "grades":grades,
    "intelligence":intelligence,
    "number_of_friends":number_of_friends,
    "fun_in_school":fun_in_school,
    "feeling_of_belonging": feeling_of_belonging,
    "interest_in_topic":interest_in_topic,
    "intellgence_x_interest" : intelligence * interest_in_topic,
    "relationship_with_teacher":relationship_with_teacher
})
```

*喜欢这篇文章吗？* [*关注我*](https://medium.com/@doriandrost) *以便接收我的未来更新。*
