# 结合大型和小型LLM以提升推理时间和质量

> 原文：[https://towardsdatascience.com/combining-large-and-small-llms-for-inference-time-and-quality-boosts-1779b6b5100b?source=collection_archive---------9-----------------------#2024-12-05](https://towardsdatascience.com/combining-large-and-small-llms-for-inference-time-and-quality-boosts-1779b6b5100b?source=collection_archive---------9-----------------------#2024-12-05)

## 实现推测性解码和对比性解码

[](https://mlsys.medium.com/?source=post_page---byline--1779b6b5100b--------------------------------)[![Richa Gadgil](../Images/c39ace5df0438240647ea751e8f6ba9e.png)](https://mlsys.medium.com/?source=post_page---byline--1779b6b5100b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1779b6b5100b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1779b6b5100b--------------------------------) [Richa Gadgil](https://mlsys.medium.com/?source=post_page---byline--1779b6b5100b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1779b6b5100b--------------------------------) ·阅读时间8分钟·2024年12月5日

--

大型语言模型由数十亿个参数（权重）组成。对于每个生成的单词，模型必须在所有这些参数上执行计算密集型操作。

大型语言模型接受一个句子或标记序列，并生成下一个最可能的标记的概率分布。

因此，通常解码**n**个标记（或从模型生成**n**个单词）需要运行模型**n**次。在每次迭代中，新的标记会附加到输入句子中，并再次传递给模型。这可能是昂贵的。

此外，解码策略会影响生成单词的质量。以简单的方式生成标记，通过仅取输出分布中概率最高的标记，可能会导致重复的文本。从分布中进行随机采样可能会导致意外的漂移。

因此，需要一个可靠的解码策略，以确保两者的实现：

+   高质量输出

+   快速推理时间

**这两项要求可以通过结合大型和小型语言模型来解决，只要业余模型和专家模型相似（例如，相同架构但不同大小）。**

+   **目标/大型模型：** 具有更多参数的主语言模型（例如，OPT-13B）

+   **业余/小型模型：** 主语言模型的较小版本，具有较少的参数（例如，OPT-125M）

**推测性**和**对比性**解码利用大型和小型LLM实现可靠和高效的文本生成。

![](../Images/0108a15ac5b5abb54cf65b964db6f0d5.png)

# 高质量推理的对比性解码

[对比解码](https://arxiv.org/abs/2210.15097)是一种策略，利用了大规模语言模型（如重复、无 coherency 等问题）在小规模语言模型中表现得更加显著的事实。因此，这种策略优化了小模型和大模型之间概率差异最大的词元。

对于单次预测，对比解码生成两个概率分布：

+   *q =* 业余模型的logit概率

+   *p =* 专家模型的logit概率

下一词元的选择依据以下标准：

+   丢弃所有在专家模型下概率不够高的词元（丢弃*p(x) < alpha * max(p)*）。

+   从剩余的词元中，选择与大模型和小模型log概率差异最大的那个，*max(p(x) - q(x))。*

![](../Images/4e99947389dc73464dbe732a72555688.png)

## 实现对比解码

```py
from transformers import AutoTokenizer, AutoModelForCausalLM
import torch

# Load models and tokenizer
tokenizer = AutoTokenizer.from_pretrained('gpt2')
amateur_lm = AutoModelForCausalLM.from_pretrained('gpt2')
expert_lm = AutoModelForCausalLM.from_pretrained('gpt2-large')

def contrastive_decoding(prompt, max_length=50):
    input_ids = tokenizer(prompt, return_tensors="pt").input_ids

    while input_ids.shape[1] < max_length:

        # Generate amateur model output
        amateur_outputs = amateur_lm(input_ids, return_dict=True)
        amateur_logits = torch.softmax(amateur_outputs.logits[:, -1, :], dim=-1)
        log_probs_amateur = torch.log(amateur_logits)

        # Generate expert model output
        expert_outputs = expert_lm(input_ids, return_dict=True)
        expert_logits = torch.softmax(expert_outputs.logits[:, -1, :], dim=-1)
        log_probs_exp = torch.log(expert_logits)

        log_probs_diff = log_probs_exp - log_probs_amateur

        # Set an alpha threshold to eliminate less confident tokens in expert
        alpha = 0.1
        candidate_exp_prob = torch.max(expert_logits)

        # Mask tokens below threshold for expert model
        V_head = expert_logits < alpha * candidate_exp_prob

        # Select the next token from the log-probabilities difference, ignoring masked values
        token = torch.argmax(log_probs_diff.masked_fill(V_head, -torch.inf)).unsqueeze(0)

        # Append token and accumulate generated text
        input_ids = torch.cat([input_ids, token.unsqueeze(1)], dim=-1)

    return tokenizer.batch_decode(input_ids)

prompt = "Large Language Models are"
generated_text = contrastive_decoding(prompt, max_length=25)
print(generated_text)
```

# 快速推理的推测性解码

[推测性解码](https://arxiv.org/abs/2211.17192)基于这样一个原理：小模型必须从与大模型相同的分布中采样。因此，这一策略旨在接受尽可能多的小模型预测，只要这些预测与大模型的分布一致。

小模型依次生成**n**个词元作为可能的猜测。然而，所有**n**个序列作为一个批次输入到更大的专家模型中，这比顺序生成更快。

这会为每个模型生成一个缓存，每个缓存中包含**n**个概率分布。

+   *q =* 业余模型的logit概率

+   *p =* 专家模型的logit概率

接下来，基于以下条件，业余模型采样的词元会被接受或拒绝：

+   如果在专家分布中（p）该词元的概率高于在业余分布中（q）的概率，或是*p(x) > q(x)*，则接受该词元。

+   如果在专家分布中（p）该词元的概率低于在业余分布中（q）的概率，或是*p(x) < q(x)*，则以概率*1 - p(x) / q(x)*拒绝该词元。

如果一个词元被拒绝，下一个词元会从专家分布或调整后的分布中采样。此外，业余和专家模型会重置缓存并重新生成**n**个猜测和概率分布*p*与*q*。

![](../Images/460e2c2954e742063edb26a642582e2d.png)

这里，蓝色表示被接受的词元，红色/绿色表示被拒绝的词元，随后从专家或调整后的分布中重新采样。

## 实现推测性解码

```py
from transformers import AutoTokenizer, AutoModelForCausalLM
import torch

# Load models and tokenizer
tokenizer = AutoTokenizer.from_pretrained('gpt2')
amateur_lm = AutoModelForCausalLM.from_pretrained('gpt2')
expert_lm = AutoModelForCausalLM.from_pretrained('gpt2-large')

# Sample next token from output distribution
def sample_from_distribution(logits):
    sampled_index = torch.multinomial(logits, 1)
    return sampled_index

def generate_cache(input_ids, n_tokens):
    # Store logits at each step for amateur and expert models
    amateur_logits_per_step = []
    generated_tokens = []

    batch_input_ids = []

    with torch.no_grad():
        for _ in range(n_tokens):
            # Generate amateur model output
            amateur_outputs = amateur_lm(input_ids, return_dict=True)
            amateur_logits = torch.softmax(amateur_outputs.logits[:, -1, :], dim=-1)
            amateur_logits_per_step.append(amateur_logits)

            # Sampling from amateur logits
            next_token = sample_from_distribution(amateur_logits)
            generated_tokens.append(next_token)

            # Append to input_ids for next generation step
            input_ids = torch.cat([input_ids, next_token], dim=-1)
            batch_input_ids.append(input_ids.squeeze(0))

    # Feed IDs to expert model as batch 
    batched_input_ids = torch.nn.utils.rnn.pad_sequence(batch_input_ids, batch_first=True, padding_value=0 )
    expert_outputs = expert_lm(batched_input_ids, return_dict=True)
    expert_logits = torch.softmax(expert_outputs.logits[:, -1, :], dim=-1)

    return amateur_logits_per_step, expert_logits, torch.cat(generated_tokens, dim=-1)

def speculative_decoding(prompt, n_tokens=5, max_length=50):
    input_ids = tokenizer(prompt, return_tensors="pt").input_ids

    while input_ids.shape[1] < max_length:
        amateur_logits_per_step, expert_logits, generated_ids = generate_cache(
            input_ids, n_tokens
        )

        accepted = 0
        for n in range(n_tokens):
            token = generated_ids[:, n][0]
            r = torch.rand(1).item()

            # Extract probabilities
            p_x = expert_logits[n][token].item()
            q_x = amateur_logits_per_step[n][0][token].item()

            # Speculative decoding acceptance criterion
            if ((q_x > p_x) and (r > (1 - p_x / q_x))):
                break  # Reject token and restart the loop
            else:
                accepted += 1

            # Check length
            if (input_ids.shape[1] + accepted) >= max_length:
                return tokenizer.batch_decode(input_ids)

        input_ids = torch.cat([input_ids, generated_ids[:, :accepted]], dim=-1)

        if accepted < n_tokens:
            diff = expert_logits[accepted] - amateur_logits_per_step[accepted][0]
            clipped_diff = torch.clamp(diff, min=0) 

            # Sample a token from the adjusted expert distribution
            normalized_result = clipped_diff / torch.sum(clipped_diff, dim=0, keepdim=True)
            next_token = sample_from_distribution(normalized_result)
            input_ids = torch.cat([input_ids, next_token.unsqueeze(1)], dim=-1)
        else:
            # Sample directly from the expert logits for the last accepted token
            next_token = sample_from_distribution(expert_logits[-1])
            input_ids = torch.cat([input_ids, next_token.unsqueeze(1)], dim=-1)

    return tokenizer.batch_decode(input_ids)

# Example usage
prompt = "Large Language models are"
generated_text = speculative_decoding(prompt, n_tokens=3, max_length=25)
print(generated_text)
```

## 评估

我们可以通过将两种解码方法与一种简单的解码方法进行比较来评估它们，其中我们从概率分布中随机选择下一个词元。

```py
def sequential_sampling(prompt, max_length=50):
    """
    Perform sequential sampling with the given model.
    """
    # Tokenize the input prompt
    input_ids = tokenizer(prompt, return_tensors="pt").input_ids

    with torch.no_grad():
          while input_ids.shape[1] < max_length:
            # Sample from the model output logits for the last token
            outputs = expert_lm(input_ids, return_dict=True)
            logits = outputs.logits[:, -1, :]

            probabilities = torch.softmax(logits, dim=-1)
            next_token = torch.multinomial(probabilities, num_samples=1)
            input_ids = torch.cat([input_ids, next_token], dim=-1)

    return tokenizer.batch_decode(input_ids)
```

为了评估对比解码，我们可以使用以下词汇丰富度的指标。

+   **n-gram 熵**：衡量生成文本中n-gram的不可预测性或多样性。高熵表明文本更具多样性，而低熵则表明文本有重复性或可预测性。

+   **distinct-n**：衡量生成文本中唯一n-gram的比例。较高的distinct-n值表示更多的词汇多样性。

```py
from collections import Counter
import math

def ngram_entropy(text, n):
    """
    Compute n-gram entropy for a given text.
    """
    # Tokenize the text
    tokens = text.split()
    if len(tokens) < n:
        return 0.0  # Not enough tokens to form n-grams

    # Create n-grams
    ngrams = [tuple(tokens[i:i + n]) for i in range(len(tokens) - n + 1)]

    # Count frequencies of n-grams
    ngram_counts = Counter(ngrams)
    total_ngrams = sum(ngram_counts.values())

    # Compute entropy
    entropy = -sum((count / total_ngrams) * math.log2(count / total_ngrams)
                   for count in ngram_counts.values())
    return entropy

def distinct_n(text, n):
    """
    Compute distinct-n metric for a given text.
    """
    # Tokenize the text
    tokens = text.split()
    if len(tokens) < n:
        return 0.0  # Not enough tokens to form n-grams

    # Create n-grams
    ngrams = [tuple(tokens[i:i + n]) for i in range(len(tokens) - n + 1)]

    # Count unique and total n-grams
    unique_ngrams = set(ngrams)
    total_ngrams = len(ngrams)

    return len(unique_ngrams) / total_ngrams if total_ngrams > 0 else 0.0

prompts = [
    "Large Language models are",
    "Barack Obama was",
    "Decoding strategy is important because",
    "A good recipe for Halloween is",
    "Stanford is known for"
]

# Initialize accumulators for metrics
naive_entropy_totals = [0, 0, 0]  # For n=1, 2, 3
naive_distinct_totals = [0, 0]    # For n=1, 2
contrastive_entropy_totals = [0, 0, 0]
contrastive_distinct_totals = [0, 0]

for prompt in prompts:
    naive_generated_text = sequential_sampling(prompt, max_length=50)[0]

    for n in range(1, 4):
        naive_entropy_totals[n - 1] += ngram_entropy(naive_generated_text, n)

    for n in range(1, 3):
        naive_distinct_totals[n - 1] += distinct_n(naive_generated_text, n)

    contrastive_generated_text = contrastive_decoding(prompt, max_length=50)[0]

    for n in range(1, 4):
        contrastive_entropy_totals[n - 1] += ngram_entropy(contrastive_generated_text, n)

    for n in range(1, 3):
        contrastive_distinct_totals[n - 1] += distinct_n(contrastive_generated_text, n)

# Compute averages
naive_entropy_averages = [total / len(prompts) for total in naive_entropy_totals]
naive_distinct_averages = [total / len(prompts) for total in naive_distinct_totals]
contrastive_entropy_averages = [total / len(prompts) for total in contrastive_entropy_totals]
contrastive_distinct_averages = [total / len(prompts) for total in contrastive_distinct_totals]

# Display results
print("Naive Sampling:")
for n in range(1, 4):
    print(f"Average Entropy (n={n}): {naive_entropy_averages[n - 1]}")
for n in range(1, 3):
    print(f"Average Distinct-{n}: {naive_distinct_averages[n - 1]}")

print("\nContrastive Decoding:")
for n in range(1, 4):
    print(f"Average Entropy (n={n}): {contrastive_entropy_averages[n - 1]}")
for n in range(1, 3):
    print(f"Average Distinct-{n}: {contrastive_distinct_averages[n - 1]}")
```

以下结果显示，对比解码在这些指标上优于朴素采样。

> **朴素采样：**
> 
> 平均熵（n=1）：4.990499826537679
> 
> 平均熵（n=2）：5.174765791328267
> 
> 平均熵（n=3）：5.14373124004409
> 
> 平均Distinct-1：0.8949694135740648
> 
> 平均Distinct-2：0.9951219512195122
> 
> **对比解码：**
> 
> 平均熵（n=1）：5.182773920916605
> 
> 平均熵（n=2）：5.3495681172235665
> 
> 平均熵（n=3）：5.313720275712986
> 
> 平均Distinct-1：0.9028425204970866
> 
> 平均Distinct-2：1.0

为了评估推测解码，我们可以查看一组提示的平均运行时间，针对不同的**n**值。

```py
import time
import matplotlib.pyplot as plt

# Parameters
n_tokens = range(1, 11)
speculative_decoding_times = []
naive_decoding_times = []

prompts = [
    "Large Language models are",
    "Barack Obama was",
    "Decoding strategy is important because",
    "A good recipe for Halloween is",
    "Stanford is known for"
]

# Loop through n_tokens values
for n in n_tokens:
    avg_time_naive, avg_time_speculative = 0, 0

    for prompt in prompts:
        start_time = time.time()
        _ = sequential_sampling(prompt, max_length=25)
        avg_time_naive += (time.time() - start_time)

        start_time = time.time()
        _ = speculative_decoding(prompt, n_tokens=n, max_length=25)
        avg_time_speculative += (time.time() - start_time)

    naive_decoding_times.append(avg_time_naive / len(prompts))
    speculative_decoding_times.append(avg_time_speculative / len(prompts))

avg_time_naive = sum(naive_decoding_times) / len(naive_decoding_times)

# Plotting the results
plt.figure(figsize=(8, 6))
plt.bar(n_tokens, speculative_decoding_times, width=0.6, label='Speculative Decoding Time', alpha=0.7)
plt.axhline(y=avg_time_naive, color='red', linestyle='--', label='Naive Decoding Time')

# Labels and title
plt.xlabel('n_tokens', fontsize=12)
plt.ylabel('Average Time (s)', fontsize=12)
plt.title('Speculative Decoding Runtime vs n_tokens', fontsize=14)
plt.legend()
plt.grid(axis='y', linestyle='--', alpha=0.7)

# Show the plot
plt.show()
plt.savefig("plot.png")
```

我们可以看到，朴素解码的平均运行时间远高于在不同**n**值下的推测解码。

![](../Images/475bc024a1d7d059c360c87cc8293ad3.png)

将大语言模型和小语言模型结合进行解码，在质量和效率之间达到了平衡。尽管这些方法在系统设计和资源管理中增加了额外的复杂性，但它们的好处适用于对话AI、实时翻译和内容创作。

这些方法需要仔细考虑部署约束。例如，运行双模型所需的额外内存和计算需求可能会限制在边缘设备上的可行性，尽管可以通过诸如模型量化等技术来缓解这一问题。

**除非另有说明，所有图片均为作者所作。**
