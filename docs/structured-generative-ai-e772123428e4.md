# 结构化生成式 AI

> 原文：[https://towardsdatascience.com/structured-generative-ai-e772123428e4?source=collection_archive---------3-----------------------#2024-04-18](https://towardsdatascience.com/structured-generative-ai-e772123428e4?source=collection_archive---------3-----------------------#2024-04-18)

## 如何限制你的模型输出定义的格式

[](https://medium.com/@orenmatar?source=post_page---byline--e772123428e4--------------------------------)[![Oren Matar](../Images/8b1fa6aa3585fc283d51828b53a0754c.png)](https://medium.com/@orenmatar?source=post_page---byline--e772123428e4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e772123428e4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e772123428e4--------------------------------) [Oren Matar](https://medium.com/@orenmatar?source=post_page---byline--e772123428e4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e772123428e4--------------------------------) ·阅读时间 7 分钟·2024 年 4 月 18 日

--

在这篇文章中，我将解释并演示“结构化生成式 AI”的概念：即将生成式 AI 限制在定义的格式内。文章结束时，你将理解它的应用场景以及如何实现它，无论是从零开始构建一个变换器模型，还是使用 Hugging Face 的模型。此外，我们还将介绍一个与分词相关的重要技巧，特别适用于结构化语言。

生成式 AI 的许多用途之一是作为翻译工具。这通常涉及在人类语言之间的翻译，但也可以包括计算机语言或格式。例如，你的应用程序可能需要将自然语言（人类语言）翻译成 SQL：

```py
**Natural language**: “Get customer names and emails of customers from the US”

**SQL**: "SELECT name, email FROM customers WHERE country = 'USA'"
```

或者将文本数据转换为 JSON 格式：

```py
**Natural language**: “I am John Doe, phone number is 555–123–4567,
                   my friends are Anna and Sara”

**JSON**: {name: "John Doe",
       phone_number: "555–123–5678",
       friends: {
          name: [["Anna", "Sara"]]}
      }
```

自然地，其他结构化语言也可以有更多的应用。此类任务的训练过程包括将自然语言与结构化格式的示例输入到编码器-解码器模型中。或者，利用预训练的语言模型（LLM）也可以满足需求。

虽然实现 100% 准确率是不可能的，但有一类错误我们是可以消除的：语法错误。这些错误是对语言格式的违反，比如用点替代逗号，使用 SQL 模式中没有的表名，或者遗漏括号闭合，这些都会导致 SQL 或 JSON 无法执行。

我们正在翻译成结构化语言，这意味着在每一步生成过程中，合法令牌的列表是有限的并且是预定的。如果我们能够将这一知识注入到生成式 AI 过程中，就能避免许多不正确的结果。这就是结构化生成式 AI 的理念：将其限制为一组合法的令牌。

## **关于令牌生成的快速提醒**

无论是使用编码器-解码器架构还是GPT架构，令牌生成都是按顺序进行的。每个令牌的选择依赖于输入和之前生成的令牌，直到生成<end>令牌，标志着序列的完成。在每一步，分类器会为词汇表中的所有令牌分配logit值，表示每个令牌作为下一个选择的概率。接下来的令牌是基于这些logits进行采样的。

![](../Images/cad3a2ae8eae2f7150a70bacdecbaa82.png)

解码器分类器为词汇表中的每个令牌分配一个logit（图片由作者提供）

## **限制令牌生成**

为了约束令牌生成，我们结合了对输出语言结构的理解。不合法的令牌将其logits设置为-inf，确保它们不会被选中。例如，如果在“Select name”后只有逗号或“FROM”是合法的，那么所有其他令牌的logits都会被设置为-inf。

如果你使用Hugging Face，可以通过“logits处理器”实现这一点。要使用它，你需要实现一个包含__call__方法的类，该方法在计算logits后被调用，但在采样之前。此方法接收所有令牌logits和生成的输入ID，并返回所有令牌的修改后的logits。

![](../Images/e92f5fbfb0e5b222e3b5b4571ae33971.png)

从logits处理器返回的logits：所有不合法的令牌都会得到-inf的值（图片由作者提供）

我将通过一个简化的示例演示代码。首先，我们初始化模型，这里我们使用Bart模型，但任何模型都可以使用。

```py
from transformers import BartForConditionalGeneration, BartTokenizerFast, PreTrainedTokenizer
from transformers.generation.logits_process import LogitsProcessorList, LogitsProcessor
import torch

name = 'facebook/bart-large'
tokenizer = BartTokenizerFast.from_pretrained(name, add_prefix_space=True)
pretrained_model = BartForConditionalGeneration.from_pretrained(name)
```

如果我们想要生成从自然语言到SQL的翻译，可以运行：

```py
to_translate = 'customers emails from the us'
words = to_translate.split()
tokenized_text = tokenizer([words], is_split_into_words=True)

out = pretrained_model.generate(
    torch.tensor(tokenized_text["input_ids"]),
    max_new_tokens=20,
)
print(tokenizer.convert_tokens_to_string(
    tokenizer.convert_ids_to_tokens(
        out[0], skip_special_tokens=True)))
```

返回

```py
'More emails from the us'
```

由于我们没有针对文本到SQL任务对模型进行微调，因此输出不类似于SQL。在本教程中，我们不会训练模型，但我们会引导它生成SQL查询。我们将通过使用一个函数来实现这一点，该函数将每个生成的令牌映射到允许的下一个令牌列表。为了简化，我们仅关注紧接着的前一个令牌，但更复杂的机制也容易实现。我们将使用一个字典来定义每个令牌允许的后续令牌。例如，查询必须以“SELECT”或“DELETE”开始，在“SELECT”之后，仅允许“name”、“email”或“id”，因为这些是我们架构中的列。

```py
rules = {'<s>': ['SELECT', 'DELETE'], # beginning of the generation
 'SELECT': ['name', 'email', 'id'],  # names of columns in our schema
 'DELETE': ['name', 'email', 'id'],
 'name': [',', 'FROM'],
 'email': [',', 'FROM'],
 'id': [',', 'FROM'],
 ',': ['name', 'email', 'id'],
 'FROM': ['customers', 'vendors'],  # names of tables in our schema
 'customers': ['</s>'],
 'vendors': ['</s>'],  # end of the generation
}
```

现在我们需要将这些令牌转换为模型使用的ID。这将在一个继承自LogitsProcessor的类中完成。

```py
def convert_token_to_id(token):
    return tokenizer(token, add_special_tokens=False)['input_ids'][0]

class SQLLogitsProcessor(LogitsProcessor):
    def __init__(self, tokenizer: PreTrainedTokenizer):
        self.tokenizer = tokenizer
        self.rules = {convert_token_to_id(k): [convert_token_to_id(v0) for v0 in v] for k,v in rules.items()}
```

最后，我们将实现__call__函数，该函数在计算logits后被调用。此函数创建一个包含-infs的新张量，检查哪些ID符合规则（字典中的规则），并将其分数放入新张量中。结果是一个仅包含有效令牌的有效值的张量。

```py
class SQLLogitsProcessor(LogitsProcessor):
    def __init__(self, tokenizer: PreTrainedTokenizer):
        self.tokenizer = tokenizer
        self.rules = {convert_token_to_id(k): [convert_token_to_id(v0) for v0 in v] for k,v in rules.items()}

    def __call__(self, input_ids: torch.LongTensor, scores: torch.LongTensor):
        if not (input_ids == self.tokenizer.bos_token_id).any():
        # we must allow the start token to appear before we start processing
            return scores
        # create a new tensor of -inf
        new_scores = torch.full((1, self.tokenizer.vocab_size), float('-inf'))
        # ids of legitimate tokens
        legit_ids = self.rules[int(input_ids[0, -1])]
        # place their values in the new tensor
        new_scores[:, legit_ids] = scores[0, legit_ids]
        return new_scores
```

就这样！我们现在可以使用logits处理器进行生成：

```py
to_translate = 'customers emails from the us'
words = to_translate.split()
tokenized_text = tokenizer([words], is_split_into_words=True, return_offsets_mapping=True)

logits_processor = LogitsProcessorList([SQLLogitsProcessor(tokenizer)])

out = pretrained_model.generate(
    torch.tensor(tokenized_text["input_ids"]),
    max_new_tokens=20,
    logits_processor=logits_processor
)
print(tokenizer.convert_tokens_to_string(
    tokenizer.convert_ids_to_tokens(
        out[0], skip_special_tokens=True)))
```

返回

```py
 SELECT email , email , id , email FROM customers
```

结果有点奇怪，但请记住：**我们甚至没有训练模型！** 我们只是根据特定规则强制生成标记。值得注意的是，约束生成不会干扰训练；约束只在训练后生成过程中起作用。因此，当这些约束得当实施时，它们只会提高生成准确性。

我们的简化实现未能涵盖所有 SQL 语法。一个真正的实现必须支持更多的语法，可能不仅考虑最后一个标记，还要考虑多个标记，并且支持批量生成。一旦这些改进到位，我们训练好的模型可以可靠地生成可执行的 SQL 查询，且仅限于模式中有效的表名和列名。类似的方法也可以在生成 JSON 时强制执行约束，确保键存在和括号闭合。

## **注意分词问题**

分词通常被忽视，但在使用生成性 AI 进行结构化输出时，正确的分词至关重要。然而，在后台，分词可能对模型的训练产生影响。例如，您可能会微调一个模型，将文本翻译为 JSON。在微调过程中，您向模型提供文本-JSON 对，模型会对其进行分词。那么这种分词会是什么样子呢？

![](../Images/dc9052660570390852112c1180650f1e.png)

(图像来源：作者)

当您阅读“[[”时，它是两个方括号，但分词器将其转换为单一的 ID，这将被分词分类器视为与单一括号完全不同的类别。这使得模型必须学习的整个逻辑更加复杂（例如，记住需要关闭多少个括号）。类似地，在单词前添加空格可能会改变它们的分词和类别 ID。例如：

![](../Images/756198370ecbaaf8710bcdb24c8ccf3a.png)

(图像来源：作者)

这再次增加了模型需要学习的逻辑复杂性，因为与这些 ID 相关的权重将需要单独学习，以适应稍微不同的情况。

为了简化学习，确保每个概念和标点符号始终转换为相同的标记，方法是为单词和字符前添加空格。

![](../Images/ce09edd397c68277a6d2ddf93b2cb378.png)

分开写的单词有助于更一致的分词（图像来源：作者）

在微调过程中输入带空格的示例可以简化模型需要学习的模式，从而提高模型的准确性。在预测时，模型将输出带空格的 JSON，您可以在解析之前去除这些空格。

## **总结**

生成性 AI 提供了一种有价值的方法，用于翻译成格式化语言。通过利用输出结构的知识，我们可以约束生成过程，消除一类错误，并确保查询的可执行性和数据结构的可解析性。

此外，这些格式可能会使用标点符号和关键词来表示某些意义。确保这些关键词的标记化一致性，可以显著降低模型需要学习的模式复杂度，从而减少模型的所需规模和训练时间，同时提高其准确性。

结构化生成式人工智能可以有效地将自然语言转换为任何结构化格式。这些翻译能够从文本中提取信息或生成查询，这是许多应用的强大工具。
