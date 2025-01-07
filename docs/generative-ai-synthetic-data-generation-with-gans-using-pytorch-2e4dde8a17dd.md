# 生成式人工智能：使用Pytorch生成对抗网络（GANs）合成数据

> 原文：[https://towardsdatascience.com/generative-ai-synthetic-data-generation-with-gans-using-pytorch-2e4dde8a17dd?source=collection_archive---------1-----------------------#2024-01-15](https://towardsdatascience.com/generative-ai-synthetic-data-generation-with-gans-using-pytorch-2e4dde8a17dd?source=collection_archive---------1-----------------------#2024-01-15)

## **解开复杂性的谜团：超越图像和语言模型**

[](https://medium.com/@ns650?source=post_page---byline--2e4dde8a17dd--------------------------------)[![Najib Sharifi, Ph.D.](../Images/d94932c5e3633e32247d98a3c221b181.png)](https://medium.com/@ns650?source=post_page---byline--2e4dde8a17dd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2e4dde8a17dd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2e4dde8a17dd--------------------------------) [Najib Sharifi, Ph.D.](https://medium.com/@ns650?source=post_page---byline--2e4dde8a17dd--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e4dde8a17dd--------------------------------) ·7分钟阅读·2024年1月15日

--

生成式模型因其生成美丽且逼真的图像和语言模型（例如ChatGPT）而变得极为流行，这些模型在各个行业中的应用日益增多。这些生成式模型可以说是人工智能/机器学习现在备受关注（或担忧）的原因；因为它展示了机器学习的巨大潜力，尤其是向那些非专业领域的人展示了这一点。网络上已经有很多关于GANs模型的资源，但大多数都集中在图像生成上。这些图像生成和语言模型需要复杂的空间或时间细节，这增加了额外的复杂性，使得读者更难理解GANs的真正本质。

为了改善这一点并使 GAN 更加容易为更广泛的受众所接受，在这段简短的讨论和 GAN 模型示例中，我们将采取一种不同且更实用的方法，重点是生成数学函数的合成数据。除了作为学习用途的简化外，合成数据生成本身也变得越来越重要。数据不仅在商业决策中扮演着核心角色，而且越来越多的使用场景中，数据驱动的方法正变得比从第一原理建模更为流行。一个令人兴奋的例子是天气预报，第一原理模型包括了简化版本的纳维-斯托克斯方程，并通过数值方法求解（我得补充一句，计算成本非常高）。然而，近期使用深度学习进行天气预报的尝试（例如，看看 Nvidia 的 FourCastNet [1]）在捕捉天气模式方面取得了非常成功的成果，一旦训练完成，运行起来更容易且速度更快。

**生成模型与判别模型**

在机器学习中，理解判别模型与生成模型之间的区别非常重要，因为它们是 GAN 的关键组成部分。让我们简要地解开这些术语：

判别模型：

判别模型专注于将数据分类到预定义的类别中，例如将狗和猫的图片分类到各自的类别中。与捕获整个分布不同，这些模型分辨出不同类别之间的边界。它们输出 P(y|x)（给定输入数据 x 时，类别 y 的概率），即它们回答给定数据点属于哪个类别的问题。

生成模型：

生成模型旨在理解数据的潜在结构。与判别模型通过类别进行区分不同，生成模型学习数据的整个分布。这些模型输出 p(x|y)，即它们回答在给定类别 y 的情况下，生成特定数据点 x 的概率。

这两种模型之间的相互作用构成了 GAN 的基础。

**GAN — 结构和组成部分**

![](../Images/1fa83d74ad0c8fcc4dcca0381c8f8226.png)

GAN 解剖学的示意图。图片来源：[Tingting Zhu](https://www.researchgate.net/publication/376301143_A_Survey_of_Generative_Adversarial_Networks_for_Synthesizing_Structured_Electronic_Health_Records) [2]

现在，让我们探索这些概念如何在 GAN 模型中结合起来。GAN 的关键组成部分包括噪声向量、生成器和判别器。

生成器：生成真实数据

为了生成合成数据，生成器使用一个随机噪声向量作为输入。在试图欺骗判别器的过程中，生成器旨在学习真实数据的分布，并生成无法与真实数据区分的合成数据。这里的问题是，对于相同的输入，它总是生成相同的输出（想象一个生成图像的模型，它生成一个逼真的图像，但总是相同的图像，这并不太有用）。随机噪声向量为过程注入了随机性，从而为生成的输出提供了多样性。

判别器：区分真实与虚假

判别器就像一个艺术评论家，经过训练能够区分真实和虚假的数据。它的角色是仔细审视接收到的数据，并为该数据的真实性分配一个概率分数。如果合成数据与真实数据相似，判别器会分配一个高概率分数，否则分配一个低概率分数。

对抗性训练：一个动态的对决

生成器努力学习生成判别器无法区分的合成数据，同时，判别器也在不断学习并提高其区分真实与合成数据的能力。这一动态训练过程促使两个模型不断完善自己的技能。这两个模型始终在竞争（因此它被称为对抗性），通过这种竞争，两个模型都能在各自的角色中变得非常出色。

**使用Pytorch实现GAN**

让我们通过一个创建GAN的示例来继续。在这个示例中，我们在PyTorch中实现一个能够生成合成数据的模型。对于训练，我们有一个包含6个参数的数据集，具有以下形状（所有参数作为参数1的函数被绘制）。每个参数的分布和形状被故意选择得有显著的不同，以增加数据集的复杂性并模拟现实世界的数据。然而，值得一提的是，判别器和生成器的架构仍有很大的优化空间，但在本教程中我们不会关注这一点。

在本教程中，我假设你已经理解了常见的人工神经网络（ANN）模型架构和Python编程。我在代码中提供了注释，以帮助你理解代码。

![](../Images/9dca607323f861b859fe959578580dd0.png)

训练数据 — 所有6个参数作为参数1的函数被绘制

定义GAN模型组件（生成器和判别器）

```py
import torch
from torch import nn
from tqdm.auto import tqdm
from torch.utils.data import DataLoader
import matplotlib.pyplot as plt
import torch.nn.init as init
import pandas as pd
import numpy as np
from torch.utils.data import Dataset

# defining a single generation block function
def FC_Layer_blockGen(input_dim, output_dim):
    single_block = nn.Sequential(
        nn.Linear(input_dim, output_dim),

        nn.ReLU()
    )
    return single_block

# DEFINING THE GENERATOR
class Generator(nn.Module):
    def __init__(self, latent_dim, output_dim):
        super(Generator, self).__init__()
        self.model = nn.Sequential(
            nn.Linear(latent_dim, 256),
            nn.ReLU(),
            nn.Linear(256, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Linear(512, output_dim),
            nn.Tanh()  
        )

    def forward(self, x):
        return self.model(x)

#defining a single discriminattor block       
def FC_Layer_BlockDisc(input_dim, output_dim):
    return nn.Sequential(
        nn.Linear(input_dim, output_dim),
        nn.ReLU(),
        nn.Dropout(0.4)
    )

# Defining the discriminator

class Discriminator(nn.Module):
    def __init__(self, input_dim):
        super(Discriminator, self).__init__()
        self.model = nn.Sequential(
            nn.Linear(input_dim, 512),
            nn.ReLU(),
            nn.Dropout(0.4),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Dropout(0.4),
            nn.Linear(512, 256),
            nn.ReLU(),
            nn.Dropout(0.4),
            nn.Linear(256, 1),
            nn.Sigmoid()
        )

    def forward(self, x):
        return self.model(x)

#Defining training parameters
batch_size = 128
num_epochs = 500
lr = 0.0002
num_features = 6
latent_dim = 20

# MODEL INITIALIZATION
generator = Generator(noise_dim, num_features)
discriminator = Discriminator(num_features)

# LOSS FUNCTION AND OPTIMIZERS
criterion = nn.BCELoss()
gen_optimizer = torch.optim.Adam(generator.parameters(), lr=lr)
disc_optimizer = torch.optim.Adam(discriminator.parameters(), lr=lr)
```

模型初始化与数据处理

```py
# IMPORTING DATA
file_path = 'SamplingData7.xlsx'
data = pd.read_excel(file_path)
X = data.values
X_normalized = torch.FloatTensor((X - X.min(axis=0)) / (X.max(axis=0) - X.min(axis=0)) * 2 - 1)
real_data = X_normalized

#Creating a dataset

class MyDataset(Dataset):
    def __init__(self, dataframe):
        self.data = dataframe.values.astype(float)
        self.labels = dataframe.values.astype(float)

    def __len__(self):
        return len(self.data)

    def __getitem__(self, idx):
        sample = {
            'input': torch.tensor(self.data[idx]),
            'label': torch.tensor(self.labels[idx])
        }
        return sample

# Create an instance of the dataset
dataset = MyDataset(data)

# Create DataLoader
dataloader = DataLoader(dataset, batch_size=batch_size, shuffle=True, drop_last=True)

def weights_init(m):
    if isinstance(m, nn.Linear):
        init.xavier_uniform_(m.weight)
        if m.bias is not None:
            init.constant_(m.bias, 0)

pretrained = False
if pretrained:
    pre_dict = torch.load('pretrained_model.pth')
    generator.load_state_dict(pre_dict['generator'])
    discriminator.load_state_dict(pre_dict['discriminator'])
else:
    # Apply weight initialization
    generator = generator.apply(weights_init)
    discriminator = discriminator.apply(weights_init)
```

模型训练

```py
model_save_freq = 100

latent_dim =20
for epoch in range(num_epochs):
    for batch in dataloader:
        real_data_batch = batch['input']
        # Train discriminator on real data
        real_labels = torch.FloatTensor(np.random.uniform(0.9, 1.0, (batch_size, 1)))
        disc_optimizer.zero_grad()
        output_real = discriminator(real_data_batch)
        loss_real = criterion(output_real, real_labels)
        loss_real.backward()

        # Train discriminator on generated data
        fake_labels = torch.FloatTensor(np.random.uniform(0, 0.1, (batch_size, 1)))
        noise = torch.FloatTensor(np.random.normal(0, 1, (batch_size, latent_dim)))
        generated_data = generator(noise)
        output_fake = discriminator(generated_data.detach())
        loss_fake = criterion(output_fake, fake_labels)
        loss_fake.backward()

        disc_optimizer.step()

        # Train generator 
        valid_labels = torch.FloatTensor(np.random.uniform(0.9, 1.0, (batch_size, 1)))
        gen_optimizer.zero_grad()
        output_g = discriminator(generated_data)
        loss_g = criterion(output_g, valid_labels)
        loss_g.backward()
        gen_optimizer.step()

    # Print progress
    print(f"Epoch {epoch}, D Loss Real: {loss_real.item()}, D Loss Fake: {loss_fake.item()}, G Loss: {loss_g.item()}")
```

评估和可视化结果

```py
import seaborn as sns

# Generate synthetic data 
synthetic_data = generator(torch.FloatTensor(np.random.normal(0, 1, (real_data.shape[0], noise_dim))))

# Plot the results
fig, axs = plt.subplots(2, 3, figsize=(12, 8))
fig.suptitle('Real and Synthetic Data Distributions', fontsize=16)

for i in range(2):
    for j in range(3):
        sns.histplot(synthetic_data[:, i * 3 + j].detach().numpy(), bins=50, alpha=0.5, label='Synthetic Data', ax=axs[i, j], color='blue')
        sns.histplot(real_data[:, i * 3 + j].numpy(), bins=50, alpha=0.5, label='Real Data', ax=axs[i, j], color='orange')
        axs[i, j].set_title(f'Parameter {i * 3 + j + 1}', fontsize=12)
        axs[i, j].set_xlabel('Value')
        axs[i, j].set_ylabel('Frequency')
        axs[i, j].legend()

plt.tight_layout(rect=[0, 0.03, 1, 0.95])
plt.show()

# Create a 2x3 grid of subplots
fig, axs = plt.subplots(2, 3, figsize=(15, 10))
fig.suptitle('Comparison of Real and Synthetic Data', fontsize=16)

# Define parameter names
param_names = ['Parameter 1', 'Parameter 2', 'Parameter 3', 'Parameter 4', 'Parameter 5', 'Parameter 6']

# Scatter plots for each parameter
for i in range(2):
    for j in range(3):
        param_index = i * 3 + j
        sns.scatterplot(real_data[:, 0].numpy(), real_data[:, param_index].numpy(), label='Real Data', alpha=0.5, ax=axs[i, j])
        sns.scatterplot(synthetic_data[:, 0].detach().numpy(), synthetic_data[:, param_index].detach().numpy(), label='Generated Data', alpha=0.5, ax=axs[i, j])
        axs[i, j].set_title(param_names[param_index], fontsize=12)
        axs[i, j].set_xlabel(f'Real Data - {param_names[param_index]}')
        axs[i, j].set_ylabel(f'Real Data - {param_names[param_index]}')
        axs[i, j].legend()

plt.tight_layout(rect=[0, 0.03, 1, 0.95])
plt.show()
```

![](../Images/dc6dbaad057f7434bcbb3812ed77ae2c.png)![](../Images/66571da2455b2846c32dd5a59fda6ba8.png)

尽管我们的模型很简单，但合成数据和真实数据的分布及数学形态非常相似！训练过程和模型架构可以进行修改，以提高准确性，但这不是我们在这里关注的内容。这个模型可以很容易地调整，以生成其他应用的合成数据，适用于具有更多参数和更复杂的真实物理系统。感谢您抽出时间阅读，希望您觉得这篇文章有启发性。使用生成对抗网络（GANs）可以做很多事情，它是一个非常激动人心的话题，绝对值得尝试这个代码，了解GAN的整体概念，然后开始尝试其他的创意！祝您好运！

*除非另有注明，所有图片均为作者提供*

**参考文献**

[1] Jaideep Pathak, Shashank Subramanian, Peter Harrington, Sanjeev Raja, Ashesh Chattopadhyay, Morteza Mardani, Thorsten Kurth, David Hall, Zongyi Li, Kamyar Azizzadenesheli, Pedram Hassanzadeh, Karthik Kashinath, Animashree Anandkumar. (2022). FourCastNet：一种使用自适应傅里叶神经算子的全球数据驱动高分辨率天气模型。arXiv:2202.11214\. [https://doi.org/10.48550/arXiv.2202.11214](https://doi.org/10.48550/arXiv.2202.11214)

[2] Ghosheh, Ghadeer & Jin, Li & Zhu, Tingting. (2023). 生成对抗网络在合成结构化电子健康记录中的应用调研。ACM计算调查。10.1145/3636424。
