# 使用大型语言模型使新闻推荐变得可解释

> 原文：[https://towardsdatascience.com/making-news-recommendations-explainable-with-large-language-models-74f119c7e036?source=collection_archive---------2-----------------------#2024-11-30](https://towardsdatascience.com/making-news-recommendations-explainable-with-large-language-models-74f119c7e036?source=collection_archive---------2-----------------------#2024-11-30)

## 通过基于提示的实验，提升内容个性化推荐的准确性和透明推理。

[](https://medium.com/@helloheld?source=post_page---byline--74f119c7e036--------------------------------)[![Alex Held](../Images/be76f042807c4816944531780d14a73d.png)](https://medium.com/@helloheld?source=post_page---byline--74f119c7e036--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--74f119c7e036--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--74f119c7e036--------------------------------) [Alex Held](https://medium.com/@helloheld?source=post_page---byline--74f119c7e036--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--74f119c7e036--------------------------------) ·阅读时间：7分钟·2024年11月30日

--

![](../Images/ff1026c0fe89bf7dceb16a6d47f1dc3f.png)

在合适的时间向读者提供相关内容。图片来自作者。

在 [DER SPIEGEL](https://www.spiegel.de/) ，我们不断探索改进如何向读者推荐新闻文章的方法。在我们最新的（离线）实验中，我们研究了 [大型语言模型](https://vickiboykis.com/what_are_embeddings/)（LLMs）是否能够有效地根据读者的阅读历史预测他们可能感兴趣的文章。

**我们的方法**

我们进行了一项研究，邀请参与者填写调查问卷，评估他们对各种新闻文章的兴趣。这为我们提供了关于读者偏好的真实数据。对于每个参与者，我们有两个关键信息：他们的实际阅读历史（即在填写调查问卷前他们阅读过哪些文章）以及他们在调查中对一组新文章的评分。了解更多关于这种混合方法的离线新闻推荐系统评估方法，请点击这里：

[](/a-mixed-methods-approach-to-offline-evaluation-of-news-recommender-systems-7dc7e9f0b501?source=post_page-----74f119c7e036--------------------------------) [## 混合方法离线评估新闻推荐系统

### 结合调查反馈与行为点击数据，优化内容个性化推荐。

towardsdatascience.com](/a-mixed-methods-approach-to-offline-evaluation-of-news-recommender-systems-7dc7e9f0b501?source=post_page-----74f119c7e036--------------------------------)

然后我们使用了[Anthropic API](https://github.com/anthropics/anthropic-sdk-python)来访问[Claude 3.5 Sonnet](https://www.anthropic.com/news/claude-3-5-sonnet)，一个最先进的语言模型，作为我们的推荐引擎。对于每个读者，我们为模型提供了他们的阅读历史（新闻标题和文章摘要），并要求模型预测他们对调查中这些文章的兴趣程度。以下是我们使用的提示：

```py
You are a news recommendation system. Based on the user's reading history, 
predict how likely they are to read new articles. Score each article from 0 to 1000, 
where 1000 means highest likelihood to read.

Reading history (Previous articles read by the user):
[List of previously read articles with titles and summaries]

Please rate the following articles (provide a score 0-1000 for each):
[List of candidate articles to rate]

You must respond with a JSON object in this format:
{
    "recommendations": [
        {
            "article_id": "article-id-here",
            "score": score
        }
    ]
}
```

通过这种方法，我们现在可以将调查中的实际评分与LLM的预测分数进行比较。这一比较提供了一个理想的数据集，用于评估语言模型预测读者兴趣的能力。

**结果与关键发现**

研究结果令人印象深刻。为了理解表现，我们可以看两个关键指标。首先是[Precision@5](https://www.evidentlyai.com/ranking-metrics/precision-recall-at-k)：LLM（大语言模型）获得了56%的得分，这意味着当系统向用户推荐前5篇文章时（从15篇中选出），平均（几乎）有3篇是用户在调查中评分最高的文章。查看这些预测的分布可以揭示出更加令人印象深刻的结果：对于24%的用户，系统正确识别出至少4或5篇他们最喜欢的文章；对于另外41%的用户，系统正确识别出他们前5篇文章中的3篇。

为了将其置于正确的视角中，如果我们随机推荐文章，我们的精准度将仅为38.8%（详细信息请参见之前的[medium文章](https://medium.com/towards-data-science/a-mixed-methods-approach-to-offline-evaluation-of-news-recommender-systems-7dc7e9f0b501)）。即使是基于文章流行度的推荐（推荐大多数人阅读的内容）也只有42.1%，而我们之前使用基于嵌入的方法的精准度为45.4%。

![](../Images/73b93682f37bf1eb4cdc35c9c5a9c262.png)

作者图表

以下图表展示了提升效果：尽管了解用户的任何信息比纯粹猜测（随机模型）要好，但基于LLM的方法展示了最强的表现。即使与我们复杂的基于嵌入的逻辑相比，LLM在预测准确性上也实现了显著提升。

![](../Images/944e6bf25ecad03090641f39d1448a3b.png)

作者图表

作为第二个评估指标，我们使用了[Spearman相关系数](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.spearmanr.html)。其值为0.41，显著高于我们基于嵌入的方法（0.17）。这也表明，LLM不仅在找到相关文章方面表现更好，还能更好地理解读者可能偏爱某篇文章超过另一篇文章的程度。

**超越表现：可解释性的力量**

基于LLM的推荐与众不同之处不仅在于其表现，还在于它们能够以自然语言解释其决策。以下是我们的系统如何分析用户阅读模式并解释其推荐的一个示例（提示未展示）：

```py
User has 221 articles in reading history

Top 5 Comparison:
--------------------------------------------------------------------------------

Top 5 Predicted by Claude:
1\. Wie ich mit 38 Jahren zum ersten Mal lernte, strukturiert zu arbeiten (Score: 850, Actual Value: 253.0)
2\. Warum wir den Umgang mit der Sonne neu lernen müssen (Score: 800, Actual Value: 757.0)
3\. Lohnt sich ein Speicher für Solarstrom vom Balkon? (Score: 780, Actual Value: 586.0)
4\. »Man muss sich fragen, ob dieser spezielle deutsche Weg wirklich intelligent ist« (Score: 750, Actual Value: 797.0)
5\. Wie Bayern versucht, sein Drogenproblem unsichtbar zu machen (Score: 720, Actual Value: 766.0)

Actual Top 5 from Survey:
4\. »Man muss sich fragen, ob dieser spezielle deutsche Weg wirklich intelligent ist« (Value: 797.0, Predicted Score: 750)
5\. Wie Bayern versucht, sein Drogenproblem unsichtbar zu machen (Value: 766.0, Predicted Score: 720)
2\. Warum wir den Umgang mit der Sonne neu lernen müssen (Value: 757.0, Predicted Score: 800)
6\. Abitur als Lotterie? (Value: 601.0, Predicted Score: 650)
3\. Lohnt sich ein Speicher für Solarstrom vom Balkon? (Value: 586.0, Predicted Score: 780)

Performance Metrics:
--------------------------------------------------------------------------------
Spearman Correlation: 0.673
Precision@5: 0.800

Claude's Analysis:
--------------------------------------------------------------------------------
1\. Key patterns in user's reading history:
- Strong interest in social and political issues
- Regular engagement with lifestyle and personal development content
- Interest in health and wellness topics
- Frequent reading of articles about economic and financial matters
- Attraction to critical analysis and commentary pieces
- Interest in food and cooking content

2\. Scoring explanation:

Highest scored articles:
- "Wie ich mit 38 Jahren zum ersten Mal lernte, strukturiert zu arbeiten" (850) - Aligns with the user's interest in personal development and lifestyle content
- "Warum wir den Umgang mit der Sonne neu lernen müssen" (800) - Matches interest in health and wellness topics
- "Lohnt sich ein Speicher für Solarstrom vom Balkon?" (780) - Corresponds to interest in practical advice and economic considerations

Medium scored articles:
- "Man muss sich fragen, ob dieser spezielle deutsche Weg wirklich intelligent ist" (750) - Fits pattern of interest in political commentary
- "Wie Bayern versucht, sein Drogenproblem unsichtbar zu machen" (720) - Matches interest in social issues and critical reporting
- "Abitur als Lotterie?" (650) - Aligns with interest in educational and social topics

Lower scored articles:
- "Eine Brise Formel 1" (550) - Limited sports content in reading history
- "Reizender Absatz" (450) - Less alignment with demonstrated interests
- "Hier wird jetzt auf ganz, ganz hohem Niveau gemeckert" (400) - Style and topic less aligned with user preferences

The scoring prioritizes articles that match the user's demonstrated interests in social issues, practical advice, and critical analysis while giving lower scores to sports and lighter content that appears less frequently in their reading history.
```

系统并非作为一个黑盒运行，而是可以阐明为何认为某篇文章可能对读者感兴趣：*因为你经常阅读关于实用建议和经济问题的文章，你可能会发现这篇关于阳台太阳能储能性价比分析的文章特别相关。* 这种透明的推理可以让推荐看起来更加个性化和值得信赖。

**结论**

尽管我们的结果很有前景，但仍然需要解决若干挑战。由于长篇提示（每个用户包含数百个文章摘要），最大的挑战是成本。每个用户的单次推荐运行费用约为$0.21，若将其扩展到全部读者群体，将变得极为昂贵。测试高效的[开源模型](https://ai.meta.com/blog/meta-llama-3-1/)，可能会减少这些成本。此外，当前的实现相对较慢，每个用户需要几秒钟的时间。对于一个内容更新频繁、读者兴趣可能在一天内发生变化的新闻平台，我们需要每天多次运行这些推荐，才能保持其相关性。

此外，我们使用了一个简单直接的提示，没有进行任何提示工程或优化。通过系统性的提示优化，可能会有（显著的）提升空间。[1] 此外，我们目前的实现仅使用了文章标题和摘要，未利用现有的元数据。我们可以通过引入其他信号，如每篇文章的阅读时间（用户每篇文章的阅读时长）或整体文章的受欢迎程度，潜在地提高性能。不过，由于高昂的API费用，目前进行迭代评估管道并不是一个可行的选项。

总的来说，强大的预测性能和自然语言解释相结合，表明大型语言模型（LLMs）将在新闻推荐系统中发挥重要作用。而且，除了推荐，它们还为我们分析数字新闻中的用户旅程提供了全新的方式。它们能够处理并解释阅读历史与元数据的结合，打开了令人兴奋的可能性：从理解内容的流向和主题的进展，到创建个性化的回顾总结。

## **感谢阅读 🙏**

希望你喜欢它，如果喜欢，就给它点个赞吧。如果有进一步的讨论或问题，请随时通过[LinkedIn与我联系](https://www.linkedin.com/in/alex-held-1193b9234/)。

作为[DER SPIEGEL](https://www.spiegel.de/)的 数据科学家，我已获得对专有用户数据和点击历史的授权访问权限，这些数据构成了本研究的基础。此数据不公开。所有展示的结果均已汇总和匿名化，以保护用户隐私，同时展示我们在新闻推荐中的方法论。

## 参考文献

[1] Dairui, Liu & Yang, Boming & Du, Honghui & Greene, Derek & Hurley, Neil & Lawlor, Aonghus & Dong, Ruihai & Li, Irene. (2024). RecPrompt: 一种自调节提示框架，利用大型语言模型进行新闻推荐。
