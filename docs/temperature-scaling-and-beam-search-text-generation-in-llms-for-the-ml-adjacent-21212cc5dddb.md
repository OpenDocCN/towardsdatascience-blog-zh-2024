# LLM中的温度缩放与束搜索文本生成，面向机器学习相关领域的人

> 原文：[https://towardsdatascience.com/temperature-scaling-and-beam-search-text-generation-in-llms-for-the-ml-adjacent-21212cc5dddb?source=collection_archive---------1-----------------------#2024-04-26](https://towardsdatascience.com/temperature-scaling-and-beam-search-text-generation-in-llms-for-the-ml-adjacent-21212cc5dddb?source=collection_archive---------1-----------------------#2024-04-26)

## “温度”是什么，它如何工作，它与束搜索启发式算法的关系，以及LLM输出生成如何可能出错

[](https://mikecvet.medium.com/?source=post_page---byline--21212cc5dddb--------------------------------)[![Mike Cvet](../Images/93545a0c873515a599ba094ad51ee915.png)](https://mikecvet.medium.com/?source=post_page---byline--21212cc5dddb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--21212cc5dddb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--21212cc5dddb--------------------------------) [Mike Cvet](https://mikecvet.medium.com/?source=post_page---byline--21212cc5dddb--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--21212cc5dddb--------------------------------) ·阅读时长19分钟·2024年4月26日

--

![](../Images/48deee7f0c8dc77db834aa1f4a6bc09a.png)

由[Paul Green](https://unsplash.com/@pgreen1983?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，图片来源于[Unsplash](https://unsplash.com/photos/photography-of-spot-light-turned-on-mln2ExJIkfc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)；除非另有说明，否则所有其他图片由作者提供

如果你曾使用过[OpenAI](https://platform.openai.com/docs/api-reference/chat/create)或[Anthropic](https://docs.anthropic.com/claude/reference/messages_post)等LLM的API，你会看到API中有一个`temperature`设置。这个参数是如何使用的，如何工作的呢？

来自[Anthropic聊天API文档](https://docs.anthropic.com/claude/reference/messages_post)：

```py
temperature (number)

Amount of randomness injected into the response.

Defaults to 1.0\. Ranges from 0.0 to 1.0\. Use temperature closer to 0.0 for 
analytical / multiple choice, and closer to 1.0 for creative and 
generative tasks.

Note that even with temperature of 0.0, the results will not be 
fully deterministic.
```

温度（按通常的实现方式）并不会真正*注入随机性*到响应中。在这篇文章中，我将讲解这个设置的作用，以及它如何在束搜索中使用，束搜索是LLM中最常用的文本生成技术，并通过[GitHub上的参考实现](https://github.com/mikecvet/beam/tree/main)展示一些输出生成的例子（包括失败和成功的案例）。

你将要了解的内容：

+   [重新审视LLM推理与Token预测](#84c0)

+   [贪婪搜索](#d2c9)

+   [束搜索](#e148)

+   [温度](#4e6e)

+   [实现细节](#e9f6)

+   [贪婪搜索与束搜索生成示例](#db73)

    - [贪婪搜索](#4100)

    - [束搜索](#45c4)

    - [带温度的束搜索](#3289)

    - [水牛水牛水牛水牛水牛水牛水牛水牛与得分处罚](#f0e1)

+   [结论](#f732)

# 重访LLM推理和标记预测

如果你在这里，你可能对LLM是如何工作的有*一些*了解。

从高层次来看，LLM文本生成涉及预测序列中的下一个标记，这取决于前面标记的累积概率。这个过程利用了由以下因素塑造的内部概率分布：

+   模型的内部学习权重，通过在庞大的数据集上进行广泛的训练得到了精细化。

+   整个输入上下文（查询以及任何其他补充数据或文档）

+   到目前为止生成的标记集

基于[Transformer](https://en.wikipedia.org/wiki/Transformer_(deep_learning_architecture))的生成模型通过[自注意力机制](/illustrated-self-attention-2d627e33b20a)构建输入上下文的表示，从而使它们能够动态评估并优先考虑输入的不同部分，基于这些部分与当前预测点的相关性。在序列解码过程中，这些模型评估*每个部分*如何影响正在生成的序列，确保每一个新的标记都能反映输入和不断演变的输出的整合（主要通过[交叉注意力](https://vaclavkosar.com/ml/cross-attention-in-transformer-architecture)实现）。

[斯坦福CS224N课程资料](https://web.stanford.edu/class/archive/cs/cs224n/cs224n.1194/readings/cs224n-2019-notes06-NMT_seq2seq_attention.pdf)是理解这些概念的极好资源。

我在这里想要强调的关键点是，当模型决定选择概率上最优的标记时，它通常是在评估*整个*输入上下文，以及*整个*正在生成的序列。然而，使用这些预测来迭代构建文本序列的最直观过程是简化的：一个[贪心算法](https://en.wikipedia.org/wiki/Greedy_algorithm)，它在每一步基于最可能的标记构建输出文本。

接下来，我将讨论它是如何工作的、它的不足之处，以及一些用于适应这些不足的技术。

# 贪心搜索

使用模型构建输出序列的最自然方法是逐渐预测下一个最佳标记，将其附加到已生成的序列中，并继续直到生成结束。这叫做*贪心搜索*，是从LLM（或其他模型）生成文本的最简单和最高效的方式。在最基本的形式下，它大致如下：

```py
sequence = ["<start>"]
while sequence[-1] != "<end>":
  # Given the input context, and seq so far, append most likely next token
  sequence += model(input, sequence)
return "".join(sequence)
```

本科计算机科学算法课程中有一节关于[图遍历](https://en.wikipedia.org/wiki/Graph_traversal)算法的内容。如果你将潜在的LLM输出序列的宇宙建模为标记的图形，那么在给定输入上下文的情况下寻找最优输出序列的问题，便与遍历加权图的问题相似。在这种情况下，边的“权重”是由注意力分数生成的概率，而遍历的目标是最小化从头到尾的总体成本（最大化总体概率）。

![](../Images/e5446cdc7c9cecce5286dafcf4ec74aa.png)

贪心最佳优先搜索通过在每一步做出看似最佳的决定，以仅向前的方向遍历概念图标记。

在所有可能的文本生成方法中，这是最具计算效率的——推理次数与输出标记的数量是1:1的关系。然而，仍然存在一些问题。

在每一步的标记生成中，算法会根据目前为止的输出序列选择具有最高概率的标记，并将其附加到该序列中。这就是这种方法的简便之处，也是其缺陷，与所有其他贪心算法一样——它会陷入[局部最小值](https://en.wikipedia.org/wiki/Maximum_and_minimum)。也就是说，看似当前最好的标记*现在*可能实际上不是生成输出*整体*上最好的标记。

```py
"We can treat it as a matter of" 
  [course (p=0.9) | principle (p=0.5)] | cause (p=0.2)]"
```

给定一些输入上下文和目前生成的字符串，`We can treat it as a matter of course`似乎是一个逻辑上合理且有可能生成的序列。

但如果上下文准确的句子是`We can treat it as a matter of cause and effect`呢？贪心搜索无法回溯并将序列标记`course`替换为`cause and effect`。当时看似最好的标记实际上将输出生成困于一个次优序列中。

在每一步考虑低概率标记的需要，希望后续能生成更好的输出序列，这正是束搜索（beam search）有用的地方。

# 束搜索（Beam Search）

回到图搜索的类比，为了生成任何给定查询和上下文的最优文本，我们必须完全探索潜在的标记序列的宇宙。这个解决方案类似于[A*搜索算法](https://en.wikipedia.org/wiki/A*_search_algorithm)（比[Dijkstra算法](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm)更接近，因为我们不一定要最短路径，而是最低成本/最高可能性）。

![](../Images/6fc0bbc1ddd5079610f4145be84f080c.png)

A*搜索示意图由[Wgullyn](https://commons.wikimedia.org/w/index.php?title=User%3AWgullyn&action=edit&redlink=1)提供，来源于[https://en.wikipedia.org/wiki/A*_search_algorithm](https://en.wikipedia.org/wiki/A*_search_algorithm)

由于我们处理的是自然语言，涉及的复杂性太高，以至于在大多数情况下无法穷尽所有查询的搜索空间。解决方案是将搜索空间缩减到一个合理数量的候选路径；比如可能只有 4、8 或 12 条。

[Beam search](https://en.wikipedia.org/wiki/Beam_search) 是一种通常用于逼近理想 A* 搜索结果的启发式方法。该技术保持 `k` 个 *候选序列*，这些序列是通过分别选择 *top-k* 最可能的标记逐步构建的。每个标记都会贡献到整个序列的得分，在每一步后，所有候选序列会被修剪，只保留得分最高的前 `k` 个序列。

![](../Images/e81d6f9091c89b995172fb8fb2992900.png)

与 A* 搜索类似，Beam search 会保持从开始到结束的多条路径，评估有限数量候选序列的整体得分。这个数量被称为“beam width”。

Beam search 中的“beam” [借用了手电筒的类比](https://web.stanford.edu/%7Ejurafsky/slp3/ed3book.pdf)，手电筒的光束可以被加宽或缩小。以生成 *the quick brown fox jumps over the lazy dog* 为例，假设 beam width 为 `2`，整个过程大致如下：

![](../Images/357234441d2c6f0bc675c925594f8b71.png)

在这一阶段，正在维护两个候选序列：“*the*” 和 “*a*”。这两个序列各自需要评估接下来最可能的两个标记。

![](../Images/1d20191c70190ef7f97d32413b19b63f.png)

下一步后，“*the speedy*” 被淘汰，“*the quick*” 被选为第一个候选序列。对于第二个候选序列，“*a lazy*” 被淘汰，“*a quick*” 被选中，因为它具有更高的累积概率。需要注意的是，如果线上两个候选的概率高于线下两个候选的概率，它们将代表下一步的两个候选序列。

![](../Images/e85a3ab3d02ab9a852646a3c3eaacd42.png)

这个过程会一直继续，直到达到最大标记长度限制，或者所有候选序列都已经添加了结束标记，意味着我们已经完成了该序列的文本生成。

增加 beam width 会增加搜索空间，从而提高输出更优结果的可能性，但也会相应地增加空间和计算成本。还需要注意的是，`beam_width=1` 的 beam search 实际上与贪心搜索是等同的。

# Temperature

那么，`temperature` 与这一切有什么关系呢？正如我上面提到的，这个参数并不会真正的 `注入随机性` 到生成的文本序列中，但它确实会修改输出序列的 *可预测性*。借用 [信息论](https://en.wikipedia.org/wiki/Information_theory)的概念：temperature 可以增加或减少与标记预测相关的 [熵](https://en.wikipedia.org/wiki/Entropy_(information_theory))。

[Softmax](https://en.wikipedia.org/wiki/Softmax_function) [激活函数](https://en.wikipedia.org/wiki/Activation_function)通常用于将模型（包括LLM）的原始输出（即[logits](https://deepai.org/machine-learning-glossary-and-terms/logit)）转换为概率分布（我稍微讲解了一下[这里](https://betterprogramming.pub/word2vec-embeddings-from-the-ground-up-for-the-ml-adjacent-8d8c484e7cb5)）。该函数定义如下，给定一个包含`n`个元素的向量`Z`：

![](../Images/17663ffca4ce96dfc2648f4c52fa803c.png)

Sigma通常用于指代softmax函数。

该函数输出一个概率向量（或[张量](https://en.wikipedia.org/wiki/Tensor)），其总和为`1.0`，可以用来清晰地评估模型在类别预测中的信心，且结果是人类可解释的。

可以引入一个“温度”缩放参数`T`，该参数在应用softmax之前对logit值进行缩放。

![](../Images/325914dbd49526f978ec826d353b6761.png)

温度缩放参数T应用于softmax函数的输入。

应用`T > 1.0`的温度效果是*缩小*logit值，并且产生了减弱不同类别之间概率最大差异的效果（它增加了模型预测中的熵）。

使用`T < 1.0`的温度会产生相反的效果；它*放大*了差异，这意味着最有信心的预测会比其他预测更加突出。这减少了模型预测中的熵。

在代码中，它看起来是这样的：

```py
scaled_logits = logits_tensor / temperature
probs = torch.softmax(scaled_logits, dim=-1)
```

看看给定一些手写logit值时，8个可能类别的效果：

![](../Images/138299975434200047e05f840538fd66.png)

通过我链接的仓库中的脚本生成

上面的图是使用以下值绘制的：

```py
ts = [0.5, 1.0, 2.0, 4.0, 8.0]
logits = torch.tensor([3.123, 5.0, 3.234, 2.642, 2.466, 3.3532, 3.8, 2.911])
probs  = [torch.softmax(logits / t, dim=-1) for t in ts]
```

条形图表示logit值（模型预测的输出），而线条表示这些类别的概率分布，概率值定义在右侧标签上。粗红线表示期望的分布，温度为`T=1.0`，而其他线条则演示了在`0.5`到`8.0`的温度范围内，相对可能性变化的情况。

你可以清楚地看到，`T=0.5`强调了最大幅度logit索引的可能性，而`T=8.0`则将类别之间的概率差异缩小到几乎为零。

```py
>>> [print(f' t={t}\n l={(logits/t)}\n p={p}\n') for p,t in zip(probs, ts)]
 t=0.5
 l=tensor([6.2460, 10.000, 6.4680, 5.2840, 4.9320, 6.7064, 7.6000, 5.8220])
 p=tensor([0.0193, 0.8257, 0.0241, 0.0074, 0.0052, 0.0307, 0.0749, 0.0127])

 t=1.0
 l=tensor([3.1230, 5.0000, 3.2340, 2.6420, 2.4660, 3.3532, 3.8000, 2.9110])
 p=tensor([0.0723, 0.4727, 0.0808, 0.0447, 0.0375, 0.0911, 0.1424, 0.0585])

 t=2.0
 l=tensor([1.5615, 2.5000, 1.6170, 1.3210, 1.2330, 1.6766, 1.9000, 1.4555])
 p=tensor([0.1048, 0.2678, 0.1108, 0.0824, 0.0754, 0.1176, 0.1470, 0.0942])

 t=4.0
 l=tensor([0.7807, 1.2500, 0.8085, 0.6605, 0.6165, 0.8383, 0.9500, 0.7278])
 p=tensor([0.1169, 0.1869, 0.1202, 0.1037, 0.0992, 0.1238, 0.1385, 0.1109])

 t=8.0
 l=tensor([0.3904, 0.6250, 0.4042, 0.3302, 0.3083, 0.4191, 0.4750, 0.3639])
 p=tensor([0.1215, 0.1536, 0.1232, 0.1144, 0.1119, 0.1250, 0.1322, 0.1183])
```

现在，这不一定会改变任何两个类别之间的*相对可能性*（除去数值稳定性问题），那么它在序列生成中有什么实际影响呢？

答案在于束搜索的机制。温度值大于`1.0`使得高分的单个标记不太可能超过一系列稍微不太可能的标记，二者结合起来产生一个更高分的输出。

```py
>>> sum([0.9, 0.3, 0.3, 0.3]) # raw probabilities
1.8 # dominated by first token
>>> sum([0.8, 0.4, 0.4, 0.4]) # temperature-scaled probabilities
2.0 # more likely overall outcome
```

总结来说，更高的温度设置允许束搜索在标记图上探索更多样化的候选序列路径。较低的温度设置使其越来越专注于每一步最可能的预测。

# 实现细节

束搜索实现通常使用[对数概率](https://en.wikipedia.org/wiki/Log_probability)来处理softmax概率，这在机器学习领域及其他许多领域中都很常见。其原因包括：

+   使用的概率通常极其小；使用对数概率可以改善[数值稳定性](https://en.wikipedia.org/wiki/Numerical_stability)。

+   我们可以通过对数概率的加法来计算结果的累积概率，而不是直接乘以原始概率，这在计算上稍微更快，并且数值上更稳定。回忆一下，`p(x) * p(y) == log(p(x)) + log(p(y))`

+   优化器，如[梯度下降](https://en.wikipedia.org/wiki/Gradient_descent)，在处理对数概率时更为简单，这使得导数计算更加简便，而交叉熵损失等损失函数已经涉及了对数计算。

这也意味着我们作为评分使用的对数概率值是负实数。由于softmax产生的概率分布的总和为`1.0`，因此任何类别的概率的对数值都将`≤ 1.0`，从而产生负值。这有些烦人，但它与高评分值更好这一特性一致，而极其负的评分则反映了极不可能的结果：

```py
>>> math.log(3)
1.0986122886681098
>>> math.log(0.99)
-0.01005033585350145
>>> math.log(0.98)
-0.020202707317519466
>>> math.log(0.0001)
-9.210340371976182
>>> math.log(0.000000000000000001)
-41.44653167389282
```

这里是大部分示例代码，注释非常详细，代码也可以在[Github](https://github.com/mikecvet/beam/blob/main/src/beam.py#L8)上找到。`GeneratedSequence`和`ScoredToken`的定义可以在[这里找到](https://github.com/mikecvet/beam/blob/main/src/sequence.py)；这些主要是用于包装标记和得分的简单工具。

```py
# The initial candidate sequence is simply the start token ID with 
# a sequence score of 0
candidate_sequences = [
  GeneratedSequence(tokenizer, start_token_id, end_token_id, 0.0)
]

for i in tqdm.tqdm(range(max_length)):
  # Temporary list to store candidates for the next generation step
  next_step_candidates = []

  # Iterate through all candidate sequences; for each, generate the next
  # most likely tokens and add them to the next-step sequnce of candidates
  for candidate in candidate_sequences:

    # skip candidate sequences which have included the end-of-sequence token
    if not candidate.has_ended():

      # Build a tensor out of the candidate IDs; add a single batch dimension
      gen_seq = torch.tensor(candidate.ids(), device=device).unsqueeze(0)

      # Predict next token
      output = model(input_ids=src_input_ids, decoder_input_ids=gen_seq)

      # Extract logits from output
      logits = output.logits[:, -1, :]

      # Scale logits using temperature value
      scaled_logits = logits / temperature

      # Construct probability distribution against scaled 
      # logits through softmax activation function
      probs = torch.softmax(scaled_logits, dim=-1)

      # Select top k (beam_width) probabilities and IDs from the distribution
      top_probs, top_ids = probs.topk(beam_width)

      # For each of the top-k generated tokens, append to this 
      # candidate sequence, update its score, and append to the list of next 
      # step candidates
      for i in range(beam_width):
        # the new token ID
        next_token_id = top_ids[:, i].item()

        # log-prob of the above token
        next_score = torch.log(top_probs[:, i]).item()

        new_seq = deepcopy(candidate)

        # Adds the new token to the end of this sequence, and updates its 
        # raw and normalized scores. Scores are normalized by sequence token 
        # length, to avoid penalizing longer sequences
        new_seq.append(ScoredToken(next_token_id, next_score))

        # Append the updated sequence to the next candidate sequence set
        next_step_candidates.append(new_seq)
    else:
      # Append the canddiate sequence as-is to the next-step candidates
      # if it already contains an end-of-sequence token
      next_step_candidates.append(candidate)

  # Sort the next-step candidates by their score, select the top-k 
  # (beam_width) scoring sequences and make them the new 
  # candidate_sequences list
  next_step_candidates.sort()
  candidate_sequences = list(reversed(next_step_candidates))[:beam_width]

  # Break if all sequences in the heap end with the eos_token_id
  if all(seq.has_ended() for seq in candidate_sequences):
    break

return candidate_sequences
```

在下一部分，你可以找到在不同数据集上使用不同参数运行此代码的一些结果。

# 贪婪搜索和束搜索生成示例

正如我提到的，[我已经将一些示例代码发布到Github](https://github.com/mikecvet/beam)，该代码使用了`t5-small`[Hugging Face的transformer模型](https://huggingface.co/docs/transformers/en/model_doc/t5)及其对应的[T5Tokenizer](https://huggingface.co/docs/transformers/v4.40.0/en/model_doc/t5#transformers.T5Tokenizer)。下面的示例是通过T5模型运行的，使用了[quick brown fox 等](https://en.wikipedia.org/wiki/The_quick_brown_fox_jumps_over_the_lazy_dog)维基百科页面，经过[提取脚本](https://github.com/mikecvet/beam/blob/main/wiki-extract.py)的清洗。

## 贪婪搜索

运行`--greedy` [模式](https://github.com/mikecvet/beam/blob/main/src/greedy.py)：

```py
$ python3 src/main.py --greedy --input ./wiki-fox.txt --prompt "summarize the following document"

greedy search generation results: 
[
the phrase is used in the annual Zaner-Bloser National Handwriting Competition.
it is used for typing typewriters and keyboards, typing fonts. the phrase 
is used in the earliest known use of the phrase.
]
```

该输出很好地总结了文章的部分内容，但整体效果不佳。它缺乏初始上下文，有重复内容，并且没有明确说明短语的具体含义。

## 束搜索

让我们再试一次，这次使用[波束搜索](https://github.com/mikecvet/beam/blob/main/src/beam.py)进行输出生成，初始波束宽度为`4`，并且使用默认的`temperature`值`1.0`。

```py
$ python3 src/main.py --beam 4 --input ./wiki-fox.txt --prompt "summarize the following document"

[lots of omitted output]

beam search (k=4, t=1.0) generation results:
[
 "the quick brown fox jumps over the lazy dog" is an English-language pangram. 
 the phrase is commonly used for touch-typing practice, typing typewriters and 
 keyboards. it is used in the annual Zaner-Bloser National 
 Handwriting Competition.
]
```

这个输出**远**优于上述的贪婪输出，最值得注意的是我们*使用相同的模型、提示和输入上下文来生成它*。

其中仍然有一些错误；例如“*打字打字机*”，并且“*键盘*”可能存在歧义。

我分享的波束搜索代码[将会输出](https://github.com/mikecvet/beam/blob/main/src/beam.py)其在文本生成过程中逐步决策的进展（完整输出[在这里](https://github.com/mikecvet/beam/blob/main/example_runs/fox_b_1.out)）。例如，前两步：

```py
beginning beam search | k = 4 bos = 0 eos = 1 temp = 1.0 beam_width = 4
0.0: [], next token probabilities:
 p:  0.30537632: ▁the
 p:  0.21197866: ▁"
 p:  0.13339639: ▁phrase
 p:  0.13240208: ▁

next step candidates:
 -1.18621039: [the]
 -1.55126965: ["]
 -2.01443028: [phrase]
 -2.02191186: []

-1.1862103939056396: [the], next token probabilities:
 p:  0.61397356: ▁phrase
 p:  0.08461960: ▁
 p:  0.06939770: ▁"
 p:  0.04978605: ▁term

-1.5512696504592896: ["], next token probabilities:
 p:  0.71881396: the
 p:  0.08922042: qui
 p:  0.05990228: The
 p:  0.03147057: a

-2.014430284500122: [phrase], next token probabilities:
 p:  0.27810165: ▁used
 p:  0.26313403: ▁is
 p:  0.10535818: ▁was
 p:  0.03361856: ▁

-2.021911859512329: [], next token probabilities:
 p:  0.72647911: earliest
 p:  0.19509122: a
 p:  0.02678721: '
 p:  0.00308457: s

next step candidates:
 -1.67401379: [the phrase]
 -1.88142237: ["the]
 -2.34145740: [earliest]
 -3.29419887: [phrase used]
 -3.34952199: [phrase is]
 -3.65579963: [the]
 -3.65619993: [a]
```

现在，如果我们看看最后一步的候选集合：

```py
next step candidates:
 -15.39409454: ["the quick brown fox jumps over the lazy dog" is an English-language pangram. the phrase is commonly used for touch-typing practice, typing typewriters and keyboards. it is used in the annual Zaner-Bloser National Handwriting Competition.]
 -16.06867695: ["the quick brown fox jumps over the lazy dog" is an English-language pangram. the phrase is commonly used for touch-typing practice, testing typewriters and keyboards. it is used in the annual Zaner-Bloser National Handwriting Competition.]
 -16.10376084: ["the quick brown fox jumps over the lazy dog" is an English-language pangram. the phrase is commonly used for touch-typing practice, typing typewriters and keyboards. it is used in the annual Zaner-Bloser national handwriting competition.]
```

你可以看到，包含`打字打字机`的得分最高的句子比包含`测试打字机`的句子高出`-15.39`到`-16.06`，如果我们提高到[欧拉常数](https://en.wikipedia.org/wiki/Euler%27s_constant)并转换回累积概率，这是一个仅为`0.00001011316%`的概率差异。必须有办法克服这个微小的差异！

## 带温度的波束搜索

让我们看看是否通过应用温度值来平滑一些对数概率得分，能够改善这个总结。再次强调，其他一切，模型和输入上下文，将与上述示例完全相同。

```py
$ python3 src/main.py --beam 4 --temperature 4.0 --input ./wiki-fox.txt --prompt "summarize the following document"

[lots of omitted output]

beam search (k=4, t=4.0) generation results:
[
 "the quick brown fox jumps over the lazy dog" is an English-language pangram. 
 it is commonly used for touch-typing practice, testing typewriters and 
 computer keyboards. earliest known use of the phrase started with "A"
]
```

这个输出正确地生成了“*测试打字机*”而不是“*打字打字机*”，并且明确指定了“*计算机键盘*”。有趣的是，它选择了历史事实，即这个短语最初以“**一只**快速的棕色狐狸”开始，而不是上面提到的Zaner-Bloser竞赛事实。完整的输出也可以在[这里](https://github.com/mikecvet/beam/blob/main/example_runs/fox_b_4.out)找到。

这个输出是否更好是一个主观的看法问题。它在一些细微的方面有所不同，温度值的使用和设置会因应用而异。我认为它更好，而且有趣的是，获得这个输出时没有改变任何模型权重、模型架构或提示。

## 水牛水牛水牛水牛水牛水牛水牛水牛与评分惩罚

让我们看看波束搜索，使用上述温度设置，是否能正确处理我最喜欢的英语语言学构造：[水牛水牛水牛水牛水牛水牛水牛水牛](https://en.wikipedia.org/wiki/Buffalo_buffalo_Buffalo_buffalo_buffalo_buffalo_Buffalo_buffalo)。

```py
$ python3 src/main.py --beam 4 --temperature 4.0 --input ./wiki-buffalo.txt --prompt "summarize the linguistic construct in the following text"

[lots of omitted outputs]

beam search (k=4, t=4.0) generation results:
[
  "Buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo 
  buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo 
  buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo 
  buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo 
  buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo 
  buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo 
  buffalo buffalo buffalo buffalo buffalo buffalo
]
```

完全的灾难，尽管这是可以预见的。鉴于这个输入文档的复杂性，我们需要额外的技术来处理像这样的上下文。有趣的是，最后一次迭代的候选并没有包括任何一个合理的序列：

```py
next step candidates:
  -361.66266489: ["Buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo]
  -362.13168168: ["buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo]
  -362.22955942: ["Buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo.]
  -362.60354519: ["Buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo]
  -363.03604889: ["Buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo,]
  -363.07167459: ["buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo]
  -363.14155817: ["Buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo Buffalo]
  -363.28574753: ["Buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo. the]
  -363.35553551: ["Buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo buffalo a]
[more of the same]
```

我们可以 [应用特定令牌的得分衰减](https://github.com/mikecvet/beam/blob/main/src/beam.py#L98)（更像是 *惩罚*）对重复的令牌进行处理，这使得它们在束搜索算法中显得不那么有吸引力（或者更准确地说，*更不可能的解*）：

```py
token_counts = Counter(t.token_id for t in candidate)

# For each of the top-k generated tokens, append to this candidate sequence,
# update its score, and append to the list of next step candidates
for i in range(beam_width):
  next_token_id = top_ids[:, i].item() # the new token ID
  next_score = torch.log(top_probs[:, i]).item() # log-prob of the above token

  # Optionally apply a token-specific score decay to repeated tokens
  if decay_repeated and next_token_id in token_counts:
    count = token_counts[next_token_id]
    decay = 1 + math.log(count + 1)
    next_score *= decay # inflate the score of the next sequence accordingly

  new_seq = deepcopy(candidate)
  new_seq.append(ScoredToken(next_token_id, next_score))
```

这导致了以下更合理的输出：

```py
$ python3 src/main.py --decay --beam 4 --temperature 4.0 --input ./wiki-buffalo.txt --prompt "summarize the linguistic construct in the following text"

[lots of omitted outputs]

beam search (k=4, t=4.0) generation results:
[
  "Buffalo buffalo" is grammatically correct sentence in English, often 
  presented as an example of how homophonies can be used to create complicated
  language constructs through unpunctuated terms and sentences. it uses three 
  distinct meanings:An attributive noun (acting
]
```

[你可以看到](https://github.com/mikecvet/beam/blob/main/example_runs/buffalo_b_4_decay.out#L285)得分惩罚将 *无限水牛* 序列拉低，导致了上述输出结果：

```py
next step candidates:
 -36.85023594: ["Buffalo buffalo Buffalo]
 -37.23766947: ["Buffalo buffalo"]
 -37.31325269: ["buffalo buffalo Buffalo]
 -37.45994210: ["buffalo buffalo"]
 -37.61866760: ["Buffalo buffalo,"]
 -37.73602080: ["buffalo" is]
 [omitted]

-36.85023593902588: ["Buffalo buffalo Buffalo], next token probabilities:
 p:  0.00728357: ▁buffalo
 p:  0.00166316: ▁Buffalo
 p:  0.00089072: "
 p:  0.00066582: ,"

['▁buffalo'] count: 1 decay: 1.6931471805599454, score: -4.922133922576904, next: -8.33389717334955
['▁Buffalo'] count: 1 decay: 1.6931471805599454, score: -6.399034023284912, next: -10.834506414832013
-37.237669467926025: ["Buffalo buffalo"], next token probabilities:
 p:  0.00167652: ▁is
 p:  0.00076465: ▁was
 p:  0.00072227: ▁
 p:  0.00064367: ▁used

-37.313252687454224: ["buffalo buffalo Buffalo], next token probabilities:
 p:  0.00740433: ▁buffalo
 p:  0.00160758: ▁Buffalo
 p:  0.00091487: "
 p:  0.00066765: ,"

['▁buffalo'] count: 1 decay: 1.6931471805599454, score: -4.905689716339111, next: -8.306054711921485
['▁Buffalo'] count: 1 decay: 1.6931471805599454, score: -6.433023929595947, next: -10.892056328870039
-37.45994210243225: ["buffalo buffalo"], next token probabilities:
 p:  0.00168198: ▁is
 p:  0.00077098: ▁was
 p:  0.00072504: ▁
 p:  0.00065945: ▁used

next step candidates:
 -43.62870741: ["Buffalo buffalo" is]
 -43.84772754: ["buffalo buffalo" is]
 -43.87371445: ["Buffalo buffalo Buffalo"]
 -44.16472149: ["Buffalo buffalo Buffalo,"]
 -44.30998302: ["buffalo buffalo Buffalo"]
```

所以事实证明，我们需要像这样的额外技巧（*技术*），来处理一些特殊的边缘情况。

# 结论

这篇文章比我原本计划写的要长很多；希望你能从中得到一些启示。除了单纯理解束搜索（beam search）和温度（temperature）是如何工作的之外，我认为最有趣的例子是，即使考虑到LLM的巨大复杂性和能力，影响它们预测结果使用方式的实现选择，仍然对输出质量产生了巨大影响。将简单的本科计算机科学概念应用于序列构建，可以导致显著不同的LLM输出，即使其他所有输入完全相同。

当我们在使用LLM时遇到幻觉、错误或其他怪癖时，完全有可能（也许很可能）这些问题是由输出序列构建算法的怪癖引起的，而不是训练模型本身的“故障”。对于API的用户来说，几乎不可能分辨出差异。

我认为这是一个有趣的例子，展示了LLM背后复杂的机制，使它们成为今天如此强大的工具和产品。
