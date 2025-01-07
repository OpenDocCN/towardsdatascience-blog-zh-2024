# 小型更智能

> 原文：[`towardsdatascience.com/smaller-is-smarter-89a9b3a5ad9e?source=collection_archive---------2-----------------------#2024-12-01`](https://towardsdatascience.com/smaller-is-smarter-89a9b3a5ad9e?source=collection_archive---------2-----------------------#2024-12-01)

## 你真的需要顶级 LLM 的算力来获得莎士比亚风格的提拉米苏食谱吗？

[](https://medium.com/@alexandre.allouin?source=post_page---byline--89a9b3a5ad9e--------------------------------)![Alexandre Allouin](https://medium.com/@alexandre.allouin?source=post_page---byline--89a9b3a5ad9e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--89a9b3a5ad9e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--89a9b3a5ad9e--------------------------------) [Alexandre Allouin](https://medium.com/@alexandre.allouin?source=post_page---byline--89a9b3a5ad9e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--89a9b3a5ad9e--------------------------------) ·4 分钟阅读·2024 年 12 月 1 日

--

对大型语言模型（LLMs）对环境影响的关注日益增加。尽管关于 LLM 实际成本的详细信息可能难以获得，但我们可以尝试收集一些事实，以了解其规模。

![](img/cdf2cd3dcb299e0cccd6759da2e88ccc.png)

由 ChatGPT-4o 生成

由于关于 ChatGPT-4 的综合数据尚不容易获取，我们可以以 Llama 3.1 405B 为例。这个 Meta 的开源模型可以说是迄今为止最“透明”的大型语言模型（LLM）。根据各种[基准测试](https://ai.meta.com/blog/meta-llama-3-1/)，Llama 3.1 405B 与 ChatGPT-4 相当，为理解这一范围内的 LLM 提供了一个合理的基础。

# 推理

运行此模型 32 位版本的硬件要求范围为 1,620 到 1,944 GB 的 GPU 内存，具体取决于来源（[substratus](https://www.substratus.ai/blog/llama-3-1-405b-gpu-requirements), [HuggingFace](https://huggingface.co/blog/llama31)）。为了保守估算，我们使用较低的数字 1,620 GB。为了更直观地理解——尽管这是一个简化的类比——1,620 GB 的 GPU 内存大约相当于 100 台标准的 MacBook Pro（每台 16GB）的总内存。因此，当你向这些 LLM 询问莎士比亚风格的提拉米苏食谱时，需要 100 台 MacBook Pro 的算力才能给出答案。

# 训练

我正试图将这些数字转化为更具体的概念……虽然这还不包括[训练成本](https://www.techtarget.com/searchenterpriseai/news/366596503/Meta-intros-its-biggest-open-source-AI-model-Llama-31-405B#:~:text=Meta%20said%20that%20to%20train,to%20train%20the%20new%20model.)，据估计，训练过程涉及大约 16,000 个 GPU，成本约为 6000 万美元（不包括硬件费用）——这是 Meta 的一项重大投资——整个过程大约耗时 80 天。在电力消耗方面，[训练需要 11 GWh](https://www.notebookcheck.net/Meta-unveils-biggest-smartest-royalty-free-Llama-3-1-405B-AI.866775.0.html)。

在像法国这样的国家，[每人年电力消耗](https://www.data.gouv.fr/fr/reuses/consommation-par-habitant-et-par-ville-delectricite-en-france/)大约为 2300 千瓦时。因此，11 GWh 大约相当于 4782 人一年的电力消耗。这一消耗释放了大约 5000 吨二氧化碳当量的温室气体（[基于欧洲平均水平](https://www.econologie.com/europe-emissions-co2-pays-kwh-electrique/)），尽管这个数字在不同国家训练模型时可能会轻松翻倍。

作为对比，燃烧 1 升柴油会产生 2.54 千克的二氧化碳。因此，在像法国这样的国家，训练 Llama 3.1 405B 的碳排放大约相当于燃烧约 200 万升柴油。这相当于大约 2800 万公里的汽车行驶。我认为这已经提供了足够的视角……而且我还没提到用于冷却 GPU 的水资源！

# 可持续性

很显然，人工智能仍处于起步阶段，我们可以预见，随着时间的推移，将会出现更为优化和可持续的解决方案。然而，在这场激烈的竞争中，OpenAI 的财务状况突显了其收入与运营开支之间的巨大差距，尤其是在推理成本方面。预计到 2024 年，公司将花费大约 40 亿美元用于由微软提供的推理工作负载处理能力，而其年收入预计在 35 亿到 45 亿美元之间。这意味着，仅推理成本几乎就等于——甚至超过——OpenAI 的总收入（[deeplearning.ai](https://www.deeplearning.ai/the-batch/openai-faces-financial-growing-pains-spending-double-its-revenue/))。

所有这些发生在一个背景下，专家们正在宣布人工智能模型的性能瓶颈（扩展范式）。与之前的飞跃相比，增加模型规模和 GPU 带来的回报显著减少，例如 GPT-4 相比于 GPT-3 取得的进展。Hugging Face 初创公司的研究员兼 AI 负责人[Sasha Luccioni](https://www.france24.com/en/live-news/20241118-is-ai-s-meteoric-rise-beginning-to-slow)表示：“追求通用人工智能（AGI）一直是不现实的，‘越大越好’的人工智能方法最终注定会遇到限制——我认为这正是我们现在看到的现象。”

# 那现在呢？

但别误会我的意思——我不是在审判人工智能，因为我爱它！这项研究阶段绝对是人工智能发展中的正常阶段。然而，我认为我们需要在使用人工智能时运用常识：我们不能每次都拿火箭筒去打蚊子。人工智能必须变得可持续——不仅是为了保护我们的环境，还为了应对社会分裂的挑战。的确，由于高昂的成本和资源需求，若将全球南方地区甩在人工智能竞赛的后头，将是这场新智能革命中的一大失败。

那么，你真的需要 ChatGPT 的全部功能来处理你 RAG 管道中的最简单任务吗？你是否想控制运营成本？你想完全掌控你的管道流程吗？你是否担心自己的私人数据在网上流通？或者你只是对人工智能的影响保持警觉，并致力于其有意识的使用？

# 小型语言模型（SLM）可能是一个更聪明的选择！

小型语言模型（SLM）提供了一个值得探索的绝佳替代方案。它们可以在你的本地基础设施上运行，并且与人类智能结合时，可以提供巨大的价值。虽然 SLM 没有统一的定义——例如，2019 年，GPT-2 凭借其 15 亿参数被视为大型语言模型（LLM），但现在已经不再是这样——我指的是像 Mistral 7B、Llama-3.2 3B 或 Phi3.5 这样的模型。这些模型可以在“一台不错的电脑”上运行，从而大大减少碳足迹，并确保在本地安装时数据的机密性。虽然它们不如大型模型多才多艺，但在特定任务中明智地使用时，仍然可以提供显著的价值——同时在环保方面更具优势。
