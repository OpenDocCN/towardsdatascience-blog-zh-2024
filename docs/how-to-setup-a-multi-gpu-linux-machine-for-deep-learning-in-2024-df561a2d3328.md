# 如何在2024年设置一个用于深度学习的多GPU Linux机器

> 原文：[https://towardsdatascience.com/how-to-setup-a-multi-gpu-linux-machine-for-deep-learning-in-2024-df561a2d3328?source=collection_archive---------0-----------------------#2024-05-19](https://towardsdatascience.com/how-to-setup-a-multi-gpu-linux-machine-for-deep-learning-in-2024-df561a2d3328?source=collection_archive---------0-----------------------#2024-05-19)

## 使用多个GPU进行深度学习

## 在几分钟内快速设置CUDA和PyTorch！

[](https://medium.com/@nirajkamal?source=post_page---byline--df561a2d3328--------------------------------)[![Nika](../Images/fcf9dfec64ccae5ea841fcc5046817d6.png)](https://medium.com/@nirajkamal?source=post_page---byline--df561a2d3328--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--df561a2d3328--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--df561a2d3328--------------------------------) [Nika](https://medium.com/@nirajkamal?source=post_page---byline--df561a2d3328--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--df561a2d3328--------------------------------) ·6分钟阅读·2024年5月19日

--

![](../Images/3cef01ab529215b4b49adeed49721c78.png)

作者提供的图像：多GPU机器（卡通图）

随着深度学习模型（尤其是LLM）不断变得更大，开发和本地使用这些模型对GPU内存（VRAM）的需求日益增加。构建或获得一台多GPU机器仅仅是挑战的第一部分。大多数库和应用程序默认只使用单个GPU。因此，机器还需要配备适当的驱动程序以及能够利用多GPU设置的库。

本文提供了一个如何设置多GPU（Nvidia）Linux机器并安装重要库的指南。希望能够节省你在实验中的时间，帮助你更快开始开发。

最后，提供了可以利用多GPU设置进行深度学习的流行开源库的链接。

## 目标

> 设置一个多GPU的Linux系统，并安装必要的库，如CUDA工具包和PyTorch，以开始进行深度学习*🤖*。相同的步骤也适用于单GPU机器。

我们将安装1）CUDA工具包，2）PyTorch和3）Miniconda，开始使用exllamaV2和torchtune等框架进行深度学习。

©️ 本文中提到的所有库和信息均为开源和/或公开可用。

## 入门

![](../Images/cf98219698c22c0bc7ba55518467eb1f.png)

作者提供的图像：在配备8个Nvidia A10G GPU的Linux机器上运行nvidia-smi命令的输出

使用终端中的 `nvidia-smi` 命令检查机器中安装的 GPU 数量。它应该打印出所有已安装的 GPU 列表。如果有任何不一致，或者命令无法工作，请首先为你的 Linux 版本安装 Nvidia 驱动程序。确保 `nvidia-smi` 命令能正确打印出所有已安装的 GPU，如上所示。

如果尚未安装 Nvidia 驱动程序，请按照此页面安装：

[如何在 Ubuntu 22.04 上安装 NVIDIA 驱动程序 — Linux 教程 — 学习 Linux 配置](https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-ubuntu-22-04)（来源：linuxconfig.org）

## 第 1 步 安装 CUDA 工具包

💡 *检查是否存在现有的 CUDA 文件夹* `*usr/local/cuda-xx*`*。这意味着已经安装了一个版本的 CUDA。如果你已经安装了所需的 CUDA 工具包（通过在终端中使用* `*nvcc*` *命令检查），请跳到第 2 步。*

检查你所需的 PyTorch 库所需的 CUDA 版本：[从本地开始 | PyTorch](https://pytorch.org/get-started/locally/)（我们正在安装 CUDA 12.1）

前往 [CUDA Toolkit 12.1 下载 | NVIDIA 开发者](https://developer.nvidia.com/cuda-12-1-0-download-archive) 获取 Linux 安装 CUDA 12.1 的命令（选择你的操作系统版本和相应的“deb（本地）”安装类型）。

![](../Images/29309c4402baba465c39b23a0bc89e15.png)

为 Ubuntu 22 选择的选项（来源：developer.nvidia.com）

基础安装程序的终端命令将根据你选择的选项出现。将它们复制并粘贴到你的 Linux 终端中运行，以安装 CUDA 工具包。例如，对于 x86_64 Ubuntu 22，打开下载文件夹中的终端并运行以下命令：

```py
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.1.0/local_installers/cuda-repo-ubuntu2204-12-1-local_12.1.0-530.30.02-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2204-12-1-local_12.1.0-530.30.02-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2204-12-1-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda
```

⚠️*在安装 CUDA 工具包时，安装程序可能会提示更新内核。如果终端中出现任何提示更新内核的弹窗，按* `*esc*` *按钮取消。此阶段不要更新内核！——这样可能会破坏你的 Nvidia 驱动程序* ☠️。

安装完成后，请重新启动 Linux 机器。`nvcc` 命令仍然无法使用。你需要将 CUDA 安装路径添加到 PATH 中。使用 nano 编辑器打开 `.bashrc` 文件。

```py
nano /home/$USER/.bashrc
```

滚动到 `.bashrc` 文件的底部，并添加以下两行：

```py
 export PATH="/usr/local/cuda-12.1/bin:$PATH"
 export LD_LIBRARY_PATH="/usr/local/cuda-12.1/lib64:$LD_LIBRARY_PATH"
```

💡 *请注意，你可以将* `*cuda-12.1*` *更改为你安装的 CUDA 版本，* `*cuda-xx*` *，如果将来需要，‘xx’ 表示你的 CUDA 版本。*

保存更改并关闭 nano 编辑器：

```py
 To save changes - On you keyboard, press the following: 

 ctrl + o             --> save 
 enter or return key  --> accept changes
 ctrl + x             --> close editor
```

关闭并重新打开终端。现在，`nvcc--version` 命令应该在终端中打印已安装的 CUDA 版本。

## 第 2 步 安装 Miniconda

在安装 PyTorch 之前，最好先安装 Miniconda，然后在 Conda 环境中安装 PyTorch。为每个项目创建一个新的 Conda 环境也是很方便的。

打开下载文件夹中的终端，并运行以下命令：

```py
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh

# initiate conda
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
```

关闭并重新打开终端。现在，`conda` 命令应该可以正常工作。

## 第 3 步 安装 PyTorch

（可选）— 为你的项目创建一个新的 conda 环境。你可以将 `<environment-name>` 替换为你喜欢的名称。我通常使用项目名称来命名。💡 *你可以在工作前后使用* `*conda activate <environment-name>*` *和* `*conda deactivate <environment-name>*` *命令。*

```py
conda create -n <environment-name> python=3.11

# activate the environment
conda activate <environment-name>
```

为你的 CUDA 版本安装 PyTorch 库。以下命令适用于我们安装的 cuda-12.1 版本：

```py
pip3 install torch torchvision torchaudio
```

上述命令来源于 PyTorch 安装指南 — [Start Locally | PyTorch](https://pytorch.org/get-started/locally/)。

![](../Images/2cb51751b02a7e251fb9eb15d57fab1e.png)

（来源：pytorch.org）

安装 PyTorch 后，检查在终端中 PyTorch 可见的 GPU 数量。

```py
python

>> import torch
>> print(torch.cuda.device_count())
8
```

这应该打印出系统中安装的 GPU 数量（在我的案例中是 8 个），并且应与 `nvidia-smi` 命令中列出的 GPU 数量一致。

完成！你已经准备好开始使用多 GPU 进行深度学习项目了 🥳。

# 下一步？开始进行利用你的多 GPU 设置的深度学习项目（LLM）。

1\. 🤗 开始时，你可以从**Hugging Face**克隆一个流行的模型：

[](https://huggingface.co/meta-llama/Meta-Llama-3-8B?source=post_page-----df561a2d3328--------------------------------) [## meta-llama/Meta-Llama-3-8B · Hugging Face

### 我们的目标是通过开源和开放科学推动人工智能的发展和普及。

[huggingface.co](https://huggingface.co/meta-llama/Meta-Llama-3-8B?source=post_page-----df561a2d3328--------------------------------)

2\. 💬 对于推理（使用 LLM 模型），请在一个单独的环境中克隆并安装**exllamav2**。这将使用你所有的 GPU 来加速推理：（查看我的 Medium 页面获取详细教程）

[](https://github.com/turboderp/exllamav2?source=post_page-----df561a2d3328--------------------------------) [## GitHub - turboderp/exllamav2: A fast inference library for running LLMs locally on modern…

### 一款适用于现代消费级 GPU 上运行 LLM 的快速推理库 - turboderp/exllamav2

[github.com](https://github.com/turboderp/exllamav2?source=post_page-----df561a2d3328--------------------------------)

3\. 👨‍🏫 对于微调或训练，你可以克隆并安装**torchtune**。按照说明进行 `full finetune` 或 `lora finetune`，并利用你所有的 GPU：（查看我的 Medium 页面获取详细教程）

[## GitHub - pytorch/torchtune: A Native-PyTorch Library for LLM Fine-tuning

### 一个用于 LLM 微调的原生 PyTorch 库。通过在…创建账户参与 pytorch/torchtune 的开发

[github.com](https://github.com/pytorch/torchtune?source=post_page-----df561a2d3328--------------------------------)

# 结论

本指南将带你完成多 GPU 深度学习所需的机器设置。现在，你可以开始进行任何使用多 GPU 的项目——例如 torchtune 来加速开发！

**敬请关注**有关**exllamaV2**和**torchtune**的更多详细教程。
