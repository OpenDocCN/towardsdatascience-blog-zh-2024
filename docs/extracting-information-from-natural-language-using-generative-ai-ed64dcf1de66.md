# 使用生成式 AI 从自然语言中提取信息

> 原文：[https://towardsdatascience.com/extracting-information-from-natural-language-using-generative-ai-ed64dcf1de66?source=collection_archive---------5-----------------------#2024-05-03](https://towardsdatascience.com/extracting-information-from-natural-language-using-generative-ai-ed64dcf1de66?source=collection_archive---------5-----------------------#2024-05-03)

## 使用小型模型高精度地提取和结构化文本元素

[](https://medium.com/@orenmatar?source=post_page---byline--ed64dcf1de66--------------------------------)[![Oren Matar](../Images/8b1fa6aa3585fc283d51828b53a0754c.png)](https://medium.com/@orenmatar?source=post_page---byline--ed64dcf1de66--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ed64dcf1de66--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ed64dcf1de66--------------------------------) [Oren Matar](https://medium.com/@orenmatar?source=post_page---byline--ed64dcf1de66--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ed64dcf1de66--------------------------------) ·6 分钟阅读 ·2024年5月3日

--

![](../Images/12de85af31069135ae128e15c4e03a46.png)

由作者通过 AI 生成的图像

在这篇文章中，我将介绍最近在 Anaplan 开发的一个范式，用于从自然语言文本中提取时间信息，这是 NLQ（自然语言查询）项目的一部分。虽然我将重点讨论时间提取，但这个范式具有多功能性，适用于解析各种非结构化文本并提取不同类型的信息模式。包括命名实体识别、文本到 SQL 转换、数量提取等。

这个范式的核心在于构建一个灵活的流水线，它提供了最大的灵活性，使得微调模型来提取任何可以想象的语言表达的意义变得简单。它基于深度学习模型（transformers），但对我们来说，它实现了 99.98% 的准确率，这在机器学习方法中相对罕见。此外，它不使用大型语言模型（LLMs），实际上，它只需要一个最小的 transformer 模型。这生成了一个紧凑且适应性强的机器学习模型，展现了规则基础系统的精度。

对于那些需要提取时间、数值或电话号码的用户，Facebook 的 [Duckling 包](https://github.com/facebook/duckling)提供了一个基于规则的解决方案。但是，如果 Duckling 无法满足您的需求，或者您希望探索一个新的机器学习范式，继续阅读吧。

## LLMs 能否捕捉到意义？

尽管大语言模型（LLMs）具有强大的能力，但在解析这些短语并全面提取其含义时仍然面临挑战。考虑表达式“the first 15 weeks of last year”。将其转换为日期范围要求模型确定当前年份，减去一年，并根据闰年调整计算第15周的位置。语言模型并不是为了这种计算而设计的。

根据我的经验，大语言模型可以在90-95%的时间里准确输出正确的日期范围，但在剩下的5-10%情况下会遇到困难，无论你使用什么提示技巧。更不用说：大语言模型资源消耗大且速度较慢。

幸运的是，通过遵循三个原则，紧凑型变换器可以成功完成这个任务。

1.  将信息提取与逻辑推理分开。

1.  使用结构化模式自动生成数据集。

1.  将生成型AI限制为所需的结构。

在这篇文章中，我将介绍前两项，因为第三项我在[上一篇文章](https://medium.com/towards-data-science/structured-generative-ai-e772123428e4)中已经讨论过了。

## 将信息提取与逻辑推理分开。

第一个原则是确保语言模型的角色是从自由文本中提取信息，而不是进行任何逻辑推理：逻辑推理可以很容易地通过代码实现。

考虑这个短语：“How many movies came out two years ago？”语言模型的任务应该是识别出相关的年份是：`**this_year - 2**`，而不是计算实际年份（这意味着它不需要知道当前年份）。它的重点是解析意义并将非结构化语言进行结构化。一旦提取出这个公式，我们就可以在代码中实现其计算。

为了实现这一目标，我们引入了一种结构化时间语言（STL），能够表达时间元素。例如，“on 2020”翻译为“TIME.year==2020”，而“three months from now”则变成“NOW.month==3”。尽管本文没有详细介绍整个STL语言，但它应该是相对直观的：你可以引用像年份、季度和月份这样的属性来表示绝对时间或相对于NOW的时间。“last year’s last 12 weeks”的翻译是“NOW.year==-1 AND TIME.week>=-12”

通过将任务中的逻辑推理或计算去除，我们减轻了语言模型的负担，使其能够专注于信息提取。这种劳动分工将显著提高其准确性。翻译过程完成后，开发一个解析器的代码来读取结构化语言并检索所需的日期范围是非常简单的。

由于这是一个翻译任务——从自然语言到STL——我们使用了一个编码器-解码器变换器。我们使用了[Hugging Face的Bart模型](https://huggingface.co/docs/transformers/en/model_doc/bart)，它可以很容易地针对这个任务进行微调。

那么，我们如何获取用于训练模型的数据呢？

## **使用结构化模式自动生成数据集**

由于此翻译任务没有现成的训练数据集，我们必须自己生成。这个过程是通过以下步骤完成的：

*第一步*：编写函数，将日期时间对象映射到“自然语言”和STL格式：

```py
def since_year(datetime):
     free_text = f“since  {datetime.year}”
     answer = f”TIME.year >= {datetime.year}”
     return free_text, answer

def half_literal(datetime):
     free_text = datetime.strftime(“%-d, %B %Y”) 
     answer = f”TIME.date >= {datetime}”
     return free_text, answer

def until_quarter_year(datetime):
    q = datetime.month//3
    free_text = f”until Q{q}-{datetime.year}”
    answer = f”TIME.year=={datetime.year} AND TIME.quarter=={q}”
    return free_text, answer
```

给定一个日期时间对象，这些函数返回一个自由文本及其对应的STL元组，例如：“自2020年以来”，“TIME.year >= 2020”。

*第二步*：对一个随机函数进行采样，并在指定范围内采样一个随机日期：

```py
date = np.random.choice(pd.date_range('1970/1/1', '2040/12/31'))
```

现在将日期时间插入到函数中。

*第三步*：将自由文本附加到一个随机问题上（我们可以轻松地随机生成问题或从某个问题数据集中抽取，问题的质量和意义并不重要）。

使用这个管道，我们可以快速生成成千上万的文本-STL对，例如：

+   “2019年第二季度的GDP增长是多少？”、“TIME.quarter==2 AND TIME.year==2019”

+   “自2017年以来，谁赢得了最多的奥斯卡奖？”、“TIME.year>=2017”

+   “2020年5月3日的总统是谁？”、“TIME.date==2020/05/03”

这种方法确保了在添加新模式时的灵活性。如果你发现一个时间表达式没有被这些函数覆盖（例如“在N年后”），你可以编写一个函数，几秒钟内就能生成这个模式的示例。

在实践中，我们可以进一步优化代码效率。与其为每个模式（如“自2020年以来”和“直到2020年”）分别编写函数，不如随机采样连接词，如“自”、“直到”、“在”等。这个初步的函数集可能需要一些时间来开发，但你可以很快扩展到数百个模式。随后，解决任何缺失的表达式将变得微不足道，因为管道已经建立。通过几轮迭代，几乎所有相关的表达式都可以覆盖。

**此外，我们不需要覆盖所有表达式**：由于我们使用的变换模型已经在庞大的文本语料库上进行过预训练，它将从提供的模式中进行泛化，适应新的表达方式。

**最后，我们可以使用LLM来生成更多的示例**。只需向LLM提问：

```py
Hey, what's another way to write "What was the revenue until Aug 23"
```

它可能返回：

```py
"How much did we make before August 2023".
```

这个数据增强过程也可以自动化：将大量示例发送给LLM，从而为我们的数据集增加多样性。鉴于LLM的作用仅限于数据集创建，成本和速度的考量变得无关紧要。

**结合新增模式的灵活性、预训练模型的泛化能力，以及使用LLM的数据增强，我们可以有效地覆盖几乎所有的表达方式。**

这个范式的最终原则是将生成式AI限制为仅生成STL查询，确保遵循所需的结构。实现这一目标的方法，以及优化标记化过程的方法，已在[之前的文章](https://medium.com/towards-data-science/structured-generative-ai-e772123428e4)中讨论过。

通过遵循这三条原则，我们在测试数据集上达到了99.98%的令人印象深刻的准确率。此外，这一范式赋予了我们灵活性，能够迅速处理新的、未支持的时间表达式。

## **总结**

大型语言模型（LLMs）并不总是语言任务的最佳解决方案。采用正确的方法，较浅层的变压器模型能够高效、灵活地从自然语言中提取信息，同时减少时间和成本，且具有高准确性。

需要记住的关键原则是：

1.  将模型专注于信息提取，避免复杂的逻辑推理。这可能需要生成一个中介语言，并在代码中实现解析器和逻辑推理。

1.  建立一个数据集生成和模型训练的管道，使得添加新功能（新的语言模式）变得简单而快捷。这个管道可以包括使用大型语言模型（LLM），为数据集添加更多的多样性。

1.  将模型生成限制在结构化语言的约束之内。

虽然这篇文章主要关注提取时间元素，但这一范式适用于从自由文本中提取任何信息，并将其结构化为各种格式。通过这一范式，你可以实现规则引擎的精确性，同时具备机器学习模型的灵活性。
