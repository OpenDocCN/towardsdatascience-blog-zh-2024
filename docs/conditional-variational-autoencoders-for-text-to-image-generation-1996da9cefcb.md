# 条件变分自编码器用于文本到图像生成

> 原文：[https://towardsdatascience.com/conditional-variational-autoencoders-for-text-to-image-generation-1996da9cefcb?source=collection_archive---------3-----------------------#2024-12-21](https://towardsdatascience.com/conditional-variational-autoencoders-for-text-to-image-generation-1996da9cefcb?source=collection_archive---------3-----------------------#2024-12-21)

## 研究早期的生成架构并将其应用于从文本输入生成图像

[](https://medium.com/@rtdcunha?source=post_page---byline--1996da9cefcb--------------------------------)[![Ryan D'Cunha](../Images/7a39859e2b5e5b09ef2c60aaf6bb75ac.png)](https://medium.com/@rtdcunha?source=post_page---byline--1996da9cefcb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1996da9cefcb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1996da9cefcb--------------------------------) [Ryan D'Cunha](https://medium.com/@rtdcunha?source=post_page---byline--1996da9cefcb--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1996da9cefcb--------------------------------) ·12 分钟阅读·2024年12月21日

--

最近，我的任务是使用条件变分自编码器（CVAE）进行文本到图像的合成。作为早期的生成结构之一，它有其局限性，但实现起来相对简单。本文将从高层次介绍 CVAE，但假定读者已经具备较高水平的理解，以便涵盖应用。

生成建模是机器学习领域的一部分，专注于学习负责创建数据的底层分布。理解这些分布使得模型能够在不同的数据集之间进行泛化，促进知识转移并有效解决数据稀疏性问题。我们理想中希望得到连续的编码，同时又能保持其独特性，以便实现平滑插值，生成新的样本。

# VAE 简介

虽然典型的自编码器是确定性的，但由于变分自编码器（VAE）将潜在空间建模为概率分布，它们是概率模型。VAE 是无监督模型，将输入数据 *x* 编码为潜在表示 *z* 并从这个潜在空间重建输入数据。它们技术上不一定需要使用神经网络实现，也可以通过生成概率模型构建。然而，在当前的深度学习状态下，大多数情况下都是使用神经网络来实现的。

![](../Images/60a6f0bb9af3df9b263f45f1d50c4c95.png)

示例 VAE 框架与重参数化技巧。来源：作者

简要解释，重参数化技巧的使用是因为我们无法在潜在空间的概率分布上进行反向传播，但我们需要更新我们的编码分布。因此，我们定义了一个可微分且可逆的函数，以便我们可以对lambda和*x*求导，同时仍然保持概率元素。

![](../Images/cb15e673b3172b4a80e27abb94b8b050.png)

z的重参数化技巧。来源：作者

VAE通过ELBO损失进行训练，ELBO损失包含重构项和编码模型与先验分布之间的Kullback-Leibler散度（KLD）。

![](../Images/dc52cd2b04c93b4d7697ae504602e3e4.png)

VAE的损失函数，左侧是KLD项，右侧是重构项[1]

# 向VAE添加条件输入

CVAE通过将额外信息（如类别标签）作为条件变量引入，扩展了VAE。这种条件化使得CVAE能够生成受控的结果。条件输入特性可以在架构中的不同点添加，但通常是在编码器和解码器之间插入。带有条件输入的损失函数是传统VAE中ELBO损失的适应版。

![](../Images/2b6e5854bf9987b4bb9ce4d79aa204b4.png)

VAE的损失函数，左侧是KLD项，右侧是重构项[2]

为了说明VAE和CVAE之间的区别，两个网络都在Fashion-MNIST数据集上使用卷积编码器和解码器架构进行了训练。每个网络的潜在空间tSNE图如图所示。

![](../Images/8c67ef17277457da060975b9d2a4e8cd.png)

VAE（左）和CVAE（右）的潜在空间流形。来源：作者

普通VAE显示出明显的聚类，而CVAE则具有更均匀的分布。普通VAE将类别和类别变异编码到潜在空间中，因为没有提供条件信号。然而，CVAE不需要学习类别区分，潜在空间可以集中在类别内的变异。因此，CVAE可能学习更多信息，因为它不依赖于学习基本的类别条件。

# CVAE的模型架构

创建了两种模型架构来测试图像生成。第一种架构是带有串联条件方法的卷积CVAE。所有网络都是为大小为28x28（共784个像素）的Fashion-MNIST图像构建的。

```py
class ConcatConditionalVAE(nn.Module):
    def __init__(self, latent_dim=128, num_classes=10):
        super().__init__()
        self.latent_dim = latent_dim
        self.num_classes = num_classes

        # Encoder
        self.encoder = nn.Sequential(
            nn.Conv2d(1, 32, 3, stride=2, padding=1),
            nn.ReLU(),
            nn.Conv2d(32, 64, 3, stride=2, padding=1),
            nn.ReLU(),
            nn.Conv2d(64, 128, 3, stride=2, padding=1),
            nn.ReLU(),
            nn.Flatten()
        )

        self.flatten_size = 128 * 4 * 4

        # Conditional embedding
        self.label_embedding = nn.Embedding(num_classes, 32)

        # Latent space (with concatenated condition)
        self.fc_mu = nn.Linear(self.flatten_size + 32, latent_dim)
        self.fc_var = nn.Linear(self.flatten_size + 32, latent_dim)

        # Decoder
        self.decoder_input = nn.Linear(latent_dim + 32, 4 * 4 * 128)

        self.decoder = nn.Sequential(
            nn.ConvTranspose2d(128, 64, 2, stride=2, padding=1, output_padding=1),
            nn.ReLU(),
            nn.ConvTranspose2d(64, 32, 3, stride=2, padding=1, output_padding=1),
            nn.ReLU(),
            nn.ConvTranspose2d(32, 1, 3, stride=2, padding=1, output_padding=1),
            nn.Sigmoid()
        )

    def encode(self, x, c):
        x = self.encoder(x)
        c = self.label_embedding(c)
        # Concatenate condition with encoded input
        x = torch.cat([x, c], dim=1)

        mu = self.fc_mu(x)
        log_var = self.fc_var(x)
        return mu, log_var

    def reparameterize(self, mu, log_var):
        std = torch.exp(0.5 * log_var)
        eps = torch.randn_like(std)
        return mu + eps * std

    def decode(self, z, c):
        c = self.label_embedding(c)
        # Concatenate condition with latent vector
        z = torch.cat([z, c], dim=1)
        z = self.decoder_input(z)
        z = z.view(-1, 128, 4, 4)
        return self.decoder(z)

    def forward(self, x, c):
        mu, log_var = self.encode(x, c)
        z = self.reparameterize(mu, log_var)
        return self.decode(z, c), mu, log_var
```

CVAE编码器由3个卷积层组成，每个卷积层后跟一个ReLU非线性激活函数。编码器的输出随后被展平。类编号通过嵌入层传递，并与编码器输出相加。然后，使用重新参数化技巧，通过2个线性层在潜在空间中获得μ和σ。一旦采样，重新参数化后的潜在空间输出将传递给解码器，并与类编号的嵌入层输出拼接。解码器由3个反卷积层组成。前两个层包含ReLU非线性激活函数，最后一层包含Sigmoid非线性激活函数。解码器的输出是一个28x28的生成图像。

另一种模型架构遵循相同的方法，但采用条件输入而不是拼接。一个主要问题是，添加或拼接是否会导致更好的重建或生成结果。

```py
class AdditiveConditionalVAE(nn.Module):
    def __init__(self, latent_dim=128, num_classes=10):
        super().__init__()
        self.latent_dim = latent_dim
        self.num_classes = num_classes

        # Encoder
        self.encoder = nn.Sequential(
            nn.Conv2d(1, 32, 3, stride=2, padding=1),
            nn.ReLU(),
            nn.Conv2d(32, 64, 3, stride=2, padding=1),
            nn.ReLU(),
            nn.Conv2d(64, 128, 3, stride=2, padding=1),
            nn.ReLU(),
            nn.Flatten()
        )

        self.flatten_size = 128 * 4 * 4

        # Conditional embedding
        self.label_embedding = nn.Embedding(num_classes, self.flatten_size)

        # Latent space (without concatenation)
        self.fc_mu = nn.Linear(self.flatten_size, latent_dim)
        self.fc_var = nn.Linear(self.flatten_size, latent_dim)

        # Decoder condition embedding
        self.decoder_label_embedding = nn.Embedding(num_classes, latent_dim)

        # Decoder
        self.decoder_input = nn.Linear(latent_dim, 4 * 4 * 128)

        self.decoder = nn.Sequential(
            nn.ConvTranspose2d(128, 64, 2, stride=2, padding=1, output_padding=1),
            nn.ReLU(),
            nn.ConvTranspose2d(64, 32, 3, stride=2, padding=1, output_padding=1),
            nn.ReLU(),
            nn.ConvTranspose2d(32, 1, 3, stride=2, padding=1, output_padding=1),
            nn.Sigmoid()
        )

    def encode(self, x, c):
        x = self.encoder(x)
        c = self.label_embedding(c)
        # Add condition to encoded input
        x = x + c

        mu = self.fc_mu(x)
        log_var = self.fc_var(x)
        return mu, log_var

    def reparameterize(self, mu, log_var):
        std = torch.exp(0.5 * log_var)
        eps = torch.randn_like(std)
        return mu + eps * std

    def decode(self, z, c):
        # Add condition to latent vector
        c = self.decoder_label_embedding(c)
        z = z + c
        z = self.decoder_input(z)
        z = z.view(-1, 128, 4, 4)
        return self.decoder(z)

    def forward(self, x, c):
        mu, log_var = self.encode(x, c)
        z = self.reparameterize(mu, log_var)
        return self.decode(z, c), mu, log_var
```

所有CVAEs都使用相同的损失函数，计算公式如上所示。

```py
def loss_function(recon_x, x, mu, logvar):
    """Computes the loss = -ELBO = Negative Log-Likelihood + KL Divergence.
        Args:
        recon_x: Decoder output.
        x: Ground truth.
        mu: Mean of Z
        logvar: Log-Variance of Z
    """
    BCE = F.binary_cross_entropy(recon_x, x, reduction='sum')
    KLD = -0.5 * torch.sum(1 + logvar - mu.pow(2) - logvar.exp())
    return BCE + KLD 
```

为了评估模型生成的图像，通常使用3个定量指标。均方误差（MSE）通过计算生成图像与真实图像之间每个像素的差值平方和来得到。结构相似性指数（SSIM）是一种通过比较两幅图像的结构信息、亮度和对比度来评估图像质量的指标[3]。SSIM可以用于比较任何大小的图像，而MSE则与像素大小相关。SSIM的得分范围从-1到1，1表示图像完全相同。Frechet起始距离（FID）是量化生成图像的真实感和多样性的指标。由于FID是一种距离度量，较低的得分表示对一组图像的更好重建。

# 来自Fashion-MNIST的短文本到图像

在扩展到完整的文本到图像之前，首先在Fashion-MNIST数据集上测试了CVAEs的图像重建和生成。Fashion-MNIST是一个类似于MNIST的数据集，包含60,000个训练样本和10,000个测试样本。每个样本都是28x28的灰度图像，并且与10个类中的一个标签相关联[4]。

创建了预处理函数，通过输入短文本正则表达式匹配提取包含类名的相关关键词。对于大多数类，使用了额外的描述符（同义词），以涵盖每个类中包含的相似时尚物品（例如，外套与夹克）。

```py
classes = {
'Shirt':0,
'Top':0,
'Trouser':1,
'Pants':1,
'Pullover':2,
'Sweater':2,
'Hoodie':2,
'Dress':3,
'Coat':4,
'Jacket':4,
'Sandal':5,
'Shirt':6,
'Sneaker':7,
'Shoe':7,
'Bag':8,
'Ankle boot':9,
'Boot':9
}

def word_to_text(input_str, classes, model, device):
  label = class_embedding(input_str, classes)
  if label == -1: return Exception("No valid label")
  samples = sample_images(model, num_samples=4, label=label, device=device)
  plot_samples(samples, input_str, torch.tensor([label]))
  return

def class_embedding(input_str, classes):
  for key in list(classes.keys()):
    template = f'(?i)\\b{key}\\b'
    output = re.search(template, input_str)
    if output: return classes[key]
  return -1
```

类名随后被转换为其类编号，并作为条件输入单独传递给CVAE。为了生成图像，从短文本描述中提取的类标签被传递到解码器，同时从高斯分布中随机采样以输入来自潜在空间的变量。

在测试生成之前，先进行图像重建测试，以确保CVAE的功能。由于创建了一个28x28图像的卷积网络，该网络可以在不到一个小时的时间内，经过不到100个epoch进行训练。

![](../Images/183a79e8aaa8367f5e79255acba2d555.png)

CVAE重建结果与真实图像对比（左为真实图像，右为模型输出）。来源：作者

重建结果包含了地面真实图像的大致形状，但图像中缺少清晰的高频特征。任何包含文本或复杂设计图案的内容在模型输出中都呈现模糊状态。输入任何包含Fashion-MNIST类别的短文本会生成类似于重建图像的输出。

![](../Images/28aa56efd802c265552db30458c473bb.png)

从CVAE Fashion-MNIST生成的图像“服饰”。来源：作者

生成的图像的MSE为11，SSIM为0.76。这些构成了较好的生成结果，表明在简单、小型图像中，CVAEs可以生成高质量的图像。GANs和DDPMs将在具有复杂特征的图像中生成更高质量的图像，但CVAEs能够处理简单的情况。

# 长文本到图像生成，使用CLIP和COCO

当扩展到生成任何长度的文本图像时，除了常规的表达式匹配方法外，还需要更强大的方法。为此，使用了OpenAI的CLIP将文本转换为高维嵌入向量。该嵌入模型采用ViT-B/32配置，输出长度为512的嵌入向量。CLIP模型的一个限制是它的最大标记长度为77，研究表明有效长度甚至小于20 [5]。因此，在输入文本包含多个句子的情况下，文本会按句子拆分并通过CLIP编码器处理。然后将得到的嵌入向量进行平均，以创建最终的输出嵌入。

长文本模型需要比Fashion-MNIST更复杂的训练数据，因此使用了COCO数据集。COCO数据集有标注（这些标注并不完全健全，但稍后会讨论），可以将这些标注传递给CLIP以获得嵌入向量。然而，COCO图像的大小为640x480，这意味着即使使用裁剪转换，也需要一个更大的网络。对于长文本到图像的生成，测试了添加和连接条件输入架构，但此处展示的是连接方法：

```py
class cVAE(nn.Module):
    def __init__(self, latent_dim=128):
        super().__init__()

        device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

        self.clip_model, _ = clip.load("ViT-B/32", device=device)
        self.clip_model.eval()
        for param in self.clip_model.parameters():
            param.requires_grad = False

        self.latent_dim = latent_dim

        # Modified encoder for 128x128 input
        self.encoder = nn.Sequential(
            nn.Conv2d(3, 32, 4, stride=2, padding=1),  # 64x64
            nn.BatchNorm2d(32),
            nn.ReLU(),
            nn.Conv2d(32, 64, 4, stride=2, padding=1),  # 32x32
            nn.BatchNorm2d(64),
            nn.ReLU(),
            nn.Conv2d(64, 128, 4, stride=2, padding=1),  # 16x16
            nn.BatchNorm2d(128),
            nn.ReLU(),
            nn.Conv2d(128, 256, 4, stride=2, padding=1),  # 8x8
            nn.BatchNorm2d(256),
            nn.ReLU(),
            nn.Conv2d(256, 512, 4, stride=2, padding=1),  # 4x4
            nn.BatchNorm2d(512),
            nn.ReLU(),
            nn.Flatten()
        )

        self.flatten_size = 512 * 4 * 4  # Flattened size from encoder

        # Process CLIP embeddings for encoder
        self.condition_processor_encoder = nn.Sequential(
            nn.Linear(512, 1024)
        )

        self.fc_mu = nn.Linear(self.flatten_size + 1024, latent_dim)
        self.fc_var = nn.Linear(self.flatten_size + 1024, latent_dim)

        self.decoder_input = nn.Linear(latent_dim + 512, 512 * 4 * 4)

        # Modified decoder for 128x128 output
        self.decoder = nn.Sequential(
            nn.ConvTranspose2d(512, 256, 4, stride=2, padding=1),  # 8x8
            nn.BatchNorm2d(256),
            nn.ReLU(),
            nn.ConvTranspose2d(256, 128, 4, stride=2, padding=1),  # 16x16
            nn.BatchNorm2d(128),
            nn.ReLU(),
            nn.ConvTranspose2d(128, 64, 4, stride=2, padding=1),  # 32x32
            nn.BatchNorm2d(64),
            nn.ReLU(),
            nn.ConvTranspose2d(64, 32, 4, stride=2, padding=1),  # 64x64
            nn.BatchNorm2d(32),
            nn.ReLU(),
            nn.ConvTranspose2d(32, 16, 4, stride=2, padding=1),  # 128x128
            nn.BatchNorm2d(16),
            nn.ReLU(),
            nn.Conv2d(16, 3, 3, stride=1, padding=1),  # 128x128
            nn.Sigmoid()
        )

    def encode_condition(self, text):
        with torch.no_grad():
            embeddings = []
            for sentence in text:
                embeddings.append(self.clip_model.encode_text(clip.tokenize(sentence).to('cuda')).type(torch.float32))
            return torch.mean(torch.stack(embeddings), dim=0)

    def encode(self, x, c):
        x = self.encoder(x)
        c = self.condition_processor_encoder(c)
        x = torch.cat([x, c], dim=1)
        return self.fc_mu(x), self.fc_var(x)

    def reparameterize(self, mu, log_var):
        std = torch.exp(0.5 * log_var)
        eps = torch.randn_like(std)
        return mu + eps * std

    def decode(self, z, c):
        z = torch.cat([z, c], dim=1)
        z = self.decoder_input(z)
        z = z.view(-1, 512, 4, 4)
        return self.decoder(z)

    def forward(self, x, c):
        mu, log_var = self.encode(x, c)
        z = self.reparameterize(mu, log_var)
        return self.decode(z, c), mu, log_var
```

另一个主要的研究点是对不同大小图像的生成与重建。具体来说，将COCO图像修改为64x64、128x128和256x256的大小。训练网络后，应该首先测试重建结果。

![](../Images/b1c38dec6034d3615eacc75be851c892.png)

使用不同图像尺寸对COCO进行CVAE重建。来源：作者

所有图像尺寸的重建结果都包含了背景的基本形状、某些特征的轮廓和正确的颜色。然而，随着图像尺寸的增大，更多的特征能够被恢复。这是有道理的，虽然训练一个大尺寸图像的模型需要更长时间，但可以捕捉到更多的信息并被模型学习。

在图像生成中，生成高质量图像极其困难。大多数图像都有一定程度的背景和图像中的模糊特征。这是从CVAE生成图像时的预期情况。这种情况出现在条件输入的拼接和加法中，但拼接方法表现得更好。这可能是因为拼接的条件输入不会干扰重要特征，并确保信息能够清晰地保留下来。如果条件不相关，可以忽略。然而，加法条件输入可能会干扰现有特征，并在反向传播过程中完全破坏网络的权重更新。

![](../Images/fcd471faa0f0229c3e1e220ed0f71cae.png)

在COCO上通过CVAE生成的图像。来源：作者

所有通过COCO生成的图像的SSIM值大约为0.4，远低于Fashion-MNIST上的SSIM值。MSE与图像大小成正比，因此很难量化差异。COCO图像生成的FID值在200左右，这进一步证明了COCO CVAE生成的图像不够鲁棒。

# CVAEs在图像生成中的局限性

使用CVAEs进行图像生成的最大限制是，嗯，就是CVAEs本身。所能包含和重建/生成的信息量极度依赖于潜在空间的大小。潜在空间太小无法捕获任何有意义的信息，且与输出图像的大小成正比。28x28的图像需要比64x64图像小得多的潜在空间（因为它按图像大小的平方比例变化）。然而，潜在空间比实际图像大则会增加不必要的信息，这时只是创建一个一对一的映射。对于COCO数据集，至少需要512维的潜在空间来捕获一些特征。虽然CVAEs是生成模型，但卷积编码器和解码器是相当基础的网络。GAN的训练方式或DDPM的复杂去噪过程则能生成更为复杂的图像。

图像生成中的另一个主要限制是所使用的训练数据集。尽管COCO数据集有注释，但这些注释并不十分详细。为了训练复杂的生成模型，应使用不同的数据集进行训练。COCO没有提供背景细节的位置信息或其他额外信息。来自CLIP编码器的复杂特征向量无法在COCO上有效地用于CVAE。

尽管CVAEs和在COCO上的图像生成存在局限性，但它创造了一个可行的图像生成模型。如果需要更多代码和细节，欢迎随时联系！

# 参考文献

[1] Kingma, Diederik P, 等人。“自编码变分贝叶斯。”*arXiv:1312.6114*（2013年）。

[2] Sohn, Kihyuk, 等人。“使用深度条件生成模型学习结构化输出表示。”*NeurIPS会议论文集*（2015年）。

[3] Nilsson, J., 等人。“理解SSIM。”*arXiv:2102.12037*（2020年）。

[4] Xiao, Han 等. “Fashion-mnist: 用于基准测试机器学习算法的全新图像数据集。” *arXiv:2403.15378* （2024年）（MIT许可证）。

[5] Zhang, B. 等. “Long-clip: 解锁clip的长文本能力。” *arXiv:2403.15378* （2024年）。

感谢我的小组项目伙伴：Jake Hession（德勤顾问）、Ashley Hong（谷歌软件工程师）和Julian Kuppel（量化分析师）！
