# LLM 微调的碳足迹——一个案例研究

> 原文：[`towardsdatascience.com/carbon-footprint-of-llm-fine-tuning-a-case-study-7703afc716a9?source=collection_archive---------9-----------------------#2024-02-22`](https://towardsdatascience.com/carbon-footprint-of-llm-fine-tuning-a-case-study-7703afc716a9?source=collection_archive---------9-----------------------#2024-02-22)

## 当我测量 LLM 指令微调的碳排放时，我得到了令人惊讶的结果。

[](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--7703afc716a9--------------------------------)![Kasper Groes Albin Ludvigsen](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--7703afc716a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7703afc716a9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7703afc716a9--------------------------------) [Kasper Groes Albin Ludvigsen](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--7703afc716a9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7703afc716a9--------------------------------) ·阅读时间 5 分钟 ·2024 年 2 月 22 日

--

![](img/26870185100a8cc707bee2bfab3c8e98.png)

图片由 Ingmar H 提供，来自 Unsplash

我最近对一个名为 Munin-7b-alpha 的丹麦 LLM 进行了 LoRA 微调，使用的微调数据集名为 SkoleGPT-instruct。在微调过程中，我测量了能耗并计算了碳足迹。本文将展示这些令人惊讶的结果。你可以在 [这里](https://huggingface.co/ThatsGroes/munin-SkoleGPTOpenOrca-7b-16bit) 找到该模型。

# 介绍

Munin-7b-alpha 是一个预训练模型（或称为基础模型），它仅被训练用于生成文本。为了使其适应聊天设置，预训练模型需要擅长遵循指令，这需要一个后续的训练步骤，称为指令微调。

与预训练不同，预训练需要大量未标记的文本数据，模型在这些数据上以自监督方式进行训练；而指令微调则需要相对较少的数据，且这些数据必须经过精心筛选和注释。

这是一篇我在本文中报道的微调过程。

[](/set-up-a-local-llm-on-cpu-with-chat-ui-in-15-minutes-4cdc741408df?source=post_page-----7703afc716a9--------------------------------) ## 在 CPU 上用聊天 UI 设置本地 LLM，只需 15 分钟

### 本文展示了如何轻松地在本地运行 LLM，并且如何在四个简单的步骤中设置类似 ChatGPT 的图形用户界面（GUI）。

towardsdatascience.com

# 方法论

Munin-7b-alpha 拥有 70 亿个参数，我使用的指令数据集包含 21,300 个样本。也就是说，21,300 个提示和良好答案的示例。

使用略微改编版的[这个精彩的模型微调笔记本](https://github.com/alexandrainst/d3a-llm-workshop)，我训练了一个 LoRA 模型，进行了 1 个 epoch，也就是说，我每次只给模型展示一个样本。

LoRA（低秩适应）是一种高效的微调技术，用于将大型语言模型（LLMs）调整到特定任务上。[Hugging Face](https://huggingface.co/docs/peft/en/package_reference/lora)提供了该技术的简明描述：

“低秩适应（LoRA）是一种 PEFT（参数高效微调）方法，它将一个大矩阵分解成两个较小的低秩矩阵，位于注意力层中。这大大减少了需要微调的参数数量。”

该模型在单个 Nvidia RTX A4000 GPU 上训练，该 GPU 是消费级 GPU，具有 16 GB 内存——足以进行该模型的 LoRA 微调。

我使用 Python 包 CodeCarbon 测量了能耗。CodeCarbon 是一个极其轻量且易于使用的包，只需两行代码就能测量 Python 脚本、函数或方法的能耗。了解如何使用它，请点击这里：

[](/how-to-estimate-and-reduce-the-carbon-footprint-of-machine-learning-models-49f24510880?source=post_page-----7703afc716a9--------------------------------) ## 如何估算和减少机器学习模型的碳足迹

### 估算机器学习模型碳足迹的两种简单方法以及 17 个减少碳足迹的建议

towardsdatascience.com

除了能耗外，CodeCarbon 还会估算计算过程所消耗的能量的碳足迹，但我发现其数据似乎并不准确。这可能是因为 CodeCarbon 使用了一个硬编码的你所在地区的平均碳强度（每产生 1KWh 的二氧化碳排放量），而不是接近实时的碳强度。因此，我访问了一个名为 Energi Data Service 的网站，它允许你下载丹麦电网的详细电力排放数据。通过将 CodeCarbon 获得的能量消耗数据与模型训练时电网的碳强度相乘，我得到了训练的碳足迹。

[](/chatgpts-energy-use-per-query-9383b8654487?source=post_page-----7703afc716a9--------------------------------) ## ChatGPT 每次查询的能耗

### ChatGPT 回答一个问题需要多少电力？

towardsdatascience.com

# 结果

微调过程只花了不到 4 小时，消耗了总共 0.694 千瓦时——根据 Python 包 CodeCarbon 的估算，这包括了 GPU、CPU 和 RAM 的总能耗。

在模型训练过程中，根据 Energi Data Service 的数据（许可：“许可方授予您全球范围内的免费、非独占且没有其他限制的使用数据许可” [1]），每千瓦时的平均 CO2e 排放量为 82.5 克。

因此，微调过程中排放了微乎其微的 57 克二氧化碳当量（0.694 千瓦时 * 82.5 克）。

作对比，平均丹麦人每年排放 11 吨二氧化碳当量。

在一项研究中发现，生成一张图像使用生成性 AI 平均消耗 2.9 瓦时[2]。因此，用于指令微调 LLM 的能量，足以生成 239 张图像。

如果你在想这样一个短暂且高效的微调过程是否能产生更好的模型，答案显然是“是的”：

根据[ScandEval 排行榜](https://scandeval.com/mainland-scandinavian-nlg/)上关于自然语言生成的排名，预训练模型在丹麦任务上的平均得分为 43.44，而微调后的模型得分为 47.55，提升了 9.45%。截至本文撰写时，这一差距使得模型从排行榜的第 5 名提升到了第 7 名。

[](/how-to-make-a-pytorch-transformer-for-time-series-forecasting-69e073d4061e?source=post_page-----7703afc716a9--------------------------------) ## 如何制作一个用于时间序列预测的 PyTorch Transformer

### 本文将逐步展示如何将时间序列 Transformer 架构图转换为 PyTorch 代码。

[towardsdatascience.com

# 讨论

令我惊讶的是，进行微调并没有需要更多的计算、能源和排放。

我预计，如果其他变量保持不变（例如使用相似的 GPU、训练方法等），我的发现将与样本数量成线性增长。即如果你在两倍的样本上进行微调，或者进行两倍的周期训练，我预计能源消耗也将翻倍。

对于一个 70 亿参数的模型，能源消耗可能会显著增加，从而导致更高的排放，但在总体上，排放可能仍然非常低。

此外，如果我没有使用 LoRA，能源消耗可能会更高。

# 结论

使用指令微调技术 LoRA 确实高效——无论是从所需时间、计算量（例如 GPU 内存）还是碳排放量来看。

使用 LoRA 对 7B LLM 进行指令微调，基于 21,300 个样本进行一个周期的训练，耗时四小时，排放了 57 克二氧化碳当量——一个非常小的数值。

就这些！希望你喜欢这个故事。让我知道你的想法！

通过注册[Medium 会员 HERE](https://kaspergroesludvigsen.medium.com/membership)来获取 Medium 的好处并支持我的写作。

关注我，了解更多关于 AI 和可持续性的内容，并且[订阅](https://kaspergroesludvigsen.medium.com/subscribe)，在我发布新故事时通过电子邮件接收更新。

我有时也会写关于时间序列预测的文章。

并欢迎通过[LinkedIn](https://www.linkedin.com/in/kaspergroesludvigsen)与我联系。

# 参考文献

[1] https://www.energidataservice.dk/Conditions_for_use_of_Danish_public_sector_data-License_for_use_of_data_in_ED.pdf

[2] “电力需求处理：瓦特驱动 AI 部署成本？”由 Luccioni 等人编写
