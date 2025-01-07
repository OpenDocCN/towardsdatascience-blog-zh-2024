# 像数据科学家一样构建提示：使用DSPy进行自动提示优化和测试

> 原文：[https://towardsdatascience.com/prompt-like-a-data-scientist-auto-prompt-optimization-and-testing-with-dspy-ff699f030cb7?source=collection_archive---------0-----------------------#2024-05-05](https://towardsdatascience.com/prompt-like-a-data-scientist-auto-prompt-optimization-and-testing-with-dspy-ff699f030cb7?source=collection_archive---------0-----------------------#2024-05-05)

## 应用机器学习方法构建提示

[](https://medium.com/@jyipkl?source=post_page---byline--ff699f030cb7--------------------------------)[![Julian Yip](../Images/2afc0ac6c4dcccaa57ffe70b2f5a14d0.png)](https://medium.com/@jyipkl?source=post_page---byline--ff699f030cb7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ff699f030cb7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ff699f030cb7--------------------------------) [Julian Yip](https://medium.com/@jyipkl?source=post_page---byline--ff699f030cb7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ff699f030cb7--------------------------------) ·40分钟阅读·2024年5月5日

--

![](../Images/266f734fa2fbe701359eb41fa9d06e9d.png)

作者绘制

LLMs的基础是数据科学，但我们对提示工程的处理方式可能显得不够科学：

1.  **不易泛化的手动提示工程**：大型语言模型（LLMs）对每个任务的提示非常敏感，因此我们需要手工编写长字符串的指令和示范。这不仅需要耗时的提示写作过程，而且给定的字符串提示可能无法在不同的管道、不同的语言模型（LMs）、数据领域甚至输入之间泛化。为了应对新问题，我们通常需要手工编写新的提示。

1.  **缺乏进行测试的框架**：与典型数据科学应用中的常规训练-测试模式不同，通常是通过选择最大化某个指标（如AUC）的模型，而在LLMs中，我们是通过反复试验找到最佳提示，通常没有客观指标来评估我们的模型表现如何。因此，无论我们如何尝试改进提示，都无法自信地说我们的应用有多可靠。

为了解决这些问题，斯坦福NLP发布了一篇[论文](https://arxiv.org/abs/2310.03714)，介绍了一种新的提示编写方法：我们不再操作自由格式的字符串，而是通过模块化编程生成提示。相关库DSPy可以在[这里](https://github.com/stanfordnlp/dspy)找到。

本文旨在展示如何进行这种“提示编程”，并深入解释优化过程背后发生的事情。代码也可以在[这里](https://github.com/yip-kl/llm_dspy_tutorial)找到。

*(说到这，你可能会发现让LLM输出格式正确的JSON也非常不科学，我也写了一篇关于如何通过函数调用解决这个问题的文章。快去看看吧！)*

[](/build-autonomous-ai-agents-with-function-calling-0bb483753975?source=post_page-----ff699f030cb7--------------------------------) [## 使用函数调用构建自主AI代理

### 将你的聊天机器人转变为一个能够与外部API交互的代理

towardsdatascience.com](/build-autonomous-ai-agents-with-function-calling-0bb483753975?source=post_page-----ff699f030cb7--------------------------------)

我们将花些时间来讲解环境准备。之后，本文分为三个部分：

1.  ***DSPy的基本概念：签名和模块***

    用于描述任务的DSPy基本构建模块，以及使用的提示技术

1.  ***优化器：像机器学习一样训练我们的提示***

    DSPy如何通过自举优化你的提示

1.  ***完整示例：与LLM的提示比较***

    将传统机器学习的严谨性应用于提示测试和选择

我们现在准备好开始了！

# 准备工作

1.  前往[Github](https://github.com/yip-kl/llm_dspy_tutorial)克隆我的代码。我的文章中的内容可以在`dspy_tutorial` Notebook中找到。

1.  请同时创建并激活一个虚拟环境，然后运行`pip install -r requirements.txt`来安装所需的包。如果你在Windows系统上，还请安装Windows C++构建工具，这对于我们将要使用的`phoneix`库是必需的，它能帮助我们观察DSPy的工作方式。

1.  我的代码使用了OpenRouter，这使我们能够在封锁地区访问OpenAI API。请将你的`OPENROUTER_API_KEY`设置为环境变量，并在“准备”块下执行代码。或者，你也可以直接使用`dspy.OpenAI`类并定义OpenAI API密钥，如果它适合你。

# DSPy的基本概念：签名和模块

它们是DSPy提示编程的构建块。让我们深入了解它们的内容吧！

## **签名：输入/输出的规范**

签名是DSPy提示编程中最基本的构建模块，它是对DSPy模块输入/输出行为的声明性规范。签名允许你告诉语言模型**需要**做什么，而不是指定如何请求语言模型去做。

假设我们想要获取一句话的情感，传统上我们可能会写出这样的提示：

```py
Given a sentence {the_sentence_itself}, deduce its sentiment.
```

但是在DSPy中，我们可以通过如下定义一个`签名`来实现相同的效果。最基本的形式，签名只是一个简单的字符串，用`->`分隔输入和输出。

*注意：本节代码包含了来自DSPy文档中* [*签名*](https://dspy-docs.vercel.app/docs/building-blocks/signatures) 的相关内容。

```py
# Define signature
signature = 'sentence -> sentiment'
classify = dspy.Predict(signature)

# Run
sentence = "it's a charming and often affecting journey."
classify(sentence=sentence).sentiment
```

```py
--- Output ---
"I'm sorry, but I am unable to determine the sentiment of the sentence without additional context or information. If you provide me with more details or specific criteria for determining sentiment, I would be happy to assist you further."
```

预测结果不好，但为了教学目的，让我们检查一下所发出的提示是什么。

```py
# This is how we inpect the last issued prompt to the LM
lm.inspect_history(n=1)
```

```py
--- Output ---
**Given the fields `sentence`, produce the fields `sentiment`.

---

Follow the following format.

Sentence: ${sentence}
Sentiment: ${sentiment}**

---

Sentence: it's a charming and often affecting journey.
Sentiment: I'm sorry, but I am unable to determine the sentiment of the sentence without additional context or information. If you provide me with more details or specific criteria for determining sentiment, I would be happy to assist you further.
```

我们可以看到，上面的提示是从`sentence -> sentiment`签名拼接而来的。但DSPy是如何得出提示中的`Given the fields…`部分的呢？

检查`dspy.Predict()`类时，我们看到当我们将签名传递给它时，签名会被解析为该类的`signature`属性，随后被组装成提示。`instructions`是DSPy库中硬编码的默认值。

```py
# Check the variables of the `classify` object,
# which was created by passing the signature to `dspy.Predict()` class
vars(classify)
```

```py
--- Output ---
{
 **'signature': StringSignature(sentence -> sentiment
     instructions='Given the fields `sentence`, produce the fields `sentiment`.'
     sentence = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'input', 'prefix': 'Sentence:', 'desc': '${sentence}'})
     sentiment = Field(annotation=str required=True json_schema_extra={'__dspy_field_type': 'output', 'prefix': 'Sentiment:', 'desc': '${sentiment}'})
 )**,
 'some_other_attributes': 'xxx'}
```

如果我们想要向LLM提供更详细的目标描述，而不仅仅是基本的`sentence -> sentiment`签名，怎么办？为此，我们需要提供更详细的签名，形式为**基于类的DSPy签名**。

请注意，我们没有明确指示LLM应如何获取情感。我们只是描述了当前的任务，以及预期的输出。

```py
# Define signature in Class-based form
class Emotion(dspy.Signature):
    # Describe the task
    """Classify emotions in a sentence."""

    sentence = dspy.InputField()
    # Adding description to the output field
    sentiment = dspy.OutputField(desc="Possible choices: sadness, joy, love, anger, fear, surprise.")

classify_class_based = dspy.Predict(Emotion)

# Issue prediction
classify_class_based(sentence=sentence).sentiment
```

```py
--- Output ---
Sentence: It's a charming and often affecting journey.
Sentiment: **joy**
```

它现在输出了一个更好的预测！我们再次看到，在定义基于类的DSPy签名时，我们所做的描述被组装成了提示。

```py
**Classify emotions in a sentence.**

---

Follow the following format.

Sentence: ${sentence}
**Sentiment: Possible choices: sadness, joy, love, anger, fear, surprise.**

---

Sentence: it's a charming and often affecting journey.
Sentiment: Sentence: It's a charming and often affecting journey.
Sentiment: joy
```

这对于简单任务可能足够了，但高级应用可能需要复杂的提示技巧，如Chain of Thought或ReAct。在DSPy中，这些都作为**模块**实现。

## **模块：抽象提示技巧**

我们可能已经习惯通过硬编码诸如`let’s think step by step`这样的短语来应用“提示技巧”在我们的提示中。在DSPy中，这些提示技巧被抽象为**模块**。下面我们来看一个将基于类的签名应用于`dspy.ChainOfThought`模块的示例。

```py
# Apply the class-based signature to Chain of Thought
classify_cot = dspy.ChainOfThought(Emotion)

# Run
classify_cot(sentence=sentence).sentiment

# Inspect prompt
lm.inspect_history(n=1)
```

```py
--- Output ---
Classify emotions in a sentence.

---

Follow the following format.

Sentence: ${sentence}
**Reasoning: Let's think step by step in order to ${produce the sentiment}. We ...**
Sentiment: Possible choices: sadness, joy, love, anger, fear, surprise.

---

Sentence: it's a charming and often affecting journey.
Reasoning: Let's think step by step in order to Sentence: It's a charming and often affecting journey.
**Reasoning: Let's think step by step in order to determine the sentiment. The use of the words "charming" and "affecting" suggests positive emotions associated with enjoyment and emotional impact. We can infer that the overall tone is positive and heartwarming, evoking feelings of joy and possibly love.**
Sentiment: Joy, love
```

请注意，“推理：让我们逐步思考...”这一短语是如何添加到我们的提示中的，现在我们的预测质量更高了。

根据DSPy的[文档](https://dspy-docs.vercel.app/docs/building-blocks/modules)，截至本文写作时，DSPy提供了以下形式为模块的提示技巧。请注意，我们在初始示例中使用的`dspy.Predict`也是一个模块，代表没有提示技巧！

1.  `dspy.Predict`：基本的预测器。不会修改签名。处理学习的关键形式（即存储指令和示范以及对LM的更新）。

1.  `dspy.ChainOfThought`：教会LM在给出签名的回应之前，逐步思考。

1.  `dspy.ProgramOfThought`：教会LM输出代码，其执行结果将决定响应。

1.  `dspy.ReAct`：一个可以使用工具来执行给定签名的智能体。

1.  `dspy.MultiChainComparison`：可以比较ChainOfThought的多个输出，以产生最终预测。

它也有一些类似函数的模块：

6. `dspy.majority`：可以进行基本的投票，从一组预测中返回最受欢迎的响应。

你可以在[每个模块的相关指南](https://dspy-docs.vercel.app/api/category/modules)中查看更多示例。

## 链接模块

另一方面，RAG怎么办？我们可以将模块串联在一起，处理更大的问题！

首先，我们定义一个检索器，在我们的示例中，我们使用ColBERT检索器从维基百科摘要2017年中获取信息。

```py
# Configure retriever
rm = dspy.ColBERTv2(url='http://20.102.90.50:2017/wiki17_abstracts')
dspy.settings.configure(rm = rm)
```

然后，我们定义继承自`dspy.Module`的`RAG`类。它需要两个方法：

+   `__init__`方法将简单声明它需要的子模块：`dspy.Retrieve`和`dspy.ChainOfThought`。后者被定义为实现我们的`context, question -> answer`签名。

+   `forward`方法将描述使用我们已有的模块来回答问题的控制流程。

*注意：本部分的代码借用自* [*DSPy简介笔记本*](https://github.com/stanfordnlp/dspy/blob/main/intro.ipynb)

```py
# Define a class-based signature
class GenerateAnswer(dspy.Signature):
    """Answer questions with short factoid answers."""

    context = dspy.InputField(desc="may contain relevant facts")
    question = dspy.InputField()
    answer = dspy.OutputField(desc="often between 1 and 5 words")

# Chain different modules together to retrieve information from Wikipedia Abstracts 2017, then pass it as context for Chain of Thought to generate an answer
class RAG(dspy.Module):
    def __init__(self, num_passages=3):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=num_passages)
        self.generate_answer = dspy.ChainOfThought(GenerateAnswer)

    def forward(self, question):
        context = self.retrieve(question).passages
        answer = self.generate_answer(context=context, question=question)
        return answer
```

然后，我们利用这个类来执行RAG。

```py
# Initilize our RAG class
rag = RAG()

# Define a question and pass it into the RAG class
my_question = "When was the first FIFA World Cup held?"
rag(question=my_question).answer
```

```py
--- Output ---
'1930'
```

检查提示时，我们发现从维基百科摘要2017年中检索的三段文本被交替作为链式思维生成的上下文。

```py
Answer questions with short factoid answers.

---

Follow the following format.

Context: may contain relevant facts

Question: ${question}

Reasoning: Let's think step by step in order to ${produce the answer}. We ...

Answer: often between 1 and 5 words

---

**Context:
[1] «History of the FIFA World Cup | The FIFA World Cup was first held in 1930, when FIFA president Jules Rimet decided to stage an international football tournament. The inaugural edition, held in 1930, was contested as a final tournament of only thirteen teams invited by the organization. Since then, the World Cup has experienced successive expansions and format remodeling to its current 32-team final tournament preceded by a two-year qualifying process, involving over 200 teams from around the world.»
[2] «1950 FIFA World Cup | The 1950 FIFA World Cup, held in Brazil from 24 June to 16 July 1950, was the fourth FIFA World Cup. It was the first World Cup since 1938, the planned 1942 and 1946 competitions having been cancelled owing to World War II. It was won by Uruguay, who had won the inaugural competition in 1930, clinching the cup by beating the hosts Brazil 2–1 in the deciding match of the four-team final group (this was the only tournament not decided by a one-match final). It was also the first tournament where the trophy was referred to as the Jules Rimet Cup, to mark the 25th anniversary of Jules Rimet's presidency of FIFA.»
[3] «1970 FIFA World Cup | The 1970 FIFA World Cup was the ninth FIFA World Cup, the quadrennial international football championship for men's national teams. Held from 31 May to 21 June in Mexico, it was the first World Cup tournament staged in North America, and the first held outside Europe and South America. Teams representing 75 nations from all six populated continents entered the competition, and its qualification rounds began in May 1968\. Fourteen teams qualified from this process to join host nation Mexico and defending champions England in the sixteen-team final tournament. El Salvador, Israel, and Morocco made their first appearances at the final stage, and Peru their first since 1930.»**

Question: When was the first FIFA World Cup held?

Reasoning: Let's think step by step in order to Answer: 1930

Answer: 1930
```

上面的例子看起来可能不算什么。在最基本的应用中，DSPy似乎只是做了一些用f-string也能做到的事情，但实际上，它为提示写作带来了范式的转变，因为它为提示组成引入了**模块化**！

首先，我们用`Signature`描述我们的目标，然后使用`Modules`应用不同的提示技巧。为了测试不同的提示技巧，我们可以简单地切换使用的模块并比较它们的结果，而不是硬编码“让我们一步步思考……”（对于链式思维）或“你将交替进行思考、行动和观察步骤”（对于ReAct）这样的短语。模块化的好处将在本文后面通过一个完整的示例进行演示。

DSPy的强大之处不仅限于模块化，它还可以根据训练样本优化我们的提示，并系统地进行测试。我们将在下一部分深入探讨！

# **优化器：像机器学习一样训练我们的提示**

在本节中，我们尝试使用DSPy优化我们的提示，以便应用于RAG（检索增强生成）应用。

以链式思维为例，除了仅仅添加“让我们一步步思考”这句话外，我们还可以通过一些调整来提升其表现：

1.  添加适当的示例（即**少量学习**）。

1.  此外，我们还可以**启动推理演示**，教导大规模语言模型如何应用恰当的推理来处理当前任务。

手动执行这个过程将非常耗时，且无法推广到不同的问题，但借助DSPy，这一切都可以自动完成。让我们深入了解一下！

## 准备工作

**#1：加载测试数据**：像机器学习一样，为了训练我们的提示，我们需要准备训练数据和测试数据集。最初，这个单元大约需要20分钟才能运行完毕。

```py
from dspy.datasets.hotpotqa import HotPotQA

# For demonstration purpose we will use a small subset of the HotPotQA dataset, 20 for training and testing each
dataset = HotPotQA(train_seed=1, train_size=20, eval_seed=2023, dev_size=20, test_size=0)
trainset = [x.with_inputs('question') for x in dataset.train]
testset = [x.with_inputs('question') for x in dataset.dev]

len(trainset), len(testset)
```

检查我们的数据集，它基本上是一组问答对。

```py
Example({'question': 'At My Window was released by which American singer-songwriter?', 'answer': 'John Townes Van Zandt'}) (input_keys={'question'})
```

**#2 设置Phoenix以进行可观察性**：为了便于理解优化过程，我们启动**Phoenix**来观察我们的DSPy应用，这是一个非常适合大规模语言模型（LLM）可观察性的工具！我将跳过在这里粘贴代码，但你可以在笔记本中执行它。

注意：如果你使用的是 Windows，请同时安装 Windows C++ Build Tools，[点击这里](https://visualstudio.microsoft.com/visual-cpp-build-tools/)，这是 Phoenix 所必需的。

## 提示优化

然后我们准备好看看这个优化到底是什么！为了“训练”我们的提示，我们需要3样东西：

1.  一个训练集。我们将只使用来自`trainset`的20个问答示例。

1.  一个验证指标。这里我们使用原生的`dspy.evaluate.answer_exact_match`，它检查预测的答案是否完全匹配正确答案（虽然值得怀疑，但足以用于演示）。对于实际应用，你可以定义自己的评估标准。

1.  一个特定的**优化器**（前身为提词器）。DSPy 库包含了多种优化策略，你可以在[这里](https://dspy-docs.vercel.app/docs/building-blocks/optimizers)查看它们。对于我们的例子，我们使用`BootstrapFewShot`。我将通过代码演示，而不是在这里用冗长的描述来解释它。

现在我们开始训练我们的提示。

```py
from dspy.teleprompt import BootstrapFewShot

# Simple optimizer example. I am explicitly stating the default values for max_bootstrapped_demos and max_labeled_demos for demonstration purposes
optimizer = BootstrapFewShot(metric=dspy.evaluate.answer_exact_match, max_bootstrapped_demos=4)

# Compile!
compiled_rag = optimizer.compile(RAG(), trainset=trainset)
```

```py
--- Successful execution should show this output ---
Bootstrapped 4 full traces after n examples in round 0
```

在使用`compiled_rag`回答问题之前，让我们看看在训练过程中（即编译过程中）发生了什么。我们通过在浏览器中访问`http://localhost:6006/`来启动 Phoenix 控制台。

![](../Images/d94f1dbe53ce5b4aef6a0ea2796a406a.png)

在“编译”过程中进行了14次调用。

在我的运行中，我使用`RAG`类进行了14次调用，每次调用时我们都会向语言模型发送一个问题以获取预测结果。

请参阅我的笔记本中的结果摘要表，从这14个样本中得出了4个正确答案，因此达到了我们的`max_bootstrapped_demos`参数，并停止了调用。

那么，DSPy 发出的提示是什么，用以获得引导的演示？这是问题 #14 的提示。我们可以看到，当 DSPy 尝试生成一个引导演示时，它会随机从我们的`trainset`中添加样本进行少量学习。

```py
Answer questions with short factoid answers.

---

**{Pairs of question-and-answer as samples}**

---

Follow the following format.

Context: may contain relevant facts

Question: ${question}

Reasoning: Let's think step by step in order to ${produce the answer}. We ...

Answer: often between 1 and 5 words

---

Context:
[1] «Eric Davis (baseball) | Eric Keith Davis (born May 29, 1962) is a former center fielder for several Major League Baseball teams. Davis was 21 years old when he broke into the big leagues on May 19, 1984 with the Cincinnati Reds, the team for which he is most remembered. Blessed with a rare combination of excellent foot speed and bat speed, Davis became the first major league player to hit at least 30 home runs and steal at least 50 bases in the same season in 1987.»
[2] «Willie Davis (baseball) | William Henry Davis, Jr. (April 15, 1940 – March 9, 2010) was a center fielder in Major League Baseball who played most of his career for the Los Angeles Dodgers. At the end of his career he ranked seventh in major league history in putouts (5449) and total chances (5719) in the outfield, and third in games in center field (2237). He was ninth in National League history in total outfield games (2274), and won Gold Glove Awards from 1971 to 1973\. He had 13 seasons of 20 or more stolen bases, led the NL in triples twice, and retired with the fourth most triples (138) by any major leaguer since 1945\. He holds Los Angeles club records (1958–present) for career hits (2091), runs (1004), triples (110), at bats (7495), total bases (3094) and extra base hits (585). His 31-game hitting streak in 1969 remains the longest by a Dodger. At one point during the streak, when the team was playing at home, the big message board at Dodger Stadium quoted a message from a telegram sent to Davis and the team from Zack Wheat, the team's former record holder, at his home in Missouri.»
[3] «1992 Los Angeles Dodgers season | The 1992 Los Angeles Dodgers season was a poor one for the team as it finished last in the Western Division of the National League with a record of 63 wins and 99 losses. Despite boasting what was nicknamed the "Outfield of Dreams", being manned by Eric Davis, Brett Butler, and Darryl Strawberry, injuries to key players and slumps from others contributed to the franchise's worst season since moving to Los Angeles. Additionally, the Dodgers cancelled four home games during the season due to the L.A. Riots. Despite the poor finish, the Dodgers had some hope for the future as first baseman Eric Karros won the National League Rookie of the Year Award, the first of five consecutive Dodger players to do so. The 1992 season also saw the Dodgers drop television station KTTV Ch.11 as their chief broadcaster of Dodger baseball, ending a 34 year-35 consecutive season association with that station. Additionally, it was the first time the Dodgers lost 90 games in a season since 1944.»

Question: Having the combination of excellent foot speed and bat speed helped Eric Davis, create what kind of outfield for the Los Angeles Dodgers?

Reasoning: Let's think step by step in order to Answer: "Outfield of Dreams"

Answer: "Outfield of Dreams"
```

是时候测试一下`compiled_rag`了！我们提出一个在总结表中被错误回答的问题，看看这次是否能得到正确的答案。

```py
compiled_rag(question="Which of these publications was most recently published, Who Put the Bomp or Self?")
```

```py
--- Output ---
Prediction(
    rationale='Answer: Self',
    answer='Self'
)
```

我们现在得到了正确的答案！

再次查看发出的提示。注意编译后的提示与引导过程中使用的提示有何不同。除了少量示例之外，**引导的上下文-问题-推理-答案演示（来自正确的预测）被添加到提示中**，从而提高了语言模型的能力。

```py
Answer questions with short factoid answers.

---

{Pairs of question-and-answer as samples}

---

Follow the following format.

Context: may contain relevant facts

Question: ${question}

Reasoning: Let's think step by step in order to ${produce the answer}. We ...

Answer: often between 1 and 5 words

---

**{4 sets of Context-Question-Reasoning-Answer demonstrations}**

---

Context:
[1] «Who Put the Bomp | Who Put The Bomp was a rock music fanzine edited and published by Greg Shaw from 1970 to 1979\. Its name came from the hit 1961 doo-wop song by Barry Mann, "Who Put the Bomp". Later, the name was shortened to "Bomp!"»
[2] «Bompiani | Bompiani is an Italian publishing house based in Milan, Italy. It was founded in 1929 by Valentino Bompiani.»
[3] «What Color is Your Parachute? | What Color is Your Parachute? by Richard Nelson Bolles is a book for job-seekers that has been in print since 1970 and has been revised every year since 1975, sometimes substantially. Bolles initially self-published the book (December 1, 1970), but it has been commercially published since November 1972 by Ten Speed Press in Berkeley, California. As of September 28, 2010, the book is available in 22 languages, it is used in 26 countries around the world, and over ten million copies have been sold worldwide. It is one of the most highly regarded career advice books in print. In the latest edition of the book, the author writes about how to adapt one's job search to the Web 2.0 age.»

Question: Which of these publications was most recently published, Who Put the Bomp or Self?

Reasoning: Let's think step by step in order to Answer: Self

Answer: Self
```

所以下面的内容基本上是在编译过程中，`BootstrapFewShot`在后台完成的工作：

![](../Images/843ab35d315078ee9a3d523c13b6f5a5.png)

引导演示以增强提示

上面的例子仍然无法满足我们通常在机器学习中所做的事情：即使引导可能有用，我们还没有证明它能提高响应的质量。

理想情况下，像传统机器学习一样，我们应该定义几个候选模型，查看它们在测试集上的表现，并选择表现最好的那个。这就是我们接下来要做的！

# **完整示例：与LLM的提示比较**

## 这个示例的目的是

在本节中，我们希望评估**（通过模块与优化器组合的方式）**哪个是进行RAG的“最佳提示”，并使用我们所用的语言模型（GPT 3.5 Turbo），对[HotpotQA数据集](https://hotpotqa.github.io/)（按[CC BY-SA 4.0许可证](http://creativecommons.org/licenses/by-sa/4.0/)分发）进行评估。

正在评估的模块如下：

+   **原生**：基于检索的上下文回答问题的单跳RAG，没有像“让我们一步一步思考”这样的关键短语

+   **COT**：带有思维链的单跳RAG

+   **ReAct**：带有ReAct提示的单跳RAG

+   **BasicMultiHop**：带有思维链的2跳RAG

优化器候选项如下：

+   **无**：除了签名外没有额外的指令

+   **标注少样本**：仅通过提供的标注问答对构造少样本示例

+   **Bootstrap少样本**：如我们所示，为我们模块的每个阶段自生成完整的示范。如果示范通过了评估指标，我们将直接使用这些生成的示范（无需进一步优化）。对于`原生`来说，它就等同于“标注少样本”

至于评估指标，我们再次使用精确匹配作为标准（`dspy.evaluate.metrics.answer_exact_match`）对测试集进行评估。

## 比较

我们开始吧！首先，我们定义我们的模块

```py
# Vanilla
class Vanilla(dspy.Module):
    def __init__(self, num_passages=3):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=num_passages)
        self.generate_answer = dspy.Predict("context, question -> answer")

    def forward(self, question):
        context = self.retrieve(question).passages
        answer = self.generate_answer(context=context, question=question)
        return answer

vanilla = Vanilla()

# COT
class COT(dspy.Module):
    def __init__(self, num_passages=3):
        super().__init__()
        self.retrieve = dspy.Retrieve(k=num_passages)
        self.generate_answer = dspy.ChainOfThought("context, question -> answer")

    def forward(self, question):
        context = self.retrieve(question).passages
        answer = self.generate_answer(context=context, question=question)
        return answer

cot = COT()

# ReAct
react = dspy.ReAct("question-> answer", tools=[dspy.Retrieve(k=3)], max_iters=5)

# BasicMultiHop
class BasicMultiHop(dspy.Module):
    def __init__(self, passages_per_hop=3):
        self.retrieve = dspy.Retrieve(k=passages_per_hop)
        self.generate_query = dspy.ChainOfThought("context, question-> search_query")
        self.generate_answer = dspy.ChainOfThought("context, question-> answer")

    def forward(self, question):
        context = []

        for hop in range(2):
            query = self.generate_query(context=context, question=question).search_query
            context += self.retrieve(query).passages

        return self.generate_answer(context=context, question=question)

multihop = BasicMultiHop(passages_per_hop=3)
```

然后定义我们模型候选项的排列

```py
from dspy.teleprompt import LabeledFewShot, BootstrapFewShot

metric = dspy.evaluate.metrics.answer_exact_match

modules = {
    'vanilla': vanilla,
    'cot': cot,
    'react': react,
    'multihop': multihop,
}

optimizers = {
    'none': None,
    'labeled_few_shot': LabeledFewShot(),
    'bootstrap_few_shot': BootstrapFewShot(metric=metric, max_errors=20),
}
```

然后我定义了一个帮助类来简化评估过程。代码稍长，所以我没有在这里粘贴，但它可以在我的笔记本中找到。它的作用是对每个优化器与模块组合进行应用，编译提示，然后在测试集上进行评估。

我们现在准备开始评估，预计大约需要20分钟完成

```py
# Compile the models
ms = ModelSelection(modules=modules, optimizers=optimizers, metric=metric, trainset=trainset)

# Evaluate them
ms.evaluate(testset=testset)
```

这是评估结果。我们可以看到，`COT`模块与`BootstrapFewShot`优化器的表现最佳。分数表示测试集中正确答案的百分比（通过精确匹配判断）。

![](../Images/5d270eb34c7f0f594923555c9e6499f9.png)

但在我们得出结论之前，可能需要更深入地检查结果：**Multihop与BootstrapFewShot**，据说比**COT与BootstrapFewShot**提供更多相关上下文，但其表现更差，真是奇怪！

## 调试并微调我们的提示

现在前往Phoenix控制台，看看发生了什么。我们选择一个随机问题`William Hughes Miller出生在一个有多少居民的城市？`，并检查COT、ReAct、BasicMultiHop与BootstrapFewShot优化器是如何得出答案的。你可以在搜索栏中输入以下内容进行筛选：`"""William Hughes Miller出生在一个有多少居民的城市？""" in input.value`

![](../Images/588fb6cbfd28970cd79b6918e2a6f614.png)

调用按顺序进行，因此对于每个模块，我们可以通过选择第三个调用来选择BootstrapFewShot变体

这些是我运行过程中3个模型提供的答案：

+   **Multihop with BootstrapFewShot**: `答案将根据 William Hughes Miller 出生城市的具体情况而有所不同。`

+   **ReAct with BootstrapFewShot**: `Kosciusko, Mississippi`

+   **COT with BootstrapFewShot**: `Kosciusko, Mississippi 市的人口大约为 7,402 人。`

正确答案是 `2010年普查时为7,402人`。**ReAct with BootstrapFewShot** 和 **COT with BootstrapFewShot** 都提供了相关答案，但 **Multihop with BootstrapFewShot** 简直没有提供任何答案。

在 Phoenix 中检查 Multihop with BootstrapFewShot 的执行轨迹，似乎语言模型无法理解在**签名**中指定的 `search_query` 所期望的内容。

![](../Images/0e1e7217ff54735470d988cac17fea8f.png)

在第一次跳跃期间，语言模型无法生成 search_query。

所以我们修订了签名，并使用下面的代码重新运行评估。

```py
# Define a class-based signature
class GenerateAnswer(dspy.Signature):
    """Answer questions with short factoid answers."""

    context = dspy.InputField(desc="may contain relevant facts")
    question = dspy.InputField()
    answer = dspy.OutputField(desc="often between 1 and 5 words")

class BasicQA(dspy.Signature):
    """Answer questions with short factoid answers."""

    question = dspy.InputField()
    answer = dspy.OutputField(desc="often between 1 and 5 words")

class FollowupQuery(dspy.Signature):
    """Generate a query which is conducive to answering the question"""

    context = dspy.InputField(desc="may contain relevant facts")
    question = dspy.InputField()
    search_query = dspy.OutputField(desc="Judge if the context is adequate to answer the question, if not adequate or if it is blank, generate a search query that would help you answer the question.")
```

```py
# Revise the modules with the class-based signatures. You can find the relevant code in my notebook
# To keep the article concise I am not pasting it here.

# Then run the below command to re-compile and evaluate
ms_revised = ModelSelection(modules=modules_revised, optimizers=optimizers, metric=metric, trainset=trainset)
ms_revised.evaluate(testset=testset)
ms_revised.evaluation_matrix
```

![](../Images/09f52de40780a66ef1cad4de15b306b6.png)

更新签名后，性能有所提升。

现在我们看到所有模型的得分都有所提高，并且 **Multihop with LabeledFewShot** 和 **Multihop with no examples** 现在表现最好！这表明，尽管 DSPy 尝试优化提示，**仍然需要通过在签名中明确阐述目标来进行一些提示工程。**

*注意：即使是签名本身也可以通过 DSPy 的* [*COPRO*](https://dspy-docs.vercel.app/docs/deep-dive/teleprompter/signature-optimizer)*进行优化！但本文不会深入探讨 COPRO，因为那样可能太多信息难以消化。*

现在最好的模型能准确匹配我们的提问！

```py
# The correct answer is 7,402
question = """`William Hughes Miller was born in a city with how many inhabitants ?"""
ms_revised.question_for_model('multihop','labeled_few_shot',question)
```

```py
--- Output ---
Prediction(
    rationale='Answer: 7,402',
    answer='7,402'
)
```

由于最佳的提示是 Multihop with LabeledFewShot，因此提示中不包含自举的上下文-问题-推理-答案示例。因此，自举可能并不一定带来更好的性能，**我们需要科学地证明哪种提示是最好的。**

```py
Answer questions with short factoid answers.

---

**{Pairs of question-and-answer as samples}**

---

Follow the following format.

Context: may contain relevant facts

Question: ${question}

Reasoning: Let's think step by step in order to ${produce the answer}. We ...

Answer: often between 1 and 5 words

---

Context:
[1] «William Hughes Miller | William Hughes Miller (born March 16, 1941, Kosciusko, Mississippi) is a professor at the University of California, Berkeley and a leading researcher in the field of theoretical chemistry.»
[2] «William Herbert Miller, Jr. | William Hubert Miller, Jr. (September 1932 – November 4, 1988), of New York City, was an aerophilatelist who published philatelic literature on the subject.»
[3] «William Green Miller | William Green Miller (born August 15, 1931 in New York City, New York), served as the United States Ambassador to Ukraine under Bill Clinton, from 1993 to 1998.»
[4] «Kosciusko, Mississippi | Kosciusko is a city in Attala County, Mississippi, United States. The population was 7,402 at the 2010 census. It is the county seat of Attala County.»
[5] «Attala County, Mississippi | Attala County is a county located in the U.S. state of Mississippi. As of the 2010 census, the population was 19,564\. Its county seat is Kosciusko. Attala County is named for Atala, a fictional Native American heroine from an early-19th-century novel of the same name by François-René de Chateaubriand.»
[6] «Kosciusko Island | Kosciusko Island is an island in the Alexander Archipelago of southeastern Alaska, United States. It lies near the northwest corner of Prince of Wales Island, just across the El Capitan Passage from the larger island. The island is near Mount Francis, Holbrook Mountain, and Tokeen Peak. Kosciusko Island has a land area of 171.585 sq mi (444.403 km²), making it the 38th largest island in the United States. It had a population of 52 persons as of the 2000 census, mostly in Edna Bay, its largest community.»

Question: `William Hughes Miller was born in a city with how many inhabitants ?

Reasoning: Let's think step by step in order to Answer: 7,402

Answer: 7,402
```

然而，这并不意味着 **Multihop with BootstrapFewShot** 在**总体**表现上较差。只是对于我们的任务来说，如果我们使用 GPT 3.5 Turbo 进行自举演示（其质量可能值得怀疑）并生成预测，那么我们可能最好不使用自举，而只保留少量示例。

这引出了一个问题：是否有可能使用更强大的语言模型，比如 GPT 4 Turbo（即 `教师`），来生成示范，同时保持像 GPT 3.5 Turbo（即 `学生`）这样的较便宜的模型用于预测？

## **“教师”增强自举能力**

答案是**YES**，正如下一个单元格所示，我们将使用 GPT 4 Turbo 作为教师。

```py
# Define the GPT-4 Turbo model
gpt4_turbo = dspy.Databricks(api_key=OPENROUTER_API_KEY,
  api_base="https://openrouter.ai/api/v1",
  model="openai/gpt-4-turbo")

# Define new Optimizer which uses GPT-4 Turbo as a teacher
optimizers_gpt4_teacher = {
    'bootstrap_few_shot': BootstrapFewShot(metric=metric, max_errors=20, teacher_settings=dict(lm=gpt4_turbo)),
}

# Compile the models and evaluate them as before
ms_gpt4_teacher = ModelSelection(modules=modules_revised, optimizers=optimizers_gpt4_teacher, metric=metric, trainset=trainset)
ms_gpt4_teacher.evaluate(testset=testset)
ms_gpt4_teacher.evaluation_matrix
```

![](../Images/4d747518e8a217acfc20102d3ffef23c.png)

使用 GPT-4 作为教师的结果

然而，使用 GPT-4 Turbo 作为 `教师` 并没有显著提升我们模型的性能。不过，看看它对提示的影响仍然是值得的。以下是仅使用 GPT 3.5 生成的提示。

```py
Answer questions with short factoid answers.

---

{Pairs of question-and-answer as samples}

---

Follow the following format.

Context: may contain relevant facts

Question: ${question}

Reasoning: Let's think step by step in order to ${produce the answer}. We ...

Answer: often between 1 and 5 words

---

**Context:
[1] «Candace Kita | Kita's first role was as a news anchor in the 1991 movie "Stealth Hunters". Kita's first recurring television role was in Fox's "Masked Rider", from 1995 to 1996\. She appeared as a series regular lead in all 40 episodes. Kita also portrayed a frantic stewardess in a music video directed by Mark Pellington for the British group, Catherine Wheel, titled, "Waydown" in 1995\. In 1996, Kita also appeared in the film "Barb Wire" (1996) and guest starred on "The Wayans Bros.". She also guest starred in "Miriam Teitelbaum: Homicide" with "Saturday Night Live" alumni Nora Dunn, "Wall To Wall Records" with Jordan Bridges, "Even Stevens", "Felicity" with Keri Russell, "V.I.P." with Pamela Anderson, "Girlfriends", "The Sweet Spot" with Bill Murray, and "Movies at Our House". She also had recurring roles on the FX spoof, "Son of the Beach" from 2001 to 2002, ABC-Family's "Dance Fever" and Oxygen Network's "Running with Scissors". Kita also appeared in the films "Little Heroes" (2002) and "Rennie's Landing" (2001).»
[2] «Jilly Kitzinger | Jilly Kitzinger is a fictional character in the science fiction series "Torchwood", portrayed by American actress Lauren Ambrose. The character was promoted as one of five new main characters to join "Torchwood" in its fourth series, "" (2011), as part of a new co-production between "Torchwood"' s British network, BBC One, and its American financiers on US premium television network Starz. Ambrose appears in seven of the ten episodes, and is credited as a "special guest star" throughout. Whilst reaction to the serial was mixed, Ambrose' portrayal was often singled out by critics for particular praise and in 2012 she received a Saturn Award nomination for Best Supporting Actress on Television.»
[3] «Candace Brown | Candace June Brown (born June 15, 1980) is an American actress and comedian best known for her work on shows such as "Grey's Anatomy", "Desperate Housewives", "Head Case", The "Wizards Of Waverly Place". In 2011, she joined the guest cast for "Torchwood"' s fourth series' "", airing on BBC One in the United Kingdom and premium television network Starz.»
[4] «Candace Kita | Kita's first role was as a news anchor in the 1991 movie "Stealth Hunters". Kita's first recurring television role was in Fox's "Masked Rider", from 1995 to 1996\. She appeared as a series regular lead in all 40 episodes. Kita also portrayed a frantic stewardess in a music video directed by Mark Pellington for the British group, Catherine Wheel, titled, "Waydown" in 1995\. In 1996, Kita also appeared in the film "Barb Wire" (1996) and guest starred on "The Wayans Bros.". She also guest starred in "Miriam Teitelbaum: Homicide" with "Saturday Night Live" alumni Nora Dunn, "Wall To Wall Records" with Jordan Bridges, "Even Stevens", "Felicity" with Keri Russell, "V.I.P." with Pamela Anderson, "Girlfriends", "The Sweet Spot" with Bill Murray, and "Movies at Our House". She also had recurring roles on the FX spoof, "Son of the Beach" from 2001 to 2002, ABC-Family's "Dance Fever" and Oxygen Network's "Running with Scissors". Kita also appeared in the films "Little Heroes" (2002) and "Rennie's Landing" (2001).»
[5] «Kiti Manver | María Isabel Ana Mantecón Vernalte (born 11 May 1953) better known as Kiti Mánver is a Spanish actress. She has appeared in more than 100 films and television shows since 1970\. She starred in the 1973 film "Habla, mudita", which was entered into the 23rd Berlin International Film Festival.»
[6] «Amy Steel | Amy Steel (born Alice Amy Steel; May 3, 1960) is an American film and television actress. She is best known for her roles as Ginny Field in "Friday the 13th Part 2" (1981) and Kit Graham in "April Fool's Day" (1986). She has starred in films such as "Exposed" (1983), "Walk Like a Man" (1987), "What Ever Happened to Baby Jane? " (1991), and "Tales of Poe" (2014). Steel has had numerous guest appearances on several television series, such as "Family Ties" (1983), "The A-Team" (1983), "Quantum Leap" (1990), and "China Beach" (1991), as well as a starring role in "The Powers of Matthew Star" (1982–83).»

Question: which American actor was Candace Kita guest starred with

Reasoning: Let's think step by step in order to Answer: Bill Murray

Answer: Bill Murray

---

Context:
[1] «Monthly Magazine | The Monthly Magazine (1796–1843) of London began publication in February 1796\. Richard Phillips was the publisher and a contributor on political issues. The editor for the first ten years was the literary jack-of-all-trades, Dr John Aikin. Other contributors included William Blake, Samuel Taylor Coleridge, George Dyer, Henry Neele and Charles Lamb. The magazine also published the earliest fiction of Charles Dickens, the first of what would become "Sketches by Boz".»
[2] «Bodega Magazine | Bodega Magazine is an online literary magazine that releases new issues on the first Monday of every month, featuring stories, poems, essays and interviews from a mix of emerging and established writers. It was founded in early spring of 2012 by creative writing MFA graduates from New York University who had previously worked together on the "Washington Square Review", and continues to be based out of Manhattan and Brooklyn. The inaugural issue was published on September 4, 2012.»
[3] «Who Put the Bomp | Who Put The Bomp was a rock music fanzine edited and published by Greg Shaw from 1970 to 1979\. Its name came from the hit 1961 doo-wop song by Barry Mann, "Who Put the Bomp". Later, the name was shortened to "Bomp!"»
[4] «The Most (album) | The Most is the third album released by straight edge hardcore punk band Down to Nothing. It was released on July 17, 2007.»
[5] «The Most Incredible Thing | “The Most Incredible Thing" (Danish: "Det Utroligste" ) is a literary fairy tale by Danish poet and author Hans Christian Andersen (1805–1875). The story is about a contest to find the most incredible thing and the wondrous consequences when the winner is chosen. The tale was first published in an English translation by Horace Scudder, an American correspondent of Andersen's, in the United States in September 1870 before being published in the original Danish in Denmark in October 1870\. "The Most Incredible Thing" was the first of Andersen's tales to be published in Denmark during World War II. Andersen considered the tale one of his best.»
[6] «Augusta Triumphans | Augusta Triumphans: or, the Way to Make London the Most Flourishing City in the Universe by Daniel Defoe was first published on 16 March 1728\. The fictitious speaker of this pamphlet, Andrew Moreton, is a man in his sixties who offers suggestions for the improvement of London. In particular, he fosters the establishment of a university, an academy of music, a hospital for foundlings and licensed institutions for the treatment of mental diseases. Moreover, he encourages the introduction of measures to prevent moral corruption and street robbery.»

Question: Which of these publications was most recently published, Who Put the Bomp or Self?

Reasoning: Let's think step by step in order to Answer: Self

Answer: Self

---

Context:
[1] «The Victorians | The Victorians - Their Story In Pictures is a 2009 British documentary series which focuses on Victorian art and culture. The four-part series is written and presented by Jeremy Paxman and debuted on BBC One at 9:00pm on Sunday 15 February 2009.»
[2] «What the Victorians Did for Us | What the Victorians Did for Us is a 2001 BBC documentary series that examines the impact of the Victorian era on modern society. It concentrates primarily on the scientific and social advances of the era, which bore the Industrial Revolution and set the standards for polite society today.»
[3] «The Great Victorian Collection | The Great Victorian Collection, published in 1975, is a novel by Northern Irish-Canadian writer Brian Moore. Set in Carmel, California, it tells the story of a man who dreams that the empty parking lot he can see from his hotel window has been transformed by the arrival of a collection of priceless Victoriana on display in a vast open-air market. When he awakes he finds that he can no longer distinguish the dream from reality.»
[4] «Jeremy Paxman | Jeremy Dickson Paxman (born 11 May 1950) is an English broadcaster, journalist, and author. He is the question master of "University Challenge", having succeeded Bamber Gascoigne when the programme was revived in 1994.»
[5] «Jeremy I | Jeremy I was king of the Miskito nation, who came to power following the death of his father, Oldman, in 1686 or 1687\. according to an English visitor, W. M., in 1699, he was about 60 years old at that time, making his birth year about 1639.»
[6] «Jeremy Cheeseman | Jeremy Cheeseman (born June 6, 1990 in Manorville, New York) is a former American professional soccer player. Playing two seasons for the Dayton Dutch Lions in the USL Professional Division before retiring due to injury»

Question: The Victorians - Their Story In Pictures is a documentary series written by an author born in what year?

Reasoning: Let's think step by step in order to Answer: 1950

Answer: 1950

---

Context:
[1] «Tae Kwon Do Times | Tae Kwon Do Times is a magazine devoted to the martial art of taekwondo, and is published in the United States of America. While the title suggests that it focuses on taekwondo exclusively, the magazine also covers other Korean martial arts. "Tae Kwon Do Times" has published articles by a wide range of authors, including He-Young Kimm, Thomas Kurz, Scott Shaw, and Mark Van Schuyver.»
[2] «Scott Shaw (artist) | Scott Shaw (often spelled Scott Shaw!) is a United States cartoonist and animator, and historian of comics. Among Scott's comic-book work is Hanna-Barbera's "The Flintstones" (for Marvel Comics and Harvey Comics), "Captain Carrot and His Amazing Zoo Crew" (for DC Comics), and "Simpsons Comics" (for Bongo Comics). He was also the first artist for Archie Comics' "Sonic the Hedgehog" comic book series.»
[3] «Scott Shaw | Scott Shaw (born September 23, 1958) is an American actor, author, film director, film producer, journalist, martial artist, musician, photographer, and professor.»
[4] «Scott Shaw (artist) | Scott Shaw (often spelled Scott Shaw!) is a United States cartoonist and animator, and historian of comics. Among Scott's comic-book work is Hanna-Barbera's "The Flintstones" (for Marvel Comics and Harvey Comics), "Captain Carrot and His Amazing Zoo Crew" (for DC Comics), and "Simpsons Comics" (for Bongo Comics). He was also the first artist for Archie Comics' "Sonic the Hedgehog" comic book series.»
[5] «Scott Shaw | Scott Shaw (born September 23, 1958) is an American actor, author, film director, film producer, journalist, martial artist, musician, photographer, and professor.»
[6] «Arnold Shaw (author) | Arnold Shaw (1909–1989) was a songwriter and music business executive, primarily in the field of music publishing, who is best known for his comprehensive series of books on 20th century American popular music.»

Question: Which magazine has published articles by Scott Shaw, Tae Kwon Do Times or Southwest Art?

Reasoning: Let's think step by step in order to Answer: Tae Kwon Do Times

Answer: Tae Kwon Do Times**

---

Context:
[1] «William Hughes Miller | William Hughes Miller (born March 16, 1941, Kosciusko, Mississippi) is a professor at the University of California, Berkeley and a leading researcher in the field of theoretical chemistry.»
[2] «William Herbert Miller, Jr. | William Hubert Miller, Jr. (September 1932 – November 4, 1988), of New York City, was an aerophilatelist who published philatelic literature on the subject.»
[3] «William Rickarby Miller | William Rickarby Miller (May 20, 1818 in Staindrop – July 1893 in New York City) was an American painter, of the Hudson River School.»
[4] «Kosciusko, Mississippi | Kosciusko is a city in Attala County, Mississippi, United States. The population was 7,402 at the 2010 census. It is the county seat of Attala County.»
[5] «Attala County, Mississippi | Attala County is a county located in the U.S. state of Mississippi. As of the 2010 census, the population was 19,564\. Its county seat is Kosciusko. Attala County is named for Atala, a fictional Native American heroine from an early-19th-century novel of the same name by François-René de Chateaubriand.»
[6] «Kosciusko Island | Kosciusko Island is an island in the Alexander Archipelago of southeastern Alaska, United States. It lies near the northwest corner of Prince of Wales Island, just across the El Capitan Passage from the larger island. The island is near Mount Francis, Holbrook Mountain, and Tokeen Peak. Kosciusko Island has a land area of 171.585 sq mi (444.403 km²), making it the 38th largest island in the United States. It had a population of 52 persons as of the 2000 census, mostly in Edna Bay, its largest community.»

Question: `William Hughes Miller was born in a city with how many inhabitants ?

Reasoning: Let's think step by step in order to Answer: 7,402

Answer: 7,402
```

这是使用 GPT-4 Turbo 作为`教师`生成的提示。请注意，“推理”部分在这里阐述得更为清晰！

```py
Answer questions with short factoid answers.

---

{Pairs of question-and-answer as samples}

---

Follow the following format.

Context: may contain relevant facts

Question: ${question}

Reasoning: Let's think step by step in order to ${produce the answer}. We ...

Answer: often between 1 and 5 words

---

**Context:
[1] «Monthly Magazine | The Monthly Magazine (1796–1843) of London began publication in February 1796\. Richard Phillips was the publisher and a contributor on political issues. The editor for the first ten years was the literary jack-of-all-trades, Dr John Aikin. Other contributors included William Blake, Samuel Taylor Coleridge, George Dyer, Henry Neele and Charles Lamb. The magazine also published the earliest fiction of Charles Dickens, the first of what would become "Sketches by Boz".»
[2] «Who Put the Bomp | Who Put The Bomp was a rock music fanzine edited and published by Greg Shaw from 1970 to 1979\. Its name came from the hit 1961 doo-wop song by Barry Mann, "Who Put the Bomp". Later, the name was shortened to "Bomp!"»
[3] «Desktop Publishing Magazine | Desktop Publishing magazine (ISSN 0884-0873) was founded, edited, and published by Tony Bove and Cheryl Rhodes of TUG/User Publications, Inc., of Redwood City, CA. ) . Its first issue appeared in October, 1985, and was created and produced on a personal computer with desktop publishing software (PageMaker on a Macintosh), preparing output on a prototype PostScript-driven typesetting machine from Mergenthaler Linotype Company. Erik Sandberg-Diment, a columnist at "The New York Times", tried to buy the venture outright when he saw an early edition.»
[4] «Self (magazine) | Self is an American magazine for women that specializes in health, wellness, beauty, and style. Part of Condé Nast, Self had a circulation of 1,515,880 and a total audience of 5,282,000 readers, according to its corporate media kit n 2013\. The editor-in-chief is Carolyn Kylstra. "Self" is based in the Condé Nast U.S. headquarters at 1 World Trade Center in New York, NY. In February 2017 the magazine became an online publication.»
[5] «Self-Publishing Review | Self-Publishing Review (or "SPR") is an online book review magazine for indie authors founded in 2008 by American author Henry Baum.»
[6] «Self-publishing | Self-publishing is the publication of any book, album or other media by its author without the involvement of an established publisher. A self-published physical book is said to have been privately printed. The author is in control of the entire process including, for a book, the design of the cover and interior, formats, price, distribution, marketing, and public relations. The authors can do it all themselves or may outsource some or all of the work to companies which offer these services.»

Question: Which of these publications was most recently published, Who Put the Bomp or Self?

Reasoning: Let's think step by step in order to determine which publication was most recently published. According to the context, "Who Put the Bomp" was published from 1970 to 1979\. On the other hand, "Self" magazine became an online publication in February 2017 after being a print publication. Therefore, "Self" was most recently published.

Answer: Self

---

Context:
[1] «The Victorians | The Victorians - Their Story In Pictures is a 2009 British documentary series which focuses on Victorian art and culture. The four-part series is written and presented by Jeremy Paxman and debuted on BBC One at 9:00pm on Sunday 15 February 2009.»
[2] «The Great Victorian Collection | The Great Victorian Collection, published in 1975, is a novel by Northern Irish-Canadian writer Brian Moore. Set in Carmel, California, it tells the story of a man who dreams that the empty parking lot he can see from his hotel window has been transformed by the arrival of a collection of priceless Victoriana on display in a vast open-air market. When he awakes he finds that he can no longer distinguish the dream from reality.»
[3] «Victorian (comics) | The Victorian is a 25-issue comic book series published by Penny-Farthing Press and starting in 1999\. The brainchild of creator Trainor Houghton, the series included a number of notable script writers and illustrators, including Len Wein, Glen Orbik and Howard Chaykin.»
[4] «Jeremy Paxman | Jeremy Dickson Paxman (born 11 May 1950) is an English broadcaster, journalist, and author. He is the question master of "University Challenge", having succeeded Bamber Gascoigne when the programme was revived in 1994.»
[5] «Jeremy I | Jeremy I was king of the Miskito nation, who came to power following the death of his father, Oldman, in 1686 or 1687\. according to an English visitor, W. M., in 1699, he was about 60 years old at that time, making his birth year about 1639.»
[6] «Jeremy Cheeseman | Jeremy Cheeseman (born June 6, 1990 in Manorville, New York) is a former American professional soccer player. Playing two seasons for the Dayton Dutch Lions in the USL Professional Division before retiring due to injury»

Question: The Victorians - Their Story In Pictures is a documentary series written by an author born in what year?

Reasoning: Let's think step by step in order to determine the birth year of the author who wrote "The Victorians - Their Story In Pictures." According to context [4], Jeremy Paxman, an English broadcaster and journalist, wrote and presented this documentary series. His birth year is provided in the same context.

Answer: 1950

---

Context:
[1] «Tae Kwon Do Times | Tae Kwon Do Times is a magazine devoted to the martial art of taekwondo, and is published in the United States of America. While the title suggests that it focuses on taekwondo exclusively, the magazine also covers other Korean martial arts. "Tae Kwon Do Times" has published articles by a wide range of authors, including He-Young Kimm, Thomas Kurz, Scott Shaw, and Mark Van Schuyver.»
[2] «Kwon Tae-man | Kwon Tae-man (born 1941) was an early Korean hapkido practitioner and a pioneer of the art, first in Korea and then in the United States. He formed one of the earliest dojang's for hapkido in the United States in Torrance, California, and has been featured in many magazine articles promoting the art.»
[3] «Scott Shaw (artist) | Scott Shaw (often spelled Scott Shaw!) is a United States cartoonist and animator, and historian of comics. Among Scott's comic-book work is Hanna-Barbera's "The Flintstones" (for Marvel Comics and Harvey Comics), "Captain Carrot and His Amazing Zoo Crew" (for DC Comics), and "Simpsons Comics" (for Bongo Comics). He was also the first artist for Archie Comics' "Sonic the Hedgehog" comic book series.»
[4] «Tae Kwon Do Times | Tae Kwon Do Times is a magazine devoted to the martial art of taekwondo, and is published in the United States of America. While the title suggests that it focuses on taekwondo exclusively, the magazine also covers other Korean martial arts. "Tae Kwon Do Times" has published articles by a wide range of authors, including He-Young Kimm, Thomas Kurz, Scott Shaw, and Mark Van Schuyver.»
[5] «Scott Savitt | Scott Savitt is a former foreign correspondent for The Los Angeles Times and United Press International in Beijing. His articles have been published in The Los Angeles Times, Washington Post http://www.washingtonpost.com/wp-dyn/content/article/2008/04/18/AR2008041802635.html, Wall Street Journal, New York Times, and many other publications.»
[6] «Scott Poulson-Bryant | Scott Poulson-Bryant is an award-winning American journalist and author. One of the co-founding editors of Vibe magazine in 1992 (and the editor who gave the magazine its name), Poulson-Bryant's journalism, profiles, reviews, and essays have appeared in such publications as the "New York Times", "the Village Voice", "Rolling Stone", "Spin", "Essence", "Ebony", and "The Source". He is the author of "HUNG: A Meditation on the Measure of Black Men in America" (published by Doubleday Books in 2006) and a novel called "The VIPs".»

Question: Which magazine has published articles by Scott Shaw, Tae Kwon Do Times or Southwest Art?

Reasoning: Let's think step by step in order to determine which magazine published articles by Scott Shaw. According to the context provided, Scott Shaw has contributed to "Tae Kwon Do Times," which is mentioned in both [1] and [4]. There is no mention of Scott Shaw contributing to "Southwest Art."

Answer: Tae Kwon Do Times

---

Context:
[1] «1972 FA Charity Shield | The 1972 FA Charity Shield was contested between Manchester City and Aston Villa.»
[2] «1968 FA Charity Shield | The 1968 FA Charity Shield was a football match played on 3 August 1968 between Football League champions Manchester City and FA Cup winners West Bromwich Albion. It was the 46th Charity Shield match and was played at City's home ground, Maine Road. Manchester City won 6–1.»
[3] «1973 FA Charity Shield | The 1973 FA Charity Shield was contested between Burnley and Manchester City in a fixture that took place at Maine Road.»
[4] «List of Aston Villa F.C. seasons | This is a list of seasons played by Aston Villa Football Club in English and European football, from 1879 (the year of the club's first FA Cup entry) to the most recent completed season. Aston Villa football club was founded in March, 1874, by members of the Villa Cross Wesleyan Chapel in Aston. Throughout the 1870s Aston Villa played a small amount of games. At least one game, against Aston Brook St Mary's was played with one half under Rugby rules and the other under football rules. In the 1880s the game became more formalised and in 1888, William McGregor formed the Football League with 11 other clubs.»
[5] «List of Aston Villa F.C. records and statistics | Aston Villa Football Club are an English professional association football club based in Aston, Birmingham, who currently play in the EFL Championship. The club was founded in 1874 and have played at their current home ground, Villa Park, since 1897\. Aston Villa were founding members of the Football League in 1888 and the Premier League in 1992\. They are one of the oldest and most successful football clubs in England, having won the First Division Championship seven times and the FA Cup seven times. In 1982 the club became one of only five English clubs to win the European Cup.»
[6] «Aston Villa F.C. | Aston Villa Football Club ( ; nicknamed Villa, The Villa, The Villans and The Lions) is a professional football club in Aston, Birmingham, that plays in the Championship, the second level of English football. Founded in 1874, they have played at their current home ground, Villa Park, since 1897\. Aston Villa were one of the founder members of the Football League in 1888 and of the Premier League in 1992.»

Question: In what year was the club founded that played Manchester City in the 1972 FA Charity Shield

Reasoning: Let's think step by step in order to determine the founding year of the club that played against Manchester City in the 1972 FA Charity Shield. According to context [1], the match was contested between Manchester City and Aston Villa. To find the founding year of Aston Villa, we refer to context [4], which states that Aston Villa Football Club was founded in March, 1874.

Answer: 1874**

---

Context:
[1] «William Hughes Miller | William Hughes Miller (born March 16, 1941, Kosciusko, Mississippi) is a professor at the University of California, Berkeley and a leading researcher in the field of theoretical chemistry.»
[2] «William Read Miller | William Read Miller (November 23, 1823November 29, 1887) was the 12th Governor of the State of Arkansas. Born in Batesville, Arkansas; Miller was Arkansas's first native born Governor. Serving two terms in the turbulent period after Reconstruction, Miller's four-year administration marked the beginnings of New Departure Democrats in Arkansas. Running on a platform of economic growth via reconciliation between whites and freedmen, Miller often was opposed by members of his own party during the infancy of the Lost Cause ideology. His plans to pay back a large state debt including the Holford Bonds, valued at $14 million ($ million today), were often interrupted by racial violence, and his support for public schools and universities was often combated by those in his own party.»
[3] «William &quot;Willie&quot; Armstrong | William Armstrong was born c1804 in Painter Heugh (or Hugh), (which was an old lane dating from medieval Newcastle, a lane joining lower part of Dean Street to the higher part of Pilgrim Street), the name possibly derived from the fact that ships tied up here in the tidal parts of the Lort Burn (now filled).»
[4] «Kosciusko, Mississippi | Kosciusko is a city in Attala County, Mississippi, United States. The population was 7,402 at the 2010 census. It is the county seat of Attala County.»
[5] «Attala County, Mississippi | Attala County is a county located in the U.S. state of Mississippi. As of the 2010 census, the population was 19,564\. Its county seat is Kosciusko. Attala County is named for Atala, a fictional Native American heroine from an early-19th-century novel of the same name by François-René de Chateaubriand.»
[6] «Kosciusko Island | Kosciusko Island is an island in the Alexander Archipelago of southeastern Alaska, United States. It lies near the northwest corner of Prince of Wales Island, just across the El Capitan Passage from the larger island. The island is near Mount Francis, Holbrook Mountain, and Tokeen Peak. Kosciusko Island has a land area of 171.585 sq mi (444.403 km²), making it the 38th largest island in the United States. It had a population of 52 persons as of the 2000 census, mostly in Edna Bay, its largest community.»

Question: `William Hughes Miller was born in a city with how many inhabitants ?

Reasoning: Let's think step by step in order to Answer: 7,402

Answer: 7,402
```

# 结论

目前，我们常常依赖于手动的提示工程，通常以f-string的形式进行抽象。此外，在语言模型（LM）对比时，我们经常提出一些不够明确的问题，比如“不同的语言模型在某个问题上的表现如何”，这个问题来源于[斯坦福NLP论文](https://arxiv.org/abs/2310.03714)中的说法。

但正如上面的示例所展示的，通过DSPy的模块化、可组合程序和优化器，我们现在能够回答**“在与优化器Y编译后的模块X进行对比时，它们在某个问题上的表现如何”**，这是一个定义明确且可重复的运行，从而减少了在现代AI中巧妙提示构建的作用。

就这些！希望你喜欢这篇文章。

**除非另有说明，所有图片均为作者提供**
