# LangChain 的内置 AI 输出评估指标：它们有何不同？

> 原文：[https://towardsdatascience.com/langchains-built-in-eval-metrics-for-ai-output-how-are-they-different-f9dd75e2de08?source=collection_archive---------9-----------------------#2024-05-22](https://towardsdatascience.com/langchains-built-in-eval-metrics-for-ai-output-how-are-they-different-f9dd75e2de08?source=collection_archive---------9-----------------------#2024-05-22)

[](https://medium.com/@jonathan.bennion?source=post_page---byline--f9dd75e2de08--------------------------------)[![Jonathan Bennion](../Images/e2d9add564ee2ac0deb7863537b0ee73.png)](https://medium.com/@jonathan.bennion?source=post_page---byline--f9dd75e2de08--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f9dd75e2de08--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f9dd75e2de08--------------------------------) [Jonathan Bennion](https://medium.com/@jonathan.bennion?source=post_page---byline--f9dd75e2de08--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9dd75e2de08--------------------------------) ·5 分钟阅读·2024年5月22日

--

我通常为自己的使用案例创建自定义指标，但在开始使用 RAGAS 和/或 DeepEval 进行 RAG 评估之前，反复遇到 LangChain 内置的这些 AI 工具评估指标，所以最终对这些指标是如何创建的产生了好奇，并进行了快速分析（当然有所有固有的偏见）。

***TLDR 来自以下的相关性矩阵：***

+   ***有用性与一致性（0.46 的相关性）***：这一强相关性表明，大型语言模型（以及由此推导出的用户）可能会觉得一致性的回答更有帮助，强调了回答中逻辑结构的重要性。这只是相关性，但这一关系揭示了这一结论的可能性。*

+   ***争议性与犯罪性（0.44 的相关性）***：这表明，即使是有争议的内容也可能被视为犯罪，反之亦然，或许反映了用户偏好那些具有吸引力和发人深省的内容。*

+   ***一致性与深度：*** *尽管一致性与有用性相关，但深度并不相关。这可能意味着用户（再次假设用户偏好固有于大型语言模型的输出中——这一点本身是一个假设，并且是一种偏见，需要保持警觉）可能更倾向于简明扼要的回答，而不是详细的回答，尤其是在那些快速解决方案比全面解决方案更被看重的情境下。*

![](../Images/2934a7023672ad3ffd5a80c6b8bfbe3a.png)

内置指标可以在这里找到（移除一个与地面真相相关且更适合在其他地方处理的指标）：

```py
# Listing Criteria / LangChain's built-in metrics
from langchain.evaluation import Criteria
new_criteria_list = [item for i, item in enumerate(Criteria) if i != 2]
new_criteria_list
```

## 这些指标：

+   简洁性

+   细节

+   相关性

+   一致性

+   有害性

+   冷漠性

+   有用性

+   争议性

+   犯罪性

+   深度

+   创造力

> 首先，这些意味着什么，为什么要创建这些？

## 假设是：

+   这些是为了尝试定义可以解释输出与理论用例目标之间关系的度量标准，任何相关性可能是偶然的，但通常尽可能避免。

在看到[这里的源代码](https://api.python.langchain.com/en/latest/_modules/langchain/evaluation/criteria/eval_chain.html)后，我得出了这个假设。

> 第二，一些这些似乎相似和/或模糊——那么它们是如何区分的呢？

我使用了标准的SQuAD数据集作为基准，评估OpenAI的GPT-3-Turbo模型输出与该数据集中的真实值之间的差异（如果有的话），并进行比较。

```py
# Import a standard SQUAD dataset from HuggingFace (ran in colab)
from google.colab import userdata
HF_TOKEN = userdata.get('HF_TOKEN')

dataset = load_dataset("rajpurkar/squad")
print(type(dataset))
```

我获取了一组随机化的行进行评估（由于时间和计算限制，无法对整个数据集进行评估），因此这可能是更多噪音和/或偏差的切入点。

```py
# Slice dataset to randomized selection of 100 rows
validation_data = dataset['validation']
validation_df = validation_data.to_pandas()
sample_df = validation_df.sample(n=100, replace=False)
```

我定义了一个LLM，使用了ChatGPT 3.5 Turbo（为了节省成本，这个过程很快）。

```py
import os

# Import OAI API key
OPENAI_API_KEY = userdata.get('OPENAI_API_KEY')
os.environ['OPENAI_API_KEY'] = userdata.get('OPENAI_API_KEY')
# Define llm
llm = ChatOpenAI(model_name='gpt-3.5-turbo', openai_api_key=OPENAI_API_KEY)
```

然后迭代遍历抽样行以进行比较——LangChain在评估标准中使用了未知的‘评分’阈值，但假设这些阈值在所有度量中是相同的。

```py
# Loop through each question in random sample
for index, row in sample_df.iterrows():
    try:
        prediction = " ".join(row['answers']['text'])
        input_text = row['question']

        # Loop through each criteria\
        for m in new_criteria_list:
            evaluator = load_evaluator("criteria", llm=llm, criteria=m)

            eval_result = evaluator.evaluate_strings(
                prediction=prediction,
                input=input_text,
                reference=None,
                other_kwarg="value"  # adding more in future for compare
            )
            score = eval_result['score']
            if m not in results:
                results[m] = []
            results[m].append(score)
    except KeyError as e:
        print(f"KeyError: {e} in row {index}")
    except TypeError as e:
        print(f"TypeError: {e} in row {index}")
```

然后，我计算了均值和95%置信区间的置信区间。

```py
# Calculate means and confidence intervals at 95%
mean_scores = {}
confidence_intervals = {}

for m, scores in results.items():
    mean_score = np.mean(scores)
    mean_scores[m] = mean_score
    # Standard error of the mean * t-value for 95% confidence
    ci = sem(scores) * t.ppf((1 + 0.95) / 2., len(scores)-1)
    confidence_intervals[m] = (mean_score - ci, mean_score + ci)
```

然后绘制了结果。

```py
# Plotting results by metric
fig, ax = plt.subplots()
m_labels = list(mean_scores.keys())
means = list(mean_scores.values())
cis = [confidence_intervals[m] for m in m_labels]
error = [(mean - ci[0], ci[1] - mean) for mean, ci in zip(means, cis)]]

ax.bar(m_labels, means, yerr=np.array(error).T, capsize=5, color='lightblue', label='Mean Scores with 95% CI')
ax.set_xlabel('Criteria')
ax.set_ylabel('Average Score')
ax.set_title('Evaluation Scores by Criteria')
plt.xticks(rotation=90)
plt.legend()
plt.show()
```

可能直观地认为‘相关性’比其他指标要高得多，但有趣的是，总体而言它们都很低（可能要感谢GPT 3.5！），而‘有用性’是下一个最高的指标（可能反映了强化学习技术和优化）。

![](../Images/06c0aec539a408ad7380c763433a4d69.png)

为了回答我关于相关性的问题，我计算了一个简单的相关矩阵，使用了原始的比较数据框。

```py
# Convert results to dataframe
min_length = min(len(v) for v in results.values())
dfdata = {k.name: v[:min_length] for k, v in results.items()}
df = pd.DataFrame(dfdata)

# Filtering out null values
filtered_df = df.drop(columns=[col for col in df.columns if 'MALICIOUSNESS' in col or 'MISOGYNY' in col])

# Create corr matrix
correlation_matrix = filtered_df.corr()
```

然后绘制了结果（p值是通过[我的代码中的进一步部分](https://github.com/j-space-b/eval_analysis/blob/main/evaluation_metrics_corrplot.ipynb)创建的，且均小于0.05）

```py
# Plot corr matrix
mask = np.triu(np.ones_like(correlation_matrix, dtype=bool))
plt.figure(figsize=(10, 8))
sns.heatmap(correlation_matrix, mask=mask, annot=True, fmt=".2f", cmap='coolwarm',
            cbar_kws={"shrink": .8})
plt.title('Correlation Matrix - Built-in Metrics from LangChain')
plt.xticks(rotation=90)
plt.yticks(rotation=0)
plt.show()
```

令人惊讶的是，大多数没有相关性，考虑到LangChain代码库中描述的性质——这表明这些内容是经过深思熟虑的，我很高兴这些是内建的可以使用的。

![](../Images/2934a7023672ad3ffd5a80c6b8bfbe3a.png)

从相关矩阵中，显现出一些显著的关系：

+   *有用性与一致性（0.46 相关性）：* 这一强相关性表明，LLM（作为用户的代理）可能会发现一致的回应更有帮助，这强调了回应中逻辑结构的重要性。尽管这只是相关性，但这种关系为此铺平了道路。

+   *争议性与犯罪性（0.44 相关性）：* 这表明，即使是有争议的内容也可能被视为犯罪，反之亦然，这也许反映了用户对引人深思且富有启发性的材料的偏好。同样，这只是相关性。

主要结论：

1.  **连贯性与深度在帮助性中的对比：** 尽管连贯性与帮助性相关，但深度却没有。这可能意味着用户更倾向于简洁明了的答案而非详细的答案，特别是在那些快速解决方案比全面方案更受重视的场景中。

1.  **利用争议性：** 争议性与犯罪性之间的正相关关系提出了一个有趣的问题：是否可以以非犯罪的方式讨论争议话题？这可能在不牺牲内容质量的情况下增加用户参与度。

1.  **偏见与模型选择的影响：** 使用GPT-3.5 Turbo以及度量设计中固有的偏见可能会影响这些相关性。承认这些偏见对于准确解读和应用这些度量标准至关重要。

除非另有说明，本文中的所有图片均由作者创作。
