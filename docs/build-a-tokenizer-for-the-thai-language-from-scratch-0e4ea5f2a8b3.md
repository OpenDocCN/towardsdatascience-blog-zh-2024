# 从零开始构建泰语分词器

> 原文：[https://towardsdatascience.com/build-a-tokenizer-for-the-thai-language-from-scratch-0e4ea5f2a8b3?source=collection_archive---------6-----------------------#2024-09-14](https://towardsdatascience.com/build-a-tokenizer-for-the-thai-language-from-scratch-0e4ea5f2a8b3?source=collection_archive---------6-----------------------#2024-09-14)

## 基于 BPE 算法，使用 Python 训练泰语和英语数据集，构建一个泰语多语言子词分词器的逐步指南

[](https://medium.com/@tamangmilan?source=post_page---byline--0e4ea5f2a8b3--------------------------------)[![Milan Tamang](../Images/18e8be296bcef18e8792bfc18240469a.png)](https://medium.com/@tamangmilan?source=post_page---byline--0e4ea5f2a8b3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0e4ea5f2a8b3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0e4ea5f2a8b3--------------------------------) [Milan Tamang](https://medium.com/@tamangmilan?source=post_page---byline--0e4ea5f2a8b3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0e4ea5f2a8b3--------------------------------) ·14分钟阅读·2024年9月14日

--

![](../Images/a876522a3058062625e355547ef07b75.png)

**[图片来源：作者]**：泰语分词器将泰语文本编码和解码为 Token ID，并反向操作。

**分词器**的主要任务是将原始输入文本（在我们的例子中是泰语，但也可以是任何外语）转换为数字，并将其传递给模型的 Transformer。模型的 Transformer 然后生成输出数字。再次，**分词器**将这些数字转回为用户可以理解的文本。下方的高层次图示描述了上述过程。

![](../Images/9fc0dc22dc54e54c85fdbc1377f4f4a3.png)

[图片来源：作者]: 图示展示了分词器在大型语言模型（LLM）输入和输出流中的作用。

一般来说，我们中的许多人只对学习模型的 Transformer 架构如何在后台工作感兴趣。我们往往忽视了详细学习一些重要的组件，如分词器。理解分词器如何在后台工作，并能够很好地控制其功能，可以为我们提高模型的准确性和性能提供很好的杠杆作用。

> 类似于分词器，一些在大型语言模型（LLM）实现管道中的重要组件包括数据预处理、评估、保护措施/安全性以及测试/监控。我强烈建议你深入学习这些主题。我是在实际实现我的基础多语言模型 ThaiLLM 并投入生产后，才意识到这些组件的重要性。

## 为什么你需要一个泰语分词器或任何其他外语分词器？

+   假设你正在使用通用的基于英语的分词器来预训练一个多语言的大型语言模型，如泰语、印地语、印尼语、阿拉伯语、中文等。在这种情况下，你的模型可能不会给出适合你特定领域或用例的合理输出。因此，构建你自己的分词器，选择自己喜欢的语言，肯定会帮助使模型输出更加连贯和易于理解。

+   构建自己的分词器还可以让你完全控制词汇的全面性和包容性。在注意力机制中，由于词汇的全面性，token 可以在序列的有限上下文长度内关注并从更多的 tokens 中学习。因此，它使学习更加连贯，最终有助于更好的模型推理。

好消息是，在构建泰语分词器完成后，你可以轻松地构建任何其他语言的分词器。所有构建步骤都是相同的，唯一不同的是你需要在你选择的语言的数据集上进行训练。

**现在我们已经有了所有构建我们自己分词器的充分理由。以下是构建我们泰语分词器的步骤。**

1.  构建我们自己的 BPE 算法

1.  训练分词器

1.  分词器的编码和解码功能

1.  加载并测试分词器

## **步骤 1：构建我们自己的 BPE（字节对编码）算法：**

BPE 算法在许多流行的 LLM（大型语言模型）中都有应用，如 Llama、GPT 等，用于构建它们的分词器。如果我们的模型是基于英语的，我们可以选择这些 LLM 分词器之一。由于我们正在构建泰语分词器，最佳选择是从零开始创建我们自己的 BPE 算法，并用它来构建我们的分词器。让我们首先通过下面简单的流程图理解 BPE 算法是如何工作的，然后我们就可以根据它开始构建。

![](../Images/bdc0fe33fb3088b9db81b36efeeed005.png)

[图像来自作者]：BPE 流程图。示例引用自维基百科页面（[https://en.wikipedia.org/wiki/Byte_pair_encoding](https://en.wikipedia.org/wiki/Byte_pair_encoding)）

流程图中的示例以英语展示，目的是为了让理解更加简便。

**让我们写代码来实现我们泰语分词器的 BPE 算法。**

```py
# A simple practice example to get familiarization with utf-8 encoding to convert strings to bytes. 
text = "How are you คุณเป็นอย่างไร"      # Text string in both English and Thai
text_bytes = text.encode("utf-8")
print(f"Text in byte: {text_bytes}")

text_list = list(text_bytes)          # Converts text bytes to a list of integer
print(f"Text list in integer: {text_list}")
```

```py
# As I don't want to reinvent the wheel, I will be referencing most of the code block from Andrej Karpathy's GitHub (https://github.com/karpathy/minbpe?tab=readme-ov-file).
# However, I'll be modifying code blocks specific to building our Thai language tokenizer and also explaining the codes so that you can understand how each code block works and make it easy when you implement code for your use case later.

# This module provides access to the Unicode Character Database (UCD) which defines character properties for all Unicode characters.
import unicodedata

# This function returns a dictionary with consecutive pairs of integers and their counts in the given list of integers.
def get_stats(ids, stats=None):

    stats = {} if stats is None else stats
    # zip function allows to iterate consecutive items from given two list
    for pair in zip(ids, ids[1:]): 
        # If a pair already exists in the stats dictionary, add 1 to its value else assign the value as 0.
        stats[pair] = stats.get(pair, 0) + 1
    return stats

# Once we find out the list of consecutive pairs of integers, we'll then replace those pairs with new integer tokens.
def merge(ids, pair, idx):
    newids = []
    i = 0
    # As we'll be merging a pair of ids, hence the minimum id in the list should be 2 or more.
    while i < len(ids):
        # If the current id and next id(id+1) exist in the given pair, and the position of id is not the last, then replace the 2 consecutive id with the given index value.
        if ids[i] == pair[0] and i < len(ids) - 1 and ids[i+1] == pair[1]:
            newids.append(idx)
            i += 2  # If the pair is matched, the next iteration starts after 2 positions in the list.
        else:
            newids.append(ids[i])
            i += 1  # Since the current id pair didn't match, so start iteration from the 1 position next in the list.
    # Returns the Merged Ids list
    return newids

# This function checks that using 'unicodedata.category' which returns "C" as the first letter if it is a control character and we'll have to replace it readable character.
def replace_control_characters(s: str) -> str:
    chars = []
    for ch in s:
        # If the character is not distorted (meaning the first letter doesn't start with "C"), then append the character to chars list.
        if unicodedata.category(ch)[0] != "C":
            chars.append(ch) 
        # If the character is distorted (meaning the first letter has the letter "C"), then replace it with readable bytes and append to chars list.
        else:
            chars.append(f"\\u{ord(ch):04x}") 
    return "".join(chars)

# Some of the tokens such as control characters like Escape Characters can't be decoded into valid strings. 
# Hence those need to be replace with readable character such as �
def render_token(t: bytes) -> str:    
    s = t.decode('utf-8', errors='replace')
    s = replace_control_characters(s)
    return s
```

上面代码块中定义的两个函数***get_stats***和***merge***是我们泰语分词器的 BPE 算法实现。现在算法已经准备好。让我们写代码来训练我们的分词器。

## 步骤 2：训练分词器：

训练分词器涉及生成一个词汇表，这是一个包含唯一标记（单词和子单词）及其唯一索引编号的数据库。我们将使用**泰语维基数据集**从Hugging Face来训练我们的泰语分词器。就像训练LLM需要大量数据一样，你也需要大量数据来训练分词器。你也可以使用相同的数据集来同时训练LLM和分词器，虽然这不是必须的。对于多语言LLM，建议以2:1的比例使用英语和泰语数据集，这是许多从业者遵循的标准做法。

**让我们开始编写训练代码。**

```py
# Import Regular Expression
import regex as re    

# Create a Thai Tokenizer class.
class ThaiTokenizer():

  def __init__(self):

        # The byte pair should be done within the related words or sentences that give a proper context. Pairing between unrelated words or sentences may give undesirable output.
        # To prevent this behavior, we'll implement the LLama 3 regular expression pattern to make meaningful chunks of our text before implementing the byte pair algorithm.
        self.pattern = r"(?i:'s|'t|'re|'ve|'m|'ll|'d)|[^\r\n\p{L}\p{N}]?\p{L}+|\p{N}{1,3}| ?[^\s\p{L}\p{N}]+[\r\n]*|\s*[\r\n]+|\s+(?!\S)|\s+"                
        self.compiled_pattern = re.compile(self.pattern)

        # Special tokens are used to provide coherence in the sequence while training.
        # Special tokens are assigned a unique index number and stored in vocabulary. 
        self.special_tokens = {
            '<|begin_of_text|>': 1101,
            '<|end_of_text|>': 1102,
            '<|start_header_id|>': 1103,
            '<|end_header_id|>': 1104,
            '<|eot_id|>': 1105
        }

        # Initialize merges with empty dictionary
        self.merges = {}

        # Initialize the vocab dictionary by calling the function _build_vocab which is defined later in this class.
        self.vocab = self._build_vocab()

  # Tokenizer training function
  def train(self, text, vocab_size):

        # Make sure the vocab size must be at least 256 as the utf-8 encoding for the range 0-255 are same as the Ascii character.
        assert vocab_size >= 256
        # Total number of merges into the vocabulary.
        num_merges = vocab_size - 256

        # The first step is to make sure to split the text up into text chunks using the pattern defined above.
        text_chunks = re.findall(self.compiled_pattern, text)

        # Each text_chunks will be utf-8 encoded to bytes and then converted into an integer list.
        ids = [list(ch.encode("utf-8")) for ch in text_chunks]

        # Iteratively merge the most common pairs to create new tokens
        merges = {} # (int, int) -> int
        vocab = {idx: bytes([idx]) for idx in range(256)} # idx -> bytes

        # Until the total num_merges is reached, find the common pair of consecutive id in the ids list and start merging them to create a new token
        for i in range(num_merges):
            # Count the number of times every consecutive pair appears
            stats = {}
            for chunk_ids in ids:
                # Passing in stats will update it in place, adding up counts
                get_stats(chunk_ids, stats)
            # Find the pair with the highest count
            pair = max(stats, key=stats.get)
            # Mint a new token: assign it the next available id
            idx = 256 + i
            # Replace all occurrences of pair in ids with idx
            ids = [merge(chunk_ids, pair, idx) for chunk_ids in ids]
            # Save the merge
            merges[pair] = idx
            vocab[idx] = vocab[pair[0]] + vocab[pair[1]]

        # Save class variables to be used later during tokenizer encode and decode
        self.merges = merges 
        self.vocab = vocab   

  # Function to return a vocab dictionary combines with merges and special tokens
  def _build_vocab(self):
        # The utf-8 encoding for the range 0-255 are same as the Ascii character. 
        vocab = {idx: bytes([idx]) for idx in range(256)}

        # Iterate through merge dictionary and add into vocab dictionary
        for (p0, p1), idx in self.merges.items():
            vocab[idx] = vocab[p0] + vocab[p1]

        # Iterate through special token dictionary and add into vocab dictionary
        for special, idx in self.special_tokens.items():
            vocab[idx] = special.encode("utf-8")

        return vocab

  # After training is complete, use the save function to save the model file and vocab file.
  # Model file will be used to load the tokenizer model for further use in llm
  # Vocab file is just for the purpose of human verification
  def save(self, file_prefix):        
        # Writing to model file 
        model_file = file_prefix + ".model"           # model file name

        # Model write begins
        with open(model_file, 'w') as f:            
            f.write("thai tokenizer v1.0\n")          # write the tokenizer version
            f.write(f"{self.pattern}\n")              # write the pattern used in tokenizer            
            f.write(f"{len(self.special_tokens)}\n")  # write the length of special tokens

            # Write each special token in the specific format like below
            for tokens, idx in self.special_tokens.items():
                f.write(f"{tokens} {idx}\n")

            # Write only the keys part from the merges dict
            for idx1, idx2 in self.merges:
                f.write(f"{idx1} {idx2}\n")

        # Writing to the vocab file
        vocab_file = file_prefix + ".vocab"       # vocab file name

        # Change the position of keys and values of merge dict and store into inverted_merges 
        inverted_merges = {idx: pair for pair, idx in self.merges.items()}        
        # Vocab write begins
        with open(vocab_file, "w", encoding="utf-8") as f:
            for idx, token in self.vocab.items():
                # render_token function processes tokens and prevents distorted bytes by replacing them with readable character
                s = render_token(token)
                # If the index of vocab is present in merge dict, then find its child index, convert their corresponding bytes in vocab dict and write the characters
                if idx in inverted_merges:                    
                    idx0, idx1 = inverted_merges[idx]
                    s0 = render_token(self.vocab[idx0])
                    s1 = render_token(self.vocab[idx1])
                    f.write(f"[{s0}][{s1}] -> [{s}] {idx}\n")
                # If index of vocab is not present in merge dict, just write it's index and the corresponding string
                else:                    
                    f.write(f"[{s}] {idx}\n")

  # Function to load tokenizer model. 
  # This function is invoked only after the training is complete and the tokenizer model file is saved.
  def load(self, model_file):

        merges = {}             # Initialize merge and special_tokens with empty dict
        special_tokens = {}     # Initialize special_tokens with empty dict
        idx = 256               # As the range (0, 255) is already reserved in vocab. So the next index only starts from 256 and onwards.

        # Read model file
        with open(model_file, 'r', encoding="utf-8") as f:

            version = f.readline().strip()          # Read the tokenizer version as defined during model file writing            
            self.pattern = f.readline().strip()     # Read the pattern used in tokenizer            
            num_special = int(f.readline().strip()) # Read the length of special tokens

            # Read all the special tokens and store in special_tokens dict defined earlier
            for _ in range(num_special):
                special, special_idx = f.readline().strip().split()
                special_tokens[special] = int(special_idx)

            # Read all the merge indexes from the file. Make it a key pair and store it in merge dictionary defined earlier. 
            # The value of this key pair would be idx(256) as defined above and keep on increase by 1\.            
            for line in f:
                idx1, idx2 = map(int, line.split())
                merges[(idx1, idx2)] = idx
                idx += 1

        self.merges = merges                  
        self.special_tokens = special_tokens  

        # Create a final vocabulary dictionary by combining merge, special_token and vocab (0-255). _build_vocab function helps to do just that.
        self.vocab = self._build_vocab() 
```

## 第3步：分词器的编码和解码功能：

+   **分词器编码：** 分词器的编码功能查找词汇表，将给定的输入文本或提示转换为整数ID列表。这些ID随后被输入到转换器块中。

+   **分词器解码：** 分词器的解码功能查找词汇表，将来自转换器分类块生成的ID列表转换为输出文本。

让我们看一下下面的图表，以便更清楚地理解。

![](../Images/8d677b110b92dc2cb9ac0c4822965467.png)

[图片来源：作者]：泰语分词器的编码和解码功能

**让我们编写代码来实现分词器的编码和解码功能。**

```py
# Tokenizer encode function takes text as a string and returns integer ids list
  def encode(self, text):      

        # Define a pattern to identify special token present in the text
        special_pattern = "(" + "|".join(re.escape(k) for k in self.special_tokens) + ")"        
        # Split special token (if present) from the rest of the text
        special_chunks = re.split(special_pattern, text)        
        # Initialize empty ids list 
        ids = []                                                

        # Loop through each of parts in the special chunks list.
        for part in special_chunks:
            # If the part of the text is the special token, get the idx of the part from the special token dictionary and append it to the ids list.
            if part in self.special_tokens:                
                ids.append(self.special_tokens[part])            
            # If the part of text is not a special token 
            else:                
                # Split the text into multiple chunks using the pattern we've defined earlier.
                text_chunks = re.findall(self.compiled_pattern, text)

                # All text chunks are encoded separately, then the results are joined                
                for chunk in text_chunks:
                    chunk_bytes = chunk.encode("utf-8")   # Encode text to bytes                    
                    chunk_ids = list(chunk_bytes)         # Convert bytes to list of integer  

                    while len(chunk_ids) >= 2:    # chunks ids list must be at least 2 id to form a byte-pair
                        # Count the number of times every consecutive pair appears
                        stats = get_stats(chunk_ids)
                        # Some idx pair might be created with another idx in the merge dictionary. Hence we'll find the pair with the lowest merge index to ensure we cover all byte pairs in the merge dict.
                        pair = min(stats, key=lambda p: self.merges.get(p, float("inf")))

                        # Break the loop and return if the pair is not present in the merges dictionary                        
                        if pair not in self.merges:
                            break 
                        # Find the idx of the pair present in the merges dictionary
                        idx = self.merges[pair]
                        # Replace the occurrences of pair in ids list with this idx and continue
                        chunk_ids = merge(chunk_ids, pair, idx)                    

                    ids.extend(chunk_ids)                
        return ids

  # Tokenizer decode function takes a list of integer ids and return strings
  def decode(self, ids):

        # Initialize empty byte list
        part_bytes = []
        # Change the position of keys and values of special_tokens dict and store into inverse_special_tokens 
        inverse_special_tokens = {v: k for k, v in self.special_tokens.items()}

        # Loop through idx in the ids list
        for idx in ids:
            # If the idx is found in vocab dict, get the bytes of idx and append them into part_bytes list
            if idx in self.vocab:
                part_bytes.append(self.vocab[idx])
            # If the idx is found in inverse_special_tokens dict, get the token string of the corresponding idx, convert it to bytes using utf-8 encode and then append it into part_bytes list
            elif idx in inverse_special_tokens:
                part_bytes.append(inverse_special_tokens[idx].encode("utf-8"))
            # If the idx is not found in both vocab and special token dict, throw an invalid error
            else:
                raise ValueError(f"invalid token id: {idx}")

        # Join all the individual bytes from the part_byte list
        text_bytes = b"".join(part_bytes)

        # Convert the bytes to text string using utf-8 decode function. Make sure to use "errors=replace" to replace distorted characters with readable characters such as �.
        text = text_bytes.decode("utf-8", errors="replace")
        return text
```

## 第4步：加载并测试分词器：

最后，来到了本文的精彩部分。在这一部分，我们将进行两个有趣的任务。

+   首先，用Hugging Face的泰语维基数据集训练我们的分词器。我们选择了一个较小的数据集（2.2 MB），以便加快训练速度。然而，实际应用中，你应该选择一个更大的数据集以获得更好的结果。训练完成后，我们将保存模型。

+   其次，我们将加载保存的分词器模型，并测试分词器的编码和解码功能。

**让我们深入了解。**

```py
# Train the tokenizer

import time   # To caculate the duration of training completion
# Load training raw text data (thai_wiki dataset) from huggingface. thai_wiki_small.text: https://github.com/tamangmilan/thai_tokenizer
texts = open("/content/thai_wiki_small.txt", "r", encoding="utf-8").read()
texts = texts.strip()
# Define vocab size
vocab_size = 512
# Initialize a tokenizer model class
tokenizer = ThaiTokenizer()
# Start train a tokenizer
start_time = time.time()
tokenizer.train(texts, vocab_size)
end_time = time.time()
# Save tokenizer: you can change path and filename.
tokenizer.save("./models/thaitokenizer")
print(f"Total time to complete tokenizer training: {end_time-start_time:.2f} seconds")

# Output: Total time to complete tokenizer training: 186.11 seconds (3m 6s) [Note: Training duration will be longer if vocab_size is bigger and lesser for smaller vocab_size]
```

```py
# Test the tokenizer

# Initialize a tokenizer model class
tokenizer = ThaiTokenizer()
# Load tokenizer model. This model was saved during training.
tokenizer.load("./models/thaitokenizer.model")
# Invoke and verify the tokenizer encode and decode function for English Language
eng_texts = "When society evolved in different lands"
print(f"English Text: {eng_texts}")
encoded_ids = tokenizer.encode(eng_texts)
print(f"Encoded Ids: {encoded_ids}")
decoded_texts = tokenizer.decode(encoded_ids)
print(f"Decoded Texts: {decoded_texts}\n")

# Invoke and verify the tokenizer encode and decode function for Thai Language
thai_texts = "เมื่อสังคมมีวิวัฒนาการขึ้นในดินแดนต่าง"
print(f"Thai Text: {thai_texts}")
thai_encoded_ids = tokenizer.encode(thai_texts)
print(f"Encoded Ids: {thai_encoded_ids}")
thai_decoded_texts = tokenizer.decode(thai_encoded_ids)
print(f"Decoded Texts: {thai_decoded_texts}")
```

![](../Images/935f6773b6e220ece558ac08625cfef3.png)

**[泰语分词器]：泰语和英语文本的编码与解码输出。**

完美。我们的泰语分词器现在可以成功且准确地对泰语和英语文本进行编码和解码。

你有没有注意到，英文文本的编码ID比泰语编码ID要长？这是因为我们仅使用泰语数据集来训练我们的分词器。因此，分词器只能为泰语构建一个全面的词汇表。由于我们没有使用英文数据集进行训练，分词器必须从字符级别开始编码，这就导致了更长的编码ID。正如我之前提到的，对于多语言LLM，你应该以2:1的比例训练英语和泰语数据集。这样可以获得平衡且高质量的结果。

**就这样！** 我们现在已经成功地从零开始仅使用 Python 创建了我们自己的泰语分词器。而且，我觉得这真的很酷。有了这个，你可以轻松地为任何外语构建分词器。这将在实现你的多语言大语言模型时为你提供很大的帮助。

**非常感谢阅读！**

[链接到 Google Colab 笔记本](https://github.com/tamangmilan/thai_tokenizer/blob/main/build_thai_tokenizer.ipynb)

**参考文献**

[1] Andrej Karpathy, Git Hub: [Karpthy/minbpe](https://github.com/karpathy/minbpe?tab=readme-ov-file)
