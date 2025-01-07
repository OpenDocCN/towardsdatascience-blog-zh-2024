# 使用 Microsoft Fabric 和 PySpark 进行特征工程

> 原文：[`towardsdatascience.com/feature-engineering-with-microsoft-fabric-and-pyspark-16d458018744?source=collection_archive---------9-----------------------#2024-04-08`](https://towardsdatascience.com/feature-engineering-with-microsoft-fabric-and-pyspark-16d458018744?source=collection_archive---------9-----------------------#2024-04-08)

## Fabric 疯狂系列第二部分

[](https://medium.com/@roger_noble?source=post_page---byline--16d458018744--------------------------------)![Roger Noble](https://medium.com/@roger_noble?source=post_page---byline--16d458018744--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--16d458018744--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--16d458018744--------------------------------) [Roger Noble](https://medium.com/@roger_noble?source=post_page---byline--16d458018744--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--16d458018744--------------------------------) ·12 分钟阅读·2024 年 4 月 8 日

--

![](img/e76cf994c16355962435cec1d006594b.png)

图片由作者和 ChatGPT 提供。“设计一幅插图，重点描绘一位正在行动的篮球运动员，这次的主题是使用 PySpark 为机器学习模型生成特征，采用图像小说风格。”提示语：ChatGPT，4，OpenAI，2024 年 4 月 4 日。[`chat.openai.com.`](https://chat.openai.com./)

*特别感谢* [*Martim Chaves*](https://medium.com/@mgrc99) *，他与我共同撰写了这篇文章并开发了示例脚本。*

在我们 [之前的文章](https://medium.com/towards-data-science/fabric-madness-96b84dc5f241) 中，我们从高层次的角度介绍了如何在 [Microsoft Fabric](https://www.microsoft.com/en-us/microsoft-fabric) 中训练机器学习模型。在这篇文章中，我们希望更深入地探讨特征工程的过程。

特征工程是任何机器学习（ML）系统开发生命周期中的关键部分。它是开发周期中的一个步骤，旨在处理原始数据，以更好地代表其潜在结构，并提供额外的信息，从而增强我们的机器学习模型。特征工程既是一门艺术，也是一门科学。尽管我们可以采取特定的步骤来创建良好的特征，但有时只有通过实验，才能获得好的结果。良好的特征对于保证系统性能至关重要。

随着数据集的指数增长，传统的特征工程可能在处理非常大的数据集时遇到困难。这时，PySpark 就能派上用场——它是一个可扩展且高效的处理平台，专为大规模数据集设计。Fabric 的一大优势是，它使得使用 PySpark 变得简单！

在这篇文章中，我们将讨论：

+   PySpark 是如何工作的？

+   PySpark 基础

+   特征工程实践

希望在本文结束时，你能够自如地在 Fabric 中使用 PySpark 进行特征工程。让我们开始吧！

# PySpark 是如何工作的？

Spark 是一个分布式计算系统，允许在机器集群上高效且快速地处理大规模数据集。它围绕弹性分布式数据集（RDD）概念构建，RDD 是一个容错的数据集合，能够并行处理。RDD 是 Spark 的基础数据结构，它们允许将数据分布到机器集群中。

PySpark 是 Spark 的 Python API。它允许创建 Spark DataFrame，类似于 Pandas DataFrame，但具有分布式跨机器集群的优势。PySpark DataFrame 是 PySpark 的核心数据结构，它们允许以分布式方式操作大规模数据集。

PySpark 的核心是 `SparkSession` 对象，它是与 Spark 交互的基础。这个 `SparkSession` 允许创建 DataFrame 和其他功能。请注意，在 Fabric 中运行 Notebook 时，会自动为你创建一个 `SparkSession`，因此你无需担心这个问题。

了解了 PySpark 的大致工作原理后，我们来看看基础知识。

# PySpark 基础

尽管 Spark DataFrame 由于其相似性可能会让我们联想到 Pandas DataFrame，但使用 PySpark 时的语法可能会有所不同。在本节中，我们将介绍一些 PySpark 的基础知识，如读取数据、合并 DataFrame、选择列、分组数据、连接 DataFrame 和使用函数等。

## 数据

我们所查看的数据来自 2024 年美国大学篮球锦标赛，这些数据是从正在进行的*Kaggle 2024 年 3 月机器学习狂潮*竞赛中获取的，详情请见[此处](https://www.kaggle.com/competitions/march-machine-learning-mania-2024/overview)，并且根据 CC BY 4.0 许可协议[1]发布。

## 读取数据

正如在本系列的[上一篇文章](https://medium.com/towards-data-science/fabric-madness-96b84dc5f241)中提到的，第一步通常是创建一个湖仓并上传一些数据。然后，在创建 Notebook 时，我们可以将其附加到创建的湖仓上，这样我们就可以访问存储在那里的数据。

PySpark DataFrame 可以读取各种数据格式，如 CSV、JSON、Parquet 等。我们的数据存储为 CSV 格式，因此我们将使用该格式，如以下代码片段所示：

```py
# Read women's data
w_data = (
    spark.read.option("header", True)
    .option("inferSchema", True)
    .csv(f"Files/WNCAATourneyDetailedResults.csv")
    .cache()
)
```

在这个代码片段中，我们正在读取最终女子篮球大学锦标赛比赛的详细结果数据集。请注意，`"header"` 选项为 true 时意味着列名将从 CSV 文件的第一行推导出来。`inferSchema` 选项告诉 Spark 自动推测列的数据类型——否则它们将全部作为字符串读取。`.cache()` 用于将 DataFrame 保持在内存中。

如果你来自 Pandas，你可能会好奇 PySpark 中 `df.head()` 的等效函数是什么 —— 它是 `df.show(5)`。`.show()` 的默认值是显示前 20 行，因此需要特别指定 5 行。

## 合并 DataFrame

合并 DataFrame 可以通过多种方式进行。我们首先查看的是 union，要求两个 DataFrame 的列名相同：

```py
# Read women's data
...

# Read men's data
m_data = (
    spark.read.option("header", True)
    .option("inferSchema", True)
    .csv(f"Files/MNCAATourneyDetailedResults.csv")
    .cache()
)

# Combine (union) the DataFrames
combined_results = m_data.unionByName(w_data)
```

在这里，`unionByName` 通过匹配列名将两个 DataFrame 连接起来。由于女子和男子的 *详细比赛结果* 具有相同的列，这是一种很好的方法。另一种方法是使用 `union`，它通过匹配列的位置合并两个 DataFrame。

## 选择列

在 PySpark 中，可以使用 `.select()` 方法选择 DataFrame 中的列。我们只需将相关列的名称作为参数进行指定。

这是 `w_scores.show(5)` 的输出：

```py
# Selecting a single column
w_scores = w_data.select("WScore")

# Selecting multiple columns
teamid_w_scores = w_data.select("WTeamID", "WScore")
```

这是 `w_scores.show(5)` 的输出：

```py
+------+
|Season|
+------+
|  2010|
|  2010|
|  2010|
|  2010|
|  2010|
+------+
only showing top 5 rows
```

在选择列时，还可以使用 `.alias()` 方法重命名列：

```py
winners = w_data.select(
    w_data.WTeamID.alias("TeamID"),
    w_data.WScore.alias("Score")
)
```

## 分组数据

分组允许我们对数据中的各个组执行某些操作，通常与聚合函数结合使用。我们可以使用 `.groupBy()` 来实现这一点：

```py
# Grouping and aggregating
winners_average_scores = winners.groupBy("TeamID").avg("Score")
```

在这个示例中，我们按 `"TeamID"` 进行分组，这意味着我们考虑的是具有不同 `"TeamID"` 值的行组。对于每个组，我们计算 `"Score"` 的平均值。通过这种方式，我们可以获得每支队伍的平均得分。

这是 `winners_average_scores.show(5)` 的输出，显示了每支队伍的平均分数：

```py
+------+-----------------+
|TeamID|       avg(Score)|
+------+-----------------+
|  3125|             68.5|
|  3345|             74.2|
|  3346|79.66666666666667|
|  3376|73.58333333333333|
|  3107|             61.0|
+------+-----------------+
```

## 连接数据

可以使用 `.join()` 方法连接两个 DataFrame。连接本质上是通过将一个 DataFrame 的列添加到另一个 DataFrame 来扩展 DataFrame。

```py
# Joining on Season and TeamID
final_df = matches_df.join(stats_df, on=['Season', 'TeamID'], how='left')
```

在这个示例中，`stats_df` 和 `matches_df` 都使用 `Season` 和 `TeamID` 作为每一行的唯一标识符。除了 `Season` 和 `TeamID` 外，`stats_df` 还有其他列，比如每支队伍在每个赛季中的统计数据，而 `matches_df` 则包含关于比赛的信息，如日期和地点。此操作使我们能够将这些有趣的统计数据添加到比赛信息中！

## 函数

PySpark 提供了多个函数帮助我们转换 DataFrame。你可以在 [这里](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/functions.html) 找到完整的函数列表。

这是一个简单函数的示例：

```py
from pyspark.sql import functions as F

w_data = w_data.withColumn("HighScore", F.when(F.col("Score") > 80, "Yes").otherwise("No"))
```

在上面的代码片段中，当分数高于 80 时，会创建一个 `"HighScore"` 列。对于 `"Score"` 列中的每一行（由 `.col()` 函数指示），如果 `"Score"` 的值大于 80，则通过 `.when()` 函数选择 `"HighScore"` 列的值为 `"Yes"`，否则通过 `.otherwise()` 函数选择值为 `"No"`。

# 特征工程实战

现在我们对 PySpark 有了基本的了解，并知道如何使用它，让我们来看看常规赛统计特征是如何创建的。这些特征随后被用作机器学习模型的输入，以尝试预测最终锦标赛比赛的结果。

起点是一个名为 `regular_data` 的数据框，包含了*常规赛*的逐场比赛统计数据，这是指每年从 11 月到 3 月的美国大学篮球赛季。

这个数据框中的每一行包含了赛季、比赛日期、队伍 1 的 ID、队伍 2 的 ID 以及其他信息，如比赛的地点。重要的是，它还包含了*每支队伍*在*特定比赛*中的统计数据，例如 `"T1_FGM"`，表示队伍 1 的投篮命中数 (FGM)，或者 `"T2_OR"`，表示队伍 2 的进攻篮板 (OR)。

第一步是选择哪些列将被使用。这些列严格包含了比赛中的统计数据。

```py
# Columns that we'll want to get statistics from
boxscore_cols = [
    'T1_FGM', 'T1_FGA', 'T1_FGM3', 'T1_FGA3', 'T1_OR', 'T1_DR', 'T1_Ast', 'T1_Stl', 'T1_PF', 
    'T2_FGM', 'T2_FGA', 'T2_FGM3', 'T2_FGA3', 'T2_OR', 'T2_DR', 'T2_Ast', 'T2_Stl', 'T2_PF'
]
```

如果你感兴趣，以下是每个统计数据代码的含义：

+   FGM: 投篮命中数

+   FGA: 投篮出手数

+   FGM3: 三分球命中数

+   FGA3: 三分球出手数

+   OR: 进攻篮板。篮板是指当一次投篮尝试未能进网时，球从篮板反弹出来。如果进行投篮尝试的队伍重新获得球权，这称为“进攻篮板”。否则，称为“防守篮板”。

+   DR: 防守篮板

+   Ast: 助攻，指直接导致进球的传球

+   Stl: 抢断，指球权被抢走

+   PF: 个人犯规，指球员犯规时

从那里开始，创建了一个*聚合表达式*字典。基本上，对于前面列出的每个列名，都会存储一个计算该列均值的函数，并通过添加后缀 `"mean"` 来重命名。

```py
from pyspark.sql import functions as F
from pyspark.sql.functions import col  # select a column

agg_exprs = {col: F.mean(col).alias(col + 'mean') for col in boxscore_cols}
```

接着，数据按 `"Season"` 和 `"T1_TeamID"` 分组，并使用先前创建的字典中的聚合函数作为 `.agg()` 的参数。

```py
season_statistics = regular_data.groupBy(["Season", "T1_TeamID"]).agg(*agg_exprs.values())
```

请注意，分组是按照赛季和**队伍 1 的 ID**进行的——这意味着，例如，`"T2_FGAmean"` 实际上会是 T1 对手的投篮出手数的均值，而不一定是某支特定队伍的投篮出手均值。所以，我们实际上需要将像 `"T2_FGAmean"` 这样的列重命名为类似 `"T1_opponent_FGAmean"` 的名称。

```py
# Rename columns for T1
for col in boxscore_cols:
    season_statistics = season_statistics.withColumnRenamed(col + 'mean', 'T1_' + col[3:] + 'mean') if 'T1_' in col \
        else season_statistics.withColumnRenamed(col + 'mean', 'T1_opponent_' + col[3:] + 'mean')
```

在这一点上，必须提到的是，`regular_data` 数据框实际上每场比赛都有**两**行数据。这是为了让每场比赛的两支队伍都可以被标记为“T1”和“T2”。这个小“技巧”使得这些统计数据变得有用。

请注意，我们“只有”关于“T1”的统计数据。我们“需要”关于“T2”的统计数据——因为没有计算新的统计数据，所以“需要”加上引号。我们只需要相同的数据，但是列的名称不同，这样对于“T1”和“T2”的匹配，我们就能得到 T1 和 T2 的统计数据。因此，我们创建了一个镜像的 DataFrame，在这个 DataFrame 中，原来的“T1&mldr;mean”和“T1_opponent_&mldr;mean”被替换为“T2&mldr;mean”和“T2_opponent_&mldr;mean”。这是非常重要的，因为稍后，当我们将这些常规赛统计数据与锦标赛比赛数据合并时，我们就能够得到 T1 和 T2 的统计数据。

```py
season_statistics_T2 = season_statistics.select(
    *[F.col(col).alias(col.replace('T1_opponent_', 'T2_opponent_').replace('T1_', 'T2_')) if col not in ['Season'] else F.col(col) for col in season_statistics.columns]
)
```

现在，有两个 DataFrame，分别包含了“T1”和“T2”的赛季统计数据。由于最终的 DataFrame 将包含“赛季”、“T1TeamID”和“T2TeamID”，我们可以通过合并操作将这些新创建的特征合并起来！

```py
tourney_df = tourney_df.join(season_statistics, on=['Season', 'T1_TeamID'], how='left')
tourney_df = tourney_df.join(season_statistics_T2, on=['Season', 'T2_TeamID'], how='left')
```

## Elo 评分

Elo 最早由[阿尔帕德·厄洛](https://en.wikipedia.org/wiki/Elo_rating_system)创立，是一个用于零和游戏（即一方获胜另一方失败的游戏）的评分系统，例如篮球。在 Elo 评分系统中，每支队伍都有一个 Elo 评分，这个评分通常反映了队伍的质量。最初，每支队伍的 Elo 评分是相同的，当队伍获胜时，Elo 评分增加；当队伍失败时，Elo 评分减少。这个系统的一个关键特点是，战胜强队时 Elo 评分的增幅大于战胜弱队时的增幅。因此，它是一个非常有用的特征！

我们想要记录一个团队在常规赛结束时的 Elo 评分，并将其作为锦标赛的特征。为了做到这一点，我们为每场比赛计算了每个团队的 Elo 评分。为了计算这个特征的 Elo，我们发现使用 Pandas 更为直观。

Elo 的核心是计算每支队伍的预期得分。可以通过以下代码来描述这一过程：

```py
# Function to calculate expected score
def expected_score(ra, rb):
    # ra = rating (Elo) team A
    # rb = rating (Elo) team B
    # Elo function
    return 1 / (1 + 10 ** ((rb - ra) / 400))
```

考虑一支队伍 A 和一支队伍 B，这个函数计算队伍 A 对队伍 B 的预期得分。

对于每场比赛，我们都会更新队伍的 Elo 评分。请注意，比赛的地点也起着作用——在主场获胜被认为不如客场获胜令人印象深刻。

```py
# Function to update Elo ratings, keeping T1 and T2 terminology
def update_elo(t1_elo, t2_elo, location, T1_Score, T2_Score):
    expected_t1 = expected_score(t1_elo, t2_elo)
    expected_t2 = expected_score(t2_elo, t1_elo)

    actual_t1 = 1 if T1_Score > T2_Score else 0
    actual_t2 = 1 - actual_t1

    # Determine K based on game location
    # The larger the K, the bigger the impact
    # team1 winning at home (location=1) less impressive than winning away (location = -1)
    if actual_t1 == 1:  # team1 won
        if location == 1:
            k = 20
        elif location == 0:
            k = 30
        else:  # location = -1
            k = 40
    else:  # team2 won
        if location == 1:
            k = 40
        elif location == 0:
            k = 30
        else:  # location = -1
            k = 20

    new_t1_elo = t1_elo + k * (actual_t1 - expected_t1)
    new_t2_elo = t2_elo + k * (actual_t2 - expected_t2)

    return new_t1_elo, new_t2_elo
```

为了应用 Elo 评分系统，我们遍历了每个赛季的比赛，为每支队伍初始化一个基础评分，并逐场更新它们的评分。每支队伍在每个赛季的最终 Elo 评分，应该能很好地描述该队伍的质量。

```py
def calculate_elo_through_seasons(regular_data):

    # For this feature, using Pandas
    regular_data = regular_data.toPandas()

    # Set value of initial elo
    initial_elo = 1500

    # DataFrame to collect final Elo ratings
    final_elo_list = []

    for season in sorted(regular_data['Season'].unique()):
        print(f"Season: {season}")
        # Initialize elo ratings dictionary
        elo_ratings = {}

        print(f"Processing Season: {season}")
        # Get the teams that played in the season
        season_teams = set(regular_data[regular_data['Season'] == season]['T1_TeamID']).union(set(regular_data[regular_data['Season'] == season]['T2_TeamID']))

        # Initialize season teams' Elo ratings
        for team in season_teams:
            if (season, team) not in elo_ratings:
                elo_ratings[(season, team)] = initial_elo

        # Update Elo ratings per game
        season_games = regular_data[regular_data['Season'] == season]
        for _, row in season_games.iterrows():
            t1_elo = elo_ratings[(season, row['T1_TeamID'])]
            t2_elo = elo_ratings[(season, row['T2_TeamID'])]

            new_t1_elo, new_t2_elo = update_elo(t1_elo, t2_elo, row['location'], row['T1_Score'], row['T2_Score'])

            # Only keep the last season rating
            elo_ratings[(season, row['T1_TeamID'])] = new_t1_elo
            elo_ratings[(season, row['T2_TeamID'])] = new_t2_elo

        # Collect final Elo ratings for the season
        for team in season_teams:
            final_elo_list.append({'Season': season, 'TeamID': team, 'Elo': elo_ratings[(season, team)]})

    # Convert list to DataFrame
    final_elo_df = pd.DataFrame(final_elo_list)

    # Separate DataFrames for T1 and T2
    final_elo_t1_df = final_elo_df.copy().rename(columns={'TeamID': 'T1_TeamID', 'Elo': 'T1_Elo'})
    final_elo_t2_df = final_elo_df.copy().rename(columns={'TeamID': 'T2_TeamID', 'Elo': 'T2_Elo'})

    # Convert the pandas DataFrames back to Spark DataFrames
    final_elo_t1_df = spark.createDataFrame(final_elo_t1_df)
    final_elo_t2_df = spark.createDataFrame(final_elo_t2_df)

    return final_elo_t1_df, final_elo_t2_df
```

理想情况下，我们不会逐场计算 Elo 的变化来确定每支队伍赛季结束时的最终 Elo。然而，我们没有想到更好的方法。你有什么想法吗？如果有，请告诉我们！

## 附加值

展示的特征工程步骤展示了我们如何将原始数据——常规赛统计数据——转化为具有预测能力的有价值信息。可以合理假设，一支球队在常规赛中的表现能够反映其在最终锦标赛中的潜在表现。通过计算每场比赛的统计数据平均值，包括球队和对手的统计数据，以及每支球队在最后一场比赛中的 Elo 评分，我们能够创建一个适合建模的数据集。然后，使用这些特征，训练模型来预测锦标赛比赛的结果，其他特征也以类似的方式开发。通过这些模型，我们只需要两个球队的 ID，查找它们的常规赛统计数据平均值和 Elo 评分，输入到模型中即可预测得分！

# 结论

在这篇文章中，我们探讨了 Spark 和 PySpark 背后的部分理论，如何应用这些理论，并展示了一个具体的实践例子。我们研究了如何在体育数据的案例中进行特征工程，创建常规赛统计数据，作为最终锦标赛比赛的特征使用。希望你觉得这篇文章有趣且有帮助——祝特征工程愉快！

**这篇文章及系列中其他文章的完整源代码可以在** [**这里**](https://dev.azure.com/nobledynamic/_git/FabricMadness)**找到。**

*最初发布于* [*https://nobledynamic.com*](https://nobledynamic.com/posts/fabric-madness-2/) *2024 年 4 月 8 日。*

## 参考文献

[1] Jeff Sonas, Ryan Holbrook, Addison Howard, Anju Kandru. (2024). March Machine Learning Mania 2024\. Kaggle. [`kaggle.com/competitions/march-machine-learning-mania-2024`](https://kaggle.com/competitions/march-machine-learning-mania-2024)
