# 在 15 分钟内设置一个基于 CPU 的本地 LLM 和聊天 UI

> 原文：[`towardsdatascience.com/set-up-a-local-llm-on-cpu-with-chat-ui-in-15-minutes-4cdc741408df?source=collection_archive---------1-----------------------#2024-02-06`](https://towardsdatascience.com/set-up-a-local-llm-on-cpu-with-chat-ui-in-15-minutes-4cdc741408df?source=collection_archive---------1-----------------------#2024-02-06)

## 这篇博客文章展示了如何轻松地在本地运行一个大语言模型（LLM），并在 4 个简单步骤中设置一个类似 ChatGPT 的图形用户界面。

[](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--4cdc741408df--------------------------------)![Kasper Groes Albin Ludvigsen](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--4cdc741408df--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4cdc741408df--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4cdc741408df--------------------------------) [Kasper Groes Albin Ludvigsen](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--4cdc741408df--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4cdc741408df--------------------------------) ·阅读时间 5 分钟·2024 年 2 月 6 日

--

![](img/4af6944f035f6558d066e724d2f69d26.png)

图片来源：Liudmila Shuvalova，来自 Unsplash

感谢全球开源社区，现在在消费者笔记本电脑或基于 CPU 的服务器上运行高效的大型语言模型（LLM）并通过精心设计的图形用户界面与之交互比以往任何时候都更容易。

这对所有不允许或不愿使用需要将数据发送给第三方的服务的组织特别有价值。

本教程展示了如何在四个简单步骤中设置一个本地 LLM，并配有一个简洁的 ChatGPT 样式的 UI。如果你已经安装了必要的软件，整个过程不超过 15 分钟（不包括某些步骤中计算机处理时间）。

本教程假设你已经在机器上安装了以下软件：

+   [Ollama](https://ollama.ai/)

+   Docker

+   React

+   Python 和常用包，包括 transformers

现在让我们开始吧。

# 第一步 — 决定使用哪个 Huggingface LLM

第一步是决定你想在本地运行哪个 LLM。也许你已经有了想法。如果没有，对于英语，Mistral 7b 的[指令版本](https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.2)似乎是首选。对于丹麦语，我推荐 Munin-NeuralBeagle，尽管它在生成令牌时可能过度（可能是因为它合并了一个未经过指令微调的模型）。对于其他斯堪的纳维亚语言，请参见[ScandEval](https://scandeval.com/)对斯堪的纳维亚生成模型的评估。

一旦你决定了使用哪个 LLM，复制 Huggingface“路径”到该模型。对于 Mistral 7b，它的路径是“mistralai/Mistral-7B-v0.1”。你将在下一步中用到它。

[](/how-to-make-a-pytorch-transformer-for-time-series-forecasting-69e073d4061e?source=post_page-----4cdc741408df--------------------------------) ## 如何制作一个用于时间序列预测的 PyTorch Transformer

### 本文将向你展示如何一步步地将时间序列 Transformer 架构图转换为 PyTorch 代码。

[towardsdatascience.com

# 步骤 2 – 对 LLM 进行量化

下一步是对你选择的模型进行量化，除非你选择了一个已经量化的模型。如果你的模型名称以 GGUF 或 GPTQ 结尾，它已经是量化过的。

量化是一种技术，它将模型的权重（即它的学习参数）转换为比原始数据类型更小的类型，例如从 fp16 转换为 int4。这使得模型占用更少的内存，同时也加快了推理速度，如果你在 CPU 上运行，这是一项很有用的功能。

我在我的仓库[local_llm](https://github.com/KasperGroesLudvigsen/local_llm)中的脚本`quantize.py`改编自[Maxime Labonne 的精彩 Colab 笔记本](https://colab.research.google.com/drive/1P646NEg33BZy4BfLDNpTz0V0lwIU3CHu?usp=sharing)（可以查看他的[LLM 课程](https://github.com/mlabonne/llm-course)获取更多 LLM 资源）。你可以使用他的笔记本或者我的脚本。这个方法已在 Mistral 和类似 Mistral 的模型上进行过测试。

要进行量化，首先克隆我的仓库：

```py
git clone https://github.com/KasperGroesLudvigsen/local_llm.git 
```

现在，在`quantize.py`文件中更改`MODEL_ID`变量，以反映你选择的模型。这就是你需要在第一步中复制的 Huggingface“路径”。所以如果你想使用 Mistral 7b：

```py
MODEL_ID = "mistralai/Mistral-7B-v0.1"
```

然后，在终端中运行脚本：

```py
python quantize.py
```

这将需要一些时间。在量化过程运行时，你可以继续进行下一步。

该脚本将生成一个目录，其中包含你选择的模型的模型文件以及量化版本的模型，量化版本的文件扩展名为“.gguf”。

[](/chatgpts-energy-use-per-query-9383b8654487?source=post_page-----4cdc741408df--------------------------------) ## ChatGPT 每次查询的能源使用

### ChatGPT 回答一个问题需要多少电量？

[towardsdatascience.com

# 步骤 3：构建并运行 Ollama 版本的模型

我们将使用[Ollama](https://ollama.ai/)运行该模型。Ollama 是一个软件框架，它将模型封装成一个 API。Ollama 还可以轻松与各种前端集成，正如我们将在下一步中看到的那样。

要构建模型的 Ollama 镜像，你需要一个所谓的模型文件，这是一个配置 Ollama 镜像的纯文本文件。如果你熟悉 Dockerfile，Ollama 的模型文件会让你觉得很熟悉。

在下面的示例中，我们首先指定要使用的 LLM。假设您的仓库中有一个名为 `mistral7b` 的文件夹，并且该文件夹包含名为 `quantized.gguf` 的模型。接着，我们将模型的上下文窗口设置为 8,000——Mistral 7b 的最大上下文大小。在 Modelfile 文件中，您还可以指定要使用的提示模板，并可以指定停止标记。

保存模型文件，例如保存为 Modelfile.txt。

欲了解更多配置选项，请参见 [Ollama 的 GitHub](https://github.com/ollama/ollama/blob/main/docs/modelfile.md)

```py
FROM ./mistral7b/quantized.gguf

PARAMETER num_ctx 8000

TEMPLATE """<|im_start|>system {{ .System }}<|im_end|><|im_start|>user {{ .Prompt }}<|im_end|><|im_start|>assistant<|im_end|>"""

PARAMETER stop <|im_end|>
PARAMETER stop <|im_start|>user
PARAMETER stop <|end|>
```

现在您已经创建了 Modelfile，通过在终端运行以下命令，构建一个 Ollama 镜像。这也需要几分钟时间：

```py
ollama create choose-a-model-name -f <location of the file e.g. ./Modelfile>'
```

当“创建”过程完成后，通过运行以下命令启动 Ollama 服务器。这将以一种 GUI 可以与其交互的方式暴露所有您的 Ollama 模型。

```py
ollama serve
```

[](/how-to-estimate-and-reduce-the-carbon-footprint-of-machine-learning-models-49f24510880?source=post_page-----4cdc741408df--------------------------------) ## 如何估算和减少机器学习模型的碳足迹

### 有两种简单的方法可以估算机器学习模型的碳足迹，以及 17 个减少碳足迹的想法

towardsdatascience.com

# 第四步 – 设置 Ollama 的聊天 UI

下一步是设置一个 GUI 与 LLM 进行交互。对此有几种选择。在本教程中，我们将使用“Chatbot Ollama”——一个非常整洁的 GUI，具有 ChatGPT 的感觉。“Ollama WebUI”是一个类似的选择。您还可以 [使用 Streamlit 设置自己的聊天 GUI](https://docs.streamlit.io/knowledge-base/tutorials/build-conversational-apps)。

通过运行以下两个命令，您将首先克隆 Chatbot Ollama 的 GitHub 仓库，然后安装 React 依赖：

```py
git clone https://github.com/ivanfioravanti/chatbot-ollama.git
npm ci
```

下一步是从 Dockerfile 构建 Docker 镜像。如果您使用的是 Linux，您需要将 Dockerfile 中的 OLLAMA_HOST 环境变量从 `hhtp://host.docker.internal:11434` 修改为 `http://localhost:11434`。

现在，从终端执行以下命令来构建 Docker 镜像并从中运行容器。您需要站在项目的根目录下。

```py
docker build -t chatbot-ollama .

docker run -p 3000:3000 chatbot-ollama
```

GUI 现在正在您的本地计算机上的 Docker 容器中运行。在终端中，您将看到 GUI 可用的地址（例如：“http://localhost:3000”）

在浏览器中访问该地址，您现在应该能够通过 Ollama 聊天 UI 与 LLM 进行互动。

# 结论

本教程简要介绍了如何轻松设置聊天 UI，使您能够与在本地机器上运行的 LLM 进行交互。很简单，对吧？只需要四个步骤：

1.  在 Huggingface 上选择一个模型

1.  （可选）对模型进行量化

1.  将模型封装在 Ollama 镜像中

1.  构建并运行一个封装 GUI 的 Docker 容器

请记住，这一切都得以实现，因为开源真是太棒了 👏

本文的 GitHub 仓库：[`github.com/KasperGroesLudvigsen/local_llm`](https://github.com/KasperGroesLudvigsen/local_llm)

就这些！希望你喜欢这个故事，告诉我你的想法！

通过注册 Medium 会员 [HERE](https://kaspergroesludvigsen.medium.com/membership)，你可以享受 Medium 的福利并支持我的写作。

关注我，了解更多关于 AI 和可持续发展的内容，并 [订阅](https://kaspergroesludvigsen.medium.com/subscribe) 以便在我发布新故事时通过电子邮件接收。

我有时也会写关于 时间序列预测 的文章。

也可以随时在 [LinkedIn](https://www.linkedin.com/in/kaspergroesludvigsen) 上与我联系。
