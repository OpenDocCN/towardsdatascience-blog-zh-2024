# 可解释的异常值检测：频繁模式异常值因子（FPOF）

> 原文：[https://towardsdatascience.com/interpretable-outlier-detection-frequent-patterns-outlier-factor-fpof-0d9cbf51b17a?source=collection_archive---------0-----------------------#2024-05-25](https://towardsdatascience.com/interpretable-outlier-detection-frequent-patterns-outlier-factor-fpof-0d9cbf51b17a?source=collection_archive---------0-----------------------#2024-05-25)

## 一种支持分类数据并为标记为异常值的数据提供解释的异常值检测方法

[](https://medium.com/@wkennedy934?source=post_page---byline--0d9cbf51b17a--------------------------------)[![W Brett Kennedy](../Images/b3ce55ffd028167326c117d47c64c467.png)](https://medium.com/@wkennedy934?source=post_page---byline--0d9cbf51b17a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0d9cbf51b17a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0d9cbf51b17a--------------------------------) [W Brett Kennedy](https://medium.com/@wkennedy934?source=post_page---byline--0d9cbf51b17a--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0d9cbf51b17a--------------------------------) ·阅读时间10分钟·2024年5月25日

--

异常值检测是机器学习中的一个常见任务。具体来说，它是一种无监督机器学习：分析没有标签的数据。它是从数据集中找到相对于其他数据项而言非常不寻常的项目。

在数据中识别异常值可能有多种原因。如果所检查的数据是会计记录，且我们有兴趣查找错误或欺诈，数据中的交易通常太多，无法逐一手动检查，因此需要选择少量可管理的交易进行调查。一个好的起点是找到最不寻常的记录并进行检查；这一做法的假设是错误和欺诈应该足够罕见，以至于能够作为异常值突出显示。

也就是说，并非所有异常值都是有趣的，但错误和欺诈很可能是异常值，因此在寻找这些时，识别异常值可以是一个非常实用的技巧。

或者，数据可能包含信用卡交易、传感器读数、天气测量、生物数据或网站日志。在所有这些情况下，识别可能表示错误或其他问题的记录，以及最有趣的记录，都非常有用。

异常值检测通常也作为商业或科学发现的一部分，用于更好地理解数据以及数据中描述的过程。例如，在科学数据中，我们通常对找到最不寻常的记录感兴趣，因为这些记录可能是最具科学价值的。

## 异常值检测中的可解释性需求

对于分类和回归问题，通常更倾向于使用可解释的模型。这样可能会导致较低的准确性（对于表格数据，通常使用提升模型能够获得最高的准确性，而这些模型通常是难以解释的），但也更安全：我们知道模型如何处理未见过的数据。但在分类和回归问题中，通常也不需要理解为何做出个别的预测。只要模型的准确性足够高，简单地让模型进行预测可能就足够了。

然而，对于异常值检测来说，对可解释性的需求要高得多。当异常值检测器预测某一记录非常不寻常时，如果我们不清楚为什么会这样，我们可能不知道该如何处理这个项，甚至是否应该相信它是异常的。

实际上，在许多情况下，如果没有很好地理解为什么某些被标记为异常值的项被标记为异常，进行异常值检测可能会有很有限的价值。如果我们正在检查一个信用卡交易数据集，并且异常值检测程序识别出一系列看起来非常不寻常的购买，因而认为这些购买是可疑的，我们只能有效地调查这些异常值，前提是我们知道这些异常之处在哪里。在某些情况下，这可能是显而易见的，或者在花些时间审查之后会变得清楚，但如果异常的性质从一开始就能明确，那么这样会更有效率和效果。

与分类和回归一样，当无法解释时，通常可以尝试通过所谓的事后解释（post-hoc explanations）来理解预测结果。这些解释使用了XAI（可解释人工智能）技术，如特征重要性、代理模型、ALE图等。这些方法也非常有用，未来的文章中会进一步讨论。但在一开始就拥有清晰的结果也是非常有利的。

本文专门讨论表格数据，但将在后续文章中讨论其他形式的数据。目前，常用的表格数据异常值检测算法有多个，包括Isolation Forests、局部异常因子（LOF）、KNN、单类支持向量机（One-Class SVM）等。这些算法通常表现良好，但不幸的是，大多数算法并不提供对找到的异常值的解释。

大多数异常值检测方法在算法层面上容易理解，但要确定为什么某些记录被检测器评分较高而其他记录没有，仍然是很困难的。如果我们使用例如Isolation Forest处理一个财务交易数据集，我们可以看到哪些记录是最不寻常的，但可能无法理解其原因，特别是当数据表有许多特征，异常值包含多个特征的罕见组合，或者异常值是那些没有单一特征特别不寻常，但多个特征中等不寻常的情况。

## 频繁模式异常值因子（FPOF）

我们现在已经快速浏览了异常检测和可解释性的内容。本文其余部分摘自我写的《Python中的异常检测》一书（[https://www.manning.com/books/outlier-detection-in-python](https://www.manning.com/books/outlier-detection-in-python)），专门讲解FPOF。

FPOF（[FP-outlier: Frequent pattern based outlier detection](https://www.semanticscholar.org/paper/FP-outlier%3A-Frequent-pattern-based-outlier-He-Xu/3e9e34df7fbbc4445e6d17b7ecd600bc6d54232d)）是少数几种能够为异常检测提供某种程度可解释性的检测器之一，值得在异常检测中得到更多的应用。

它还有一个吸引人的特点，就是它是专门设计用于处理类别型数据，而不是数值型数据的。大多数现实世界中的表格数据是混合型的，包含了数值型和类别型列。但大多数检测器假设所有列都是数值型的，这就需要将所有类别型列进行数值编码（使用独热编码、顺序编码或其他编码方式）。

当像FPOF这样的检测器假设数据是类别型时，我们遇到的问题恰恰相反：所有数值型特征必须进行分箱，才能转化为类别型格式。这两种方式都可行，但当数据主要是类别型时，能够使用像FPOF这样的检测器非常方便。

在进行异常检测时，拥有一些数值型检测器和一些类别型检测器是非常有益的。不幸的是，类别型检测器相对较少，因此FPOF在这方面也很有用，即使在不需要可解释性的情况下。

## FPOF算法

FPOF通过识别表格中所谓的*频繁项集*（FISs）来工作。这些项集可以是某一特征中非常常见的值，也可以是跨越多个列、经常一起出现的值集合。

几乎所有表格都包含大量的频繁项集。基于单一值的频繁项集会出现在某些列中的某些值显著比其他值更常见的情况下，这几乎是永远成立的。基于多个列的频繁项集会出现在这些列之间存在关联的情况下：某些值（或数值范围）往往与其他列中的值（或数值范围）关联。

FPOF的核心思想是，只要一个数据集包含许多频繁项集（几乎所有数据集都有），那么大多数行将包含多个频繁项集，而正常（内点）记录将包含比异常行更多的频繁项集。我们可以利用这一点，将异常行识别为那些包含的频繁项集远少于大多数行的记录。

## 使用真实数据的示例

在使用FPOF的真实世界示例中，我们来看一下OpenML上的SpeedDating数据集（[https://www.openml.org/search?type=data&sort=nr_of_likes&status=active&id=40536](https://www.openml.org/search?type=data&sort=nr_of_likes&status=active&id=40536)，遵循CC BY 4.0 DEED协议）。

执行 FPOF 从挖掘数据集中的频繁项集开始。Python 中有许多库可以支持这一操作。在这个示例中，我们使用 mlxtend（[https://rasbt.github.io/mlxtend/](https://rasbt.github.io/mlxtend/)），这是一个通用的机器学习库。它提供了几种算法来识别频繁项集；我们在这里使用了其中一种名为 *apriori* 的算法。

我们首先从 OpenML 收集数据。通常我们会使用所有的分类特征和（分箱的）数值特征，但为了简化起见，在这里我们只使用少量特征。

如前所述，FPOF 确实需要对数值特征进行分箱。通常，我们会对每个数值列使用少量（大约 5 到 20 个）等宽的分箱。pandas 的 cut() 方法在这方面非常方便。这个示例甚至更简单，因为我们只处理分类列。

```py
from mlxtend.frequent_patterns import apriori
import pandas as pd
from sklearn.datasets import fetch_openml
import warnings

warnings.filterwarnings(action='ignore', category=DeprecationWarning)

data = fetch_openml('SpeedDating', version=1, parser='auto') 
data_df = pd.DataFrame(data.data, columns=data.feature_names)

data_df = data_df[['d_pref_o_attractive', 'd_pref_o_sincere',
                   'd_pref_o_intelligence', 'd_pref_o_funny',
                   'd_pref_o_ambitious', 'd_pref_o_shared_interests']] 
data_df = pd.get_dummies(data_df) 
for col_name in data_df.columns:
    data_df[col_name] = data_df[col_name].map({0: False, 1: True})

frequent_itemsets = apriori(data_df, min_support=0.3, use_colnames=True) 

data_df['FPOF_Score'] = 0

for fis_idx in frequent_itemsets.index: 
    fis = frequent_itemsets.loc[fis_idx, 'itemsets']
    support = frequent_itemsets.loc[fis_idx, 'support'] 
    col_list = (list(fis))
    cond = True
    for col_name in col_list:
        cond = cond & (data_df[col_name])

    data_df.loc[data_df[cond].index, 'FPOF_Score'] += support   

min_score = data_df['FPOF_Score'].min() 
max_score = data_df['FPOF_Score'].max()
data_df['FPOF_Score'] = [(max_score - x) / (max_score - min_score) 
                         for x in data_df['FPOF_Score']]
```

apriori 算法要求所有特征都必须进行独热编码。为此，我们使用 pandas 的 get_dummies() 方法。

然后，我们调用 apriori 方法来确定频繁项集。在这个过程中，我们需要指定最小支持度，即频繁项集出现的最小行数比例。我们不希望这个值太高，否则记录，即使是强内点，也会包含很少的频繁项集，从而难以与异常值区分开。我们也不希望这个值太低，否则频繁项集可能没有意义，而且异常值和内点可能包含相同数量的频繁项集。较低的最小支持度也可能导致 apriori 生成大量的频繁项集，导致执行变慢并降低可解释性。在这个例子中，我们使用 0.3。

也可以，且有时会，设置 FIS 的大小限制，要求它们与某些最小和最大列数相关，这有助于缩小你最感兴趣的异常值形式。

然后，频繁项集以 pandas 数据框的形式返回，其中包含支持度和列值列表（以独热编码列的形式，指示原始列和值）。

为了解释结果，我们可以首先查看频繁项集，下面展示了这一部分。为了包括每个频繁项集的长度，我们添加了：

```py
frequent_itemsets['length'] = \
    frequent_itemsets['itemsets'].apply(lambda x: len(x))
```

找到了 24 个频繁项集，最长的涵盖了三个特征。下表展示了前十行，按支持度排序。

![](../Images/0bef8be40f6bb2f89b2fac3967a29d57.png)

然后，我们遍历每个频繁项集，并通过支持度为每一行中包含该频繁项集的记录增加得分。这个过程可以选择性地调整，以偏向更长的频繁项集（因为支持度为 0.4 且覆盖 5 列的频繁项集，相较于支持度为 0.4 且覆盖 2 列的频繁项集，在其他条件相同的情况下，更具相关性），但在此我们仅使用每行中频繁项集的数量和支持度。

这实际上产生了一个关于正常性而非异常性的得分，因此当我们将得分标准化为介于 0.0 和 1.0 之间时，我们会反转顺序。得分最高的行现在是最强的异常值：那些包含最少和最少常见频繁项集的行。

将得分列添加到原始数据框并按得分排序后，我们可以看到最正常的行：

![](../Images/cd5b92569ca1a82ec11831d8bfc66202.png)

我们可以看到这一行的值与 FIS 匹配得很好。`d_pref_o_attractive` 的值是 [21–100]，这是一个 FIS（支持度 0.36）；`d_pref_o_ambitious` 和 `d_pref_o_shared_interests` 的值分别是 [0–15] 和 [0–15]，这也是一个 FIS（支持度 0.59）。其他值也倾向于匹配 FIS。

接下来展示的是最不寻常的行。该行与任何已识别的 FIS 都不匹配。

![](../Images/205f2f8164d00c184faec829190df103.png)

由于频繁项集本身非常易于理解，这种方法具有产生合理可解释结果的优势，尽管当使用多个频繁项集时这一点的适用性较差。

![](../Images/2c12b426d1fa6cdee6a4eae3d9713570.png)

可解释性可能会降低，因为异常值是通过不包含 FIS 来识别的，而不是通过包含 FIS，这意味着解释一行的得分就等于列出它没有包含的所有 FIS。然而，解释每个异常值时，并不严格需要列出所有缺失的 FIS；列出一小组最常见的缺失 FIS 就足以为大多数目的提供合理的异常值解释。关于存在的 FIS 及其在行中出现的正常数值和频率的统计数据，能够为比较提供良好的背景。

这种方法的一种变体是使用不频繁的项集，而不是频繁的项集，通过每一行所包含的不频繁项集的数量和稀有度来对其进行评分。这也可以产生有用的结果，但计算开销要大得多，因为需要挖掘更多的项集，并且每一行都要与多个 FIS 进行测试。尽管如此，最终的得分可能更具可解释性，因为它们是基于每一行中找到的项集，而不是缺失的项集。

## 结论

除了这里的代码外，我不知道 Python 中有 FPOF 的实现，尽管 R 中有一些实现。FPOF 的主要工作是挖掘 FIS，而有许多 Python 工具可以用来进行这一操作，包括这里使用的 mlxtend 库。剩余的 FPOP 代码，如上所示，相对简单。

鉴于异常检测中可解释性的重要性，FPOF 很可能值得尝试。

在未来的文章中，我们还将介绍一些其他可解释的异常检测方法。

所有图像均由作者提供
