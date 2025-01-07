# 使用StyleGAN-2 ADA增强癌症检测

> 原文：[https://towardsdatascience.com/enhancing-cancer-detection-with-stylegan-2-ada-aee55ef99c5b?source=collection_archive---------5-----------------------#2024-01-22](https://towardsdatascience.com/enhancing-cancer-detection-with-stylegan-2-ada-aee55ef99c5b?source=collection_archive---------5-----------------------#2024-01-22)

## 针对数据匮乏的深度神经网络进行数据增强。

[](https://medium.com/@ianstebbs?source=post_page---byline--aee55ef99c5b--------------------------------)[![Ian Stebbins](../Images/50ece59dc136f7d41e02c046ea1216e4.png)](https://medium.com/@ianstebbs?source=post_page---byline--aee55ef99c5b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aee55ef99c5b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--aee55ef99c5b--------------------------------) [Ian Stebbins](https://medium.com/@ianstebbs?source=post_page---byline--aee55ef99c5b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aee55ef99c5b--------------------------------) ·8分钟阅读·2024年1月22日

--

*作者：* [*Ian Stebbins*](https://www.linkedin.com/in/ian-stebbins-244a1722b/)*,* [*Benjamin Goldfried*](https://www.linkedin.com/in/benjamin-goldfried/)*,* [*Ben Maizes*](https://www.linkedin.com/in/benjamin-maizes/)

## **简介**

对于许多领域特定的问题，数据的匮乏可能会妨碍深度神经网络的有效性，甚至使其无法使用。然而，生成对抗网络（GAN）的最新架构使我们能够通过创建捕捉数据分布中细节、纹理和变异的新样本来合成增强数据。这些合成数据可以作为深度神经网络的额外训练输入，从而使数据稀缺的领域任务变得更加可行。

在这个项目中，我们使用了带有自适应判别器增强（ADA）的NVIDIA StyleGAN-2，应用于一个小型的[胸部CT扫描数据集](https://www.kaggle.com/datasets/mohamedhanyyy/chest-ctscan-images/code?datasetId=839140&sortBy=voteCount)（许可协议：[数据库：开放数据库，内容：© 原作者](http://opendatacommons.org/licenses/odbl/1.0/)）[1]。此外，我们构建了一个CNN分类器，用于区分正常扫描与肿瘤扫描。通过将不同比例的合成生成数据注入到不同模型的训练过程中，我们能够评估仅使用真实数据与使用真实-合成混合数据模型之间的性能差异。

## **StyleGAN-2 ADA**

StyleGAN-2与ADA首次由NVIDIA在2020年NeurIPS论文中提出：[“在有限数据上训练生成对抗网络”](https://nvlabs-fi-cdn.nvidia.com/stylegan2-ada/ada-paper.pdf) [2]。过去，在小数据集上训练GAN通常会导致网络判别器过拟合。因此，判别器往往不是学习区分真实数据和生成数据，而是倾向于记住训练集中的噪声和异常值的模式，而不是学习数据分布的一般趋势。为了解决这个问题，ADA根据训练中观察到的过拟合程度动态调整数据增强的强度。这有助于模型更好地进行泛化，并在小数据集上实现更好的GAN性能。

## **增强数据集**

要使用StyleGAN-2 ADA模型，我们使用了来自GitHub的官方NVIDIA模型实现，具体可以在[这里](https://github.com/NVlabs/stylegan3)找到。*请注意，这是StyleGAN-3的仓库，但仍然可以运行StyleGAN-2。*

```py
!git clone https://github.com/NVlabs/stylegan3
```

根据你的设置，你可能需要安装依赖项并进行一些其他预处理。例如，我们选择将数据集图像调整大小并缩小为224x224，因为我们只有一个GPU，使用更大的图像尺寸在计算上更加昂贵。我们选择使用224x224的图像尺寸，因为我们为CNN选择的预训练模型ResNet优化了这种图像大小。

```py
!pip install pillow
from PIL import Image
import os

'''Loops through the files in an input folder (input_folder), resizes them to a
specified new size (new_size), an adds them to an output folder (output_folder).'''
def resize_images_in_folder(input_folder, output_folder, new_size):
    # Loop through all files in the input folder
    for filename in os.listdir(input_folder):
        input_path = os.path.join(input_folder, filename)

        # Check if the file is an image
        if os.path.isfile(input_path) and filename.lower().endswith(('.png', '.jpg', '.jpeg', '.gif')):
            # Open the image file
            image = Image.open(input_path)

            #Convert to RGB
            image = image.convert('RGB')

            # Resize the image
            resized_image = image.resize(new_size)

            # Generate the output file path
            output_path = os.path.join(output_folder, filename)

            # Save the resized image to the output folder
            resized_image.save(output_path)

            print(f"Resized {filename} and saved to {output_path}")
```

要开始训练过程，首先导航到你克隆的仓库目录，然后运行以下命令。

```py
import os

!python dataset_tool.py --source= "Raw Data Directory" --dest="Output Directory" --resolution='256x256'

# Training
EXPERIMENTS = "Output directory where the Network Pickle File will be saved""
DATA = "Your Training DataSet Directory"
SNAP = 10
KIMG = 80

# Build the command and run it
cmd = f"/usr/bin/python3 /content/stylegan3/train.py --snap {SNAP} --outdir {EXPERIMENTS} --data {DATA}  --kimg {KIMG} --cfg stylegan2 --gpus 1 --batch 8 --gamma 50"
!{cmd} 
```

**SNAP**指的是你希望在多少个训练步骤（信息显示的训练步数）后拍摄网络快照并将其保存到pickle文件中。

**KIMG**指的是你希望输入到GAN中的成千上万的图像数量。

**GAMMA d**决定正则化对判别器的影响强度。

![](../Images/b34210233f78736ace829dafc60eb73c.png)![](../Images/6a3268bd615e1c08b05b12fa8d74ea19.png)

初始生成的图像

![](../Images/86495efb421750130e2efe65762c8570.png)![](../Images/948a9ccdfd5f21abf3313d44279cfa62.png)

训练期间生成的图像

一旦你的模型完成训练（根据你的计算资源，这可能需要多个小时），你就可以使用训练好的网络生成图像。

```py
pickle_file = "Network_Snapshot.pkl"
model_path = f'Path to Pickle File/{pickle_file}'
SAMPLES = Number of samples you want to generate
!python /content/stylegan3/gen_images.py --outdir=Output Directory --trunc=1 --seeds {SAMPLES} \
    --network=$model_path
```

![](../Images/43a52ee7c15a0a27c8aee5c3c86606fd.png)![](../Images/7496b11702ef63c7c146d11d852d35d2.png)

正常真实图像（左）与正常生成图像（右）

## **迁移学习与卷积神经网络**

为了评估我们合成生成数据的有效性，我们首先在原始数据上训练了一个CNN模型。一旦我们得到了测试集上的基准准确度，我们就用逐渐增加的合成数据重新训练了模型。

为了将数据输入模型，我们使用了Keras数据生成器，它将样本直接从指定目录流入模型。原始数据集有4个类别，分别代表不同类型的癌症，但为了简化问题，我们将其转化为二分类问题。我们决定从原始Kaggle数据集中选择正常类和鳞状类进行处理。

```py
# Define directories for training, validation, and test datasets
train_dir = 'Your training data directory'
test_dir = 'Your testing data directory'
val_dir = 'Your validation data directory'

# Utilize data genarators to flow directly from directories
train_generator = train_datagen.flow_from_directory(
    train_dir,
    target_size=(224, 224),
    batch_size=20,
    class_mode='binary',  #Use 'categorical' for multi-class classification
    shuffle=True,
    seed=42 )

val_generator = val_datagen.flow_from_directory(
    val_dir,
    target_size=(224, 224),
    batch_size=20,
    class_mode='binary',
    shuffle=True )

test_generator = test_datagen.flow_from_directory(
    test_dir,
    target_size=(224, 224),
    batch_size=20,
    class_mode='binary',
    shuffle=True )
```

为了构建我们的模型，我们首先使用了ResNet50基础架构和模型权重。我们选择使用ResNet50是因为它具有适中的架构大小、良好的文档支持，并且通过Keras易于使用。在导入带有Imagenet模型权重的ResNet50后，我们冻结了ResNet50的层，并在其上添加了可训练的密集层，帮助网络学习我们特定的分类任务。

我们还选择了引入批量归一化（batch normalization），通过对层输入进行归一化并减少内部协变量偏移，能够加速收敛并使训练更加稳定[3]。此外，它还可以提供一种正则化效果，有助于防止我们添加的可训练密集层发生过拟合。

![](../Images/563757ec5c2f1f0f7c1b6a808ac3f16b.png)

我们的模型架构

起初，我们的模型表现不佳。我们通过将激活函数从ReLU切换到leaky ReLU解决了这个问题。这表明我们的网络可能正面临着ReLU死亡或神经元失效的问题。简而言之，由于ReLU对负数的梯度始终为零，这可能导致神经元“死亡”，从而无法对网络作出贡献[4][5]。由于leaky ReLU对负值不为零，使用它作为激活函数有助于解决这个问题。

## **结果**

为了测试我们的合成数据，我们在5个不同实例上训练了上述CNN，分别使用了0%、25%、50%、75%和100%的附加合成样本。例如，0%的合成样本意味着数据全部为原始数据，而100%则意味着训练集包含相等数量的原始数据和合成数据。对于每个网络，我们随后使用准确度指标在一组未见过的实际测试数据上评估了性能。下图可视化了不同合成数据比例对测试准确率的影响。

![](../Images/9a3fef522d3001511884da078b5b2dc7.png)

二分类（正常与鳞状肿瘤）分类的测试准确率

训练模型时不稳定，因此我们排除了准确度为1.0或极低的迭代。这帮助我们避免了过拟合或欠拟合的训练迭代。

我们可以看到，从0%到25%时，测试准确度出现了急剧上升，这表明即使是通过少量的数据增强，也能对数据最初较少的问题产生较大影响。

由于我们仅在80 KIMG上训练了我们的GAN（由于计算限制），因此如果有更多的GAN训练迭代，我们的合成数据质量可能会更好。值得注意的是，合成数据质量的提高也可能影响上述图表。我们假设，合成数据质量的提高将导致在训练中使用合成数据的最佳比例增加。此外，如果合成图像更好地适应我们训练数据的真实分布，我们就可以在模型训练中融入更多的合成图像而不至于过拟合。

## **结论**

在这个项目中，使用GAN进行有限数据的增强被证明是扩展训练集并且更重要的是提高分类精度的有效技术。尽管我们选择了一个小而基础的问题，但这一技术可以通过几种方式轻松扩展。未来的工作可能包括使用更多的计算资源来获取更好的合成样本，引入更多的类别到分类任务中（使其成为一个多分类问题），并实验更新的GAN架构。不管怎样，使用GAN增强小数据集现在可以将许多以前受数据限制的问题纳入深度神经网络的范畴。

## Kaggle 数据集

我们将增强和重新调整大小的图像汇编成以下[Kaggle数据集](https://www.kaggle.com/datasets/benjaminmaizes/formatted-and-augmented-chest-ct-scan-images)。该数据集包含501张正常和501张鳞状的224x224合成图像，可用于进一步的实验。

[我们的*GitHub 仓库*](https://github.com/istebbins/Enhancing-Cancer-Detection-with-StyleGAN-2-ADA)

**引用**

[1] Hany, Mohamed， [胸部CT扫描图像数据集](https://www.kaggle.com/datasets/mohamedhanyyy/chest-ctscan-images/code?datasetId=839140&sortBy=voteCount)，Kaggle（2020）。

[2] Karras, Tero，等， [使用有限数据训练生成对抗网络](https://nvlabs-fi-cdn.nvidia.com/stylegan2-ada/ada-paper.pdf)（2020），《神经信息处理系统进展》2020。

[3] Ioffe, Sergey, 和 Christian Szegedy， [批量归一化：通过减少内部协变量偏移加速深度网络训练](http://proceedings.mlr.press/v37/ioffe15.pdf)，（2015），*国际机器学习会议*。pmlr，2015。

[4] He, Kaiming，等， [深入探讨修正器：在imagenet分类中超越人类级别的表现](https://arxiv.org/pdf/1502.01852.pdf)，（2015），*IEEE国际计算机视觉会议论文集*。2015。

[5] Bai, Yuhan， [RELU函数及其导函数回顾](https://www.shs-conferences.org/articles/shsconf/pdf/2022/14/shsconf_stehf2022_02006.pdf)，（2022），*SHS Web of Conferences*。第144卷。EDP Sciences，2022。
