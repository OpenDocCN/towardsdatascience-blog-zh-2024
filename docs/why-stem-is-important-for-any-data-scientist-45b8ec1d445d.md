# 为什么 STEM 对任何数据科学家都很重要

> 原文：[https://towardsdatascience.com/why-stem-is-important-for-any-data-scientist-45b8ec1d445d?source=collection_archive---------6-----------------------#2024-11-14](https://towardsdatascience.com/why-stem-is-important-for-any-data-scientist-45b8ec1d445d?source=collection_archive---------6-----------------------#2024-11-14)

## 从我个人经验出发，有3个案例可以证明这一点

[](https://radmilamandzhi.medium.com/?source=post_page---byline--45b8ec1d445d--------------------------------)[![Radmila M.](../Images/f3722a0ca0c96b5f6abb8f23a1162488.png)](https://radmilamandzhi.medium.com/?source=post_page---byline--45b8ec1d445d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--45b8ec1d445d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--45b8ec1d445d--------------------------------) [Radmila M.](https://radmilamandzhi.medium.com/?source=post_page---byline--45b8ec1d445d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--45b8ec1d445d--------------------------------) ·8分钟阅读·2024年11月14日

--

![](../Images/c07104a43f20e38bbb4a5f6523ad4a8a.png)

图片来源：[Ashley Batz](https://unsplash.com/@ashleybatz?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 前言

曾几何时，我曾学习石油工程。说实话，我几乎是偶然进入了这个本科专业。在学校时，我喜欢物理和数学，因此我肯定想在大学学习STEM专业。那时，我对石油行业一无所知，像许多人一样，我以为石油是从地下湖泊中提取的。但因为我成功地被该项目录取，我决定尝试一下。

我不能说我后悔自己的选择，尽管我必须承认，除了实习期间，我并未在行业中工作。但我得到的是解决各种任务的科学方法，而毫无疑问，这是一份极大的礼物。

在这篇文章中，我想强调了解科学原理和法则的重要性。在大多数情况下，这些法则是基于积累的经验和长期的观察所制定的，因此它们在各个方面有着广泛的应用。数据科学也不例外，即便没有直接应用这些积累的智慧，拥有与主要科学方法的类比，也帮助我更有效地解决与数据相关的挑战性任务。

# 案例 # 1: **分解与傅里叶变换**

> 傅里叶变换是一种将复杂的波或信号分解为一组独特的正弦成分的方法。通过这种分解，我们可以检视、放大、衰减或删除每个正弦元素。

这是傅里叶变换的正式定义，从中可以看出，该方法的核心是对波的分解，以便简化它们的分析。因此，傅里叶变换在许多应用中都非常有用。例如，音乐识别服务使用傅里叶变换来识别歌曲。在语音识别中，傅里叶变换和相关的变换用于重建语音。

此外，傅里叶变换在图像处理中的应用也非常广泛。JPEG压缩算法就是傅里叶变换的一个特例，用于去除图像中的高频成分。

就我个人而言，我已经应用了快速傅里叶变换（或简称FFT）来创建图像副本，这个方法适用于当我们无法访问微型CT扫描仪，但需要一些二进制图像来研究岩石样本的主要特性时。

顺便说一下，最近我写了一篇关于二进制图像的文章：

[](https://pub.towardsai.net/the-brief-history-of-binary-images-c4dacf40ce95?source=post_page-----45b8ec1d445d--------------------------------) [## 二进制图像的简史

### 关于如何使用AI和统计方法处理此类图像的简短Python指南

pub.towardsai.net](https://pub.towardsai.net/the-brief-history-of-binary-images-c4dacf40ce95?source=post_page-----45b8ec1d445d--------------------------------)

接下来，我将考虑一个稍微简单的情况——去除输入图像中的系统性噪声。

这是我们将要处理的原始照片：

![](../Images/3765574ea9555f51a229933d8766632b.png)

输入图像。由作者拍摄。

让我们使用`skimage`包中的`imread`函数来读取图像，然后对其应用傅里叶变换。

Python代码：

```py
import matplotlib.pyplot as plt
import numpy as np
from skimage.io import imread, imshow
from skimage.color import rgb2gray

# read input image
my_im = imread('photo.jpg')
plt.figure('Input Image')
plt.imshow(my_im)
plt.axis('off')  # hide axis
plt.show()

# convert the image to grayscale
gray_im = rgb2gray(my_im)

# applying FFT and center shift
fourier_im = np.fft.fft2(gray_im)
im_shift = np.fft.fftshift(fourier_im)
plt.figure('Applying FFT')
plt.imshow(np.log(abs(im_shift)), cmap='gray')
plt.tight_layout()
plt.show()
```

输出结果：

![](../Images/2ae22111841a50427ee014e8c9656c6a.png)

应用傅里叶变换。图像由作者提供。

在这里，可以注意到图像中有两处扭曲，表现为交叉的线条——它们与照片中的水平（云层）和垂直（路灯）元素直接相关。

那么，如果我们尝试去除与照片中的云层相关的水平“噪音”呢？

我们可以使用一个掩模，该掩模通过初始化一个与图像大小相同的零矩阵来创建，然后在掩模中设置中心的垂直和水平条带为1。接着，通过逐元素相乘将掩模应用于平移后的傅里叶变换图像。过滤后，我们对掩模后的频域数据进行逆傅里叶变换（IFFT），以将其转换回空间域。

```py
# create vertical & horizontal mask for noise removal
rows, cols = gray_im.shape
crow, ccol = rows // 2, cols // 2

# create a mask with ones in the vertical and horizontal strip
# let's say width is equal to 100 pixels
mask = np.zeros((rows, cols), dtype=np.float32)
mask[crow - 50:crow + 50, :] = 1  # vertical strip in the center
mask[:, ccol - 50:ccol + 50] = 1  # horizontal strip in the center

# apply the mask to the shifted FFT
filtered_im_shift = im_shift * mask

# inverse FFT to get the filtered image back
filtered_fourier_im = np.fft.ifftshift(filtered_im_shift)
filtered_image = np.fft.ifft2(filtered_fourier_im)
filtered_image = np.abs(filtered_image)  # Take absolute value

# display the filtered image
plt.figure('Filtered Image')
plt.imshow(filtered_image, cmap='gray')
plt.axis('off')  # hide axis
plt.tight_layout()
plt.show()
```

结果将如下所示：

![](../Images/7995ac29d8d0c1f465793f8fcb54c5d1.png)

经过垂直和水平掩模处理的图像。图像由作者提供。

# 案例 #2：叠加原理

叠加原理是物理学和工程学中的一个基本概念，特别是在波动力学、光学和信号处理领域。它表明，当两个或多个波在空间中重叠时，任意一点的合成波是该点各个波的和。这个原理适用于线性系统，对于理解干涉和衍射等现象至关重要。

> 在 STEM（科学、技术、工程和数学）的背景下，叠加原理可以应用于分析各种类型的波，包括声波、电磁波和量子波函数。它使得工程师和科学家能够预测波如何相互作用，这对于设计通信网络、音频设备和光学设备等系统至关重要。

## 数学表示

对于由以下方程描述的两条正弦波：

```py
y₁(x, t) = A₁ sin(k₁ x - ω₁ t + φ₁)
y₂(x, t) = A₂ sin(k₂ x - ω₂ t + φ₂) 
```

由于这两波的叠加，合成波`y(x, t)`可以表示为：

```py
y(x, t) = y₁(x, t) + y₂(x, t)
```

在上述方程中，`A₁`和`A₂`是波的振幅；`k₁`和`k₂`是波数；`ω₁`和`ω₂`是角频率；`φ₁`和`φ₂`是相位偏移。

## 计算两条正弦波叠加的 Python 脚本

以下是一个 Python 脚本，使用`numpy`和`matplotlib`计算并可视化两条正弦波的叠加。该脚本生成具有指定参数的两条正弦波并绘制它们的叠加。

```py
import numpy as np
import matplotlib.pyplot as plt

# parameters for the first wave
A1 = 1.0      # amplitude
k1 = 2 * np.pi / 5  # wave number (2*pi/wavelength)
omega1 = 2 * np.pi / 10  # angular frequency (2*pi/period)
phi1 = 0      # phase shift

# parameters for the second wave
A2 = 0.5      # amplitude
k2 = 2 * np.pi / 3   # wave number
omega2 = 2 * np.pi / 15  # angular frequency
phi2 = np.pi / 4  # phase shift

# create an array of x values
x = np.linspace(0, 30, 1000)
t = 0  # time at which we calculate the waves

# calculate the individual waves
y1 = A1 * np.sin(k1 * x - omega1 * t + phi1)
y2 = A2 * np.sin(k2 * x - omega2 * t + phi2)

# calculate the superposition of the two waves
y_superposition = y1 + y2

# plotting
plt.figure(figsize=(12, 8))
plt.plot(x, y1, label='Wave 1', linestyle='--')
plt.plot(x, y2, label='Wave 2', linestyle='--')
plt.plot(x, y_superposition, label='Superposition', linewidth=2)
plt.title('Superposition of Two Sinusoidal Waves')
plt.xlabel('Position (x)')
plt.ylabel('Amplitude')
plt.legend()
plt.show()
```

输出为：

![](../Images/8dfde87c23e52b72598bf2be7bb8b871.png)

两条正弦波叠加的结果。图片来自作者。

# 案例 # 3：物料平衡

应用科学方法的最后一个案例有点“理论化”，所以我完全不会在这里插入复杂的公式。

我决定在关于 STEM 的帖子中提到物料平衡，因为任何数据科学家都或多或少知道一个著名的“垃圾进，垃圾出”（或简称 GIGO）公式，意味着低质量的输入将产生错误的输出，我认为这也是数据科学中物料平衡的一种形式 :)

![](../Images/b97278dd03afb284b971be4cc5eeb1e8.png)

GIGO 原理中的物料平衡。图片来自作者。

> 数据科学中的 GIGO 原理指的是输出的质量由输入的质量决定。如果你提供低质量、不准确或无关的数据（垃圾进），那么你的分析、模型或算法的结果也将是有缺陷或具有误导性的（垃圾出）。这强调了数据质量、清洁性和相关性在数据科学项目中的重要性，以及为了确保可靠的结果，数据预处理和验证的必要性。

# 结论

STEM 背景为数据科学提供了坚实的基础，增强了解释复杂数据集所必需的分析能力。首先，支撑统计学和算法的数学原理使数据科学家能够开发出准确预测趋势和行为的模型。其次，科学方法促进了批判性思维和问题解决能力，使从业者能够系统地提出假设、进行实验并验证结果。最后，工程原理对构建可扩展的数据基础设施和优化性能至关重要，确保数据解决方案不仅有效，而且高效。综合来看，这些 STEM 学科使数据科学家能够以结构化的思维方式应对挑战，推动创新并在日益数据驱动的世界中做出明智的决策。

> 我尝试通过个人经验提供三个简单的案例，展示 STEM 教育对于那些想进入数据领域的人有多么重要。但当然，现实中还有更多的例子，而 2024 年诺贝尔物理学奖则是 STEM 对数据科学和机器学习发展重要性的另一个亮眼展示。今年的奖项颁发给了“为人工神经网络的机器学习奠定基础的发现和发明”。

感谢阅读！虽然我建议不要仅仅阅读别人的经验，而是尝试在下一个数据科学项目中实现 STEM 原则，亲自感受其背后的深度 :)
