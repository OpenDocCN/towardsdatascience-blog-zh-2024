# 使用 OpenAI 和 PandasAI 进行 Series 操作

> 原文：[`towardsdatascience.com/using-openai-and-pandasai-for-series-operations-2978d539fb66?source=collection_archive---------1-----------------------#2024-06-29`](https://towardsdatascience.com/using-openai-and-pandasai-for-series-operations-2978d539fb66?source=collection_archive---------1-----------------------#2024-06-29)

## 将自然语言查询和操作融入到你的 Python 数据清洗工作流中。

[](https://michaelbrucewalker.medium.com/?source=post_page---byline--2978d539fb66--------------------------------)![Michael B Walker](https://michaelbrucewalker.medium.com/?source=post_page---byline--2978d539fb66--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2978d539fb66--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2978d539fb66--------------------------------) [Michael B Walker](https://michaelbrucewalker.medium.com/?source=post_page---byline--2978d539fb66--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2978d539fb66--------------------------------) ·阅读时间：6 分钟·2024 年 6 月 29 日

--

![](img/ba6fcadd6ed048d35c8b0a0ea7df7a48.png)

红熊猫画作由艺术家 Karen Walker 捐赠。

我们在进行 pandas 数据清洗项目时需要执行的许多 Series 操作，可以通过 AI 工具来辅助完成，其中包括 PandasAI。PandasAI 利用 OpenAI 等大型语言模型，实现对数据列的自然语言查询和操作。在这篇文章中，我们将探讨如何使用 PandasAI 查询 Series 值、创建新的 Series、按条件设置 Series 值以及重塑数据。

你可以通过在终端或 Windows Powershell 中输入 `pip install pandasai` 来安装 PandasAI。你还需要从 openai.com 获取一个令牌，以便向 OpenAI API 发送请求。

由于 PandasAI 库正在快速发展，你可以根据使用的 PandasAI 和 pandas 版本，预期会有不同的结果。在这篇文章中，我使用的是 PandasAI 版本 1.4.8 和 pandas 版本 1.5.3。

我们将使用来自美国劳动统计局（Bureau of Labor Statistics）进行的《国家青少年纵向研究》（NLS）中的数据。NLS 对同一批高中生进行了超过 25 年的调查，涵盖了关于教育成果和每年工作周数等众多有用的数据项。该数据公开可用，网址为 nlsinfo.org。（NLS 的公开发布受美国政府 [开放数据政策](https://digital.gov/resources/open-data-policy-m-13-13/#:~:text=Making%20information%20resources%20accessible%2C%20discoverable,contribute%20significantly%20to%20job%20creation.) 的保护，允许非商业和商业用途。）

我们还将使用由 *Our World in Data* 提供的 COVID-19 数据。该数据集每天为每个国家提供一行，包含新病例和新死亡人数。此数据集可以在 [ourworldindata.org/covid-cases](https://ourworldindata.org/covid-cases) 下载，采用 Creative Commons CC BY 4.0 许可。你也可以从 [GitHub](https://github.com/PacktPublishing/Python-Data-Cleaning-Cookbook-Second-Edition/tree/main/6.%20SeriesOperations) 下载本文中使用的所有代码和数据。

我们首先从 PandasAI 导入 OpenAI 和 SmartDataframe 模块。我们还需要实例化一个 llm 对象：

```py
import pandas as pd
from pandasai.llm.openai import OpenAI
from pandasai import SmartDataframe
llm = OpenAI(api_token="Your API Token")
```

接下来，我们加载将要使用的 DataFrame，并从 NLS pandas DataFrame 创建一个 SmartDataframe 对象：

```py
covidcases = pd.read_csv("data/covidcases.csv")
nls97 = pd.read_csv("data/nls97f.csv")
nls97.set_index("personid", inplace=True)
nls97sdf = SmartDataframe(nls97, config={"llm": llm})
```

现在我们准备好生成来自 SmartDataframe 的 Series 的汇总统计数据。我们可以请求单个 Series 或多个 Series 的平均值：

```py
nls97sdf.chat("Show average of gpaoverall")

2.8184077281812128

nls97sdf.chat("Show average for each weeks worked column")

               Average Weeks Worked
                  0
weeksworked00 26.42
weeksworked01 29.78
weeksworked02 31.83
weeksworked03 33.51
weeksworked04 35.10
weeksworked05 37.34
weeksworked06 38.44
weeksworked07 39.29
weeksworked08 39.33
weeksworked09 37.51
weeksworked10 37.12
weeksworked11 38.06
weeksworked12 38.15
weeksworked13 38.79
weeksworked14 38.73
weeksworked15 39.67
weeksworked16 40.19
weeksworked17 40.37
weeksworked18 40.01
weeksworked19 41.22
weeksworked20 38.35
weeksworked21 36.17
weeksworked22 11.43
```

我们还可以通过另一个 Series 来汇总 Series 值，通常是一个类别型 Series：

```py
nls97sdf.chat("Show satmath average by gender")

   Female   Male
0  486.65 516.88
```

我们还可以通过 SmartDataframe 的 `chat` 方法创建一个新的 Series。我们不需要使用实际的列名。例如，当我们写 child at home 时，PandasAI 会识别出我们需要的是*childathome* Series：

```py
nls97sdf = nls97sdf.chat("Set childnum to child at home plus child not at
  home")
nls97sdf[['childnum','childathome','childnotathome']].\
  sample(5, random_state=1)

         childnum  childathome  childnotathome
personid                                       
211230        2.00         2.00            0.00
990746        3.00         3.00            0.00
308169        3.00         1.00            2.00
798458         NaN          NaN             NaN
312009         NaN          NaN             NaN
```

我们可以使用`chat`方法有条件地创建 Series 值：

```py
nls97sdf = nls97sdf.chat("evermarried is 'No' when maritalstatus is
  'Never-married', else 'Yes'")
nls97sdf.groupby(['evermarried','maritalstatus']).size()

evermarried  maritalstatus
No           Never-married    2767
Yes          Divorced          669
             Married          3068
             Separated         148
             Widowed            23
dtype: int64
```

PandasAI 对你在这里使用的语言非常灵活。例如，以下语句提供了相同的结果：

```py
nls97sdf = nls97sdf.chat("if maritalstatus is 'Never-married' set
  evermarried2 to 'No', otherwise 'Yes'")
nls97sdf.groupby(['evermarried2','maritalstatus']).size()

evermarried2  maritalstatus
No            Never-married    2767
Yes           Divorced          669
              Married          3068
              Separated         148
              Widowed            23
dtype: int64
```

我们可以对一些相似命名的列进行计算：

```py
nls97sdf = nls97sdf.chat("Set weeksworked for each row to the average of
  all weeksworked columns for that row")
```

这将计算所有 weeksworked00 到 weeksworked22 列的平均值，并将其赋值给一个新列，命名为 weeksworked。

我们可以轻松地根据汇总统计数据填充缺失的值：

```py
nls97sdf.gpaenglish.describe()

count   5,798
mean      273
std        74
min         0
25%       227
50%       284
75%       323
max       418
Name: gpaenglish, dtype: float64

nls97sdf = nls97sdf.chat("set missing gpaenglish to the average")
nls97sdf.gpaenglish.describe()

count   8,984
mean      273
std        59
min         0
25%       264
50%       273
75%       298
max       418
Name: gpaenglish, dtype: float64
```

我们还可以使用 PandasAI 做一些重塑。回想一下，COVID-19 案例数据为每个国家每一天都有新病例数据。假设我们只想要每个国家的第一行数据。我们可以通过传统方法使用*drop_duplicates*来实现：

```py
firstcase = covidcases.\
  sort_values(['location','casedate']).\
  drop_duplicates(['location'], keep='first')

firstcase.set_index('location', inplace=True)

firstcase.shape

(231, 67)

firstcase[['iso_code','continent','casedate',
  'total_cases','new_cases']].head(2).T

location    Afghanistan     Albania
iso_code            AFG         ALB
continent          Asia      Europe
casedate     2020-03-01  2020-03-15
total_cases        1.00       33.00
new_cases          1.00       33.00
```

我们也可以通过创建一个 SmartDataframe 并使用 `chat` 方法来获得相同的结果。我在这里使用的自然语言非常简洁，*展示每个国家的第一个 casedate 和 location 及其他值*：

```py
covidcasessdf = SmartDataframe(covidcases, config={"llm": llm})
firstcasesdf = covidcasessdf.chat("Show first casedate and location and
other values for each country.")

firstcasesdf.shape
(231, 7)

firstcasesdf[['location','continent','casedate',
  'total_cases','new_cases']].head(2).T

iso_code               ABW          AFG
location             Aruba  Afghanistan
continent    North America         Asia
casedate        2020-03-22   2020-03-01
total_cases           5.00         1.00
new_cases             5.00         1.00
```

注意，PandasAI 会智能地选择要获取的列。我们只获取需要的列，而不是所有列。我们也可以直接传递我们想要的列名给`chat`。 （PandasAI 按 iso_code 排序行，而不是按位置排序，这就是为什么第一行不同的原因。）

使用 PandasAI 时，很多工作实际上只是导入相关的库并实例化大型语言模型和 SmartDataframe 对象。一旦完成这些，简单的句子通过 SmartDataframe 的 `chat` 方法发送，就足以总结 Series 值并创建新的 Series。

PandasAI 擅长从 Series 生成简单的统计数据。我们甚至不需要准确记住 Series 的名称。我们使用的自然语言往往比传统的 pandas 方法（如 groupby）更直观。*按性别显示 satmath 平均值* 传递给 `chat` 的值就是一个很好的例子。

对 Series 的操作，包括创建新的 Series，也非常简单。我们通过指示 SmartDataframe 将家里有孩子的人数与家里没有孩子的人数相加，创建了一个表示孩子总数的 Series（childnum）。我们甚至没有提供字面上的 Series 名称，分别是 childathome 和 childnotathome。PandasAI 自动理解了我们的意思。

由于我们为 Series 操作传递给 `chat` 的是自然语言指令，因此没有唯一的正确方法来获得我们想要的结果。例如，当我们将 `evermarried is ‘No’ when maritalstatus is ‘Never-married’, else ‘Yes’` 传递给 `chat` 时，结果与我们将 `if maritalstatus is ‘Never-married’ set evermarried2 to ‘No’, otherwise ‘Yes’` 传递给 `chat` 时相同。

我们还可以通过简单的自然语言指令进行相当广泛的 DataFrame 重塑，就像我们提供的最后一个命令一样。我们在指令中加入了 `and other values`，以获取除了 casedate 之外的列。PandasAI 还自动识别出 location 作为索引是有意义的。

你可以在这里阅读更多关于如何使用 PandasAI 和 SmartDataframes 的内容：

[](https://docs.pandas-ai.com/llms?source=post_page-----2978d539fb66--------------------------------) [## 大型语言模型 - PandasAI

### PandasAI 支持多种大型语言模型（LLMs），用于从自然语言查询中生成代码。

docs.pandas-ai.com](https://docs.pandas-ai.com/llms?source=post_page-----2978d539fb66--------------------------------)

或者在我书的第二版《Python 数据清洗 Cookbook》中：

[](https://packt.link/hKI1y?source=post_page-----2978d539fb66--------------------------------) [## Python 数据清洗 Cookbook - 第二版：使用 pandas、NumPy 准备数据进行分析…

### Python 数据清洗 Cookbook - 第二版：使用 pandas、NumPy、Matplotlib 准备数据进行分析…

packt.link](https://packt.link/hKI1y?source=post_page-----2978d539fb66--------------------------------)

祝你数据清洗顺利，期待听到你的进展！
