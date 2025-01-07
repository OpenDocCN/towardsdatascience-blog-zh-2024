# 揭开元动力学的面纱

> 原文：[https://towardsdatascience.com/unveiling-metadynamics-a-beginners-guide-to-mastering-plumed-part-1-of-3-0442e1196abb?source=collection_archive---------4-----------------------#2024-06-22](https://towardsdatascience.com/unveiling-metadynamics-a-beginners-guide-to-mastering-plumed-part-1-of-3-0442e1196abb?source=collection_archive---------4-----------------------#2024-06-22)

## 学习掌握PLUMED的初学者指南（第1部分，共3部分）

[](https://medium.com/@stimpsondon?source=post_page---byline--0442e1196abb--------------------------------)[![Don Robert Stimpson](../Images/271627469edde5c5309ea0289ec0177d.png)](https://medium.com/@stimpsondon?source=post_page---byline--0442e1196abb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0442e1196abb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0442e1196abb--------------------------------) [Don Robert Stimpson](https://medium.com/@stimpsondon?source=post_page---byline--0442e1196abb--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0442e1196abb--------------------------------) ·阅读时间10分钟·2024年6月22日

--

![](../Images/dbe602816330054eafcd9d481d2c12cb.png)

DALL-E生成的封面图像

在计算化学和分子动力学（MD）中，理解复杂系统有时需要超越MD引擎或VMD可视化所提供的分析。我个人从事生物分子原子模拟工作，这些分子可真是大得惊人。考虑到计算每个原子在这些大模拟盒子中的轨迹的复杂性，通常我只能看到不超过1或2微秒的轨迹，这是许多MD运行的常见上限。这意味着，虽然传统的MD方法对于观察发生在此时间范围内的过程的轨迹波动非常有效，但对于那些需要更长时间的过程，怎么办呢？

有一种强大的技术可以用来研究这些过程，叫做元动力学，而PLUMED作为一个在该领域中的领先工具，因其与GROMACS引擎的无缝集成而脱颖而出。在这一系列文章中，我们将逐步建立对元动力学的理解，涵盖理论、代码和语法，最终目标是能够为你想深入观察的任何现象生成复杂的元动力学模拟！本文将特别介绍元动力学的概念以及一些关于如何正确安装PLUMED和快速运行的一般代码。

本文假设读者已经熟悉某些分子动力学（MD）引擎，并能够为该引擎生成系统。如果你只是想了解一个有趣的技术，这并不必要，但我建议如果你打算实现元动力学，最好对某个MD引擎（我偏好GROMACS）有所了解。

## 什么是元动力学？

元动力学是一种先进的采样方法，旨在探索分子系统的自由能景观。它通过鼓励那些系统原本不容易发生的事件，帮助研究稀有事件和缓慢过程。

它通过添加一个历史依赖的偏置势来实现这一点，从而允许系统离开CV空间中的局部最小值，克服能量障碍，探索更多样的配置，具体取决于你要做的事情。

![](../Images/baaf400885c26ecc903982b077b11f00.png)

我做的一个旧的元动力学运行的3D渲染图，展示了一个膜蛋白两个亚基相对位置的变化。等高线和着色表示相对自由能，3D视图则展示了在物理景观中的表现。（图像由作者生成）

## 理解元动力学中的高斯分布

所以，元动力学成功的关键在于相对自由能中目标高斯分布的实现。为了理解这个过程，我们来看看经典物理学中的球体滚下山坡的比喻。我们都知道球体会滚下来，但如果这座山坡不仅仅是一个双曲形的隆起，而是一座山脉呢？那么球体仍然会滚下来，但它很可能会被困在某个裂缝中，或者停在一个较平坦的地方，而不是滚到山脉中的全局最低点。

![](../Images/b723674067a74491103ad65e7ffec45f.png)

看它怎么动起来！(DALL-E生成)

元动力学通过依次在球体下添加高斯核，就像在球下堆小土堆一样，来修改这一情景。当你在球上添加足够的土堆时，球就会处于一个可以再次滚动的位置。我们基本上会一直这样做，直到差不多填满所有的山谷，每添加一个土堆都会记录下来。一旦我们添加足够的土堆，将山脉替换成平坦的表面后，就可以通过统计我们添加的土堆的数量及每个土堆的大小来确定每个区域的深度，从而找出山脉中最低能量的位置。

现在，这可能不是用一个真实的球在真实的山脉中做事的最有效方法；它既不具成本效益，也不环保，而且过程中你还破坏了山脉。但如果我们将这个思路应用到一个系统的自由能表面，它就变成了一个非常理想的选择。如果我们用我们感兴趣的集合变量（CVs）代替球的位置，并在其中执行相同的基本任务，逐步添加高斯分布，我们就可以形成一个自由能表面（FES），它告诉我们不同构象的相对吉布斯自由能，就像下面的图所示：

![](../Images/c7a793f84ff7fa505905bbc09eef3584.png)

这里的顶部图是自由能表面（FES），它显示了相对吉布斯自由能如何作为位置的二维函数变化。第二个图描绘了“球体”在这些维度中的路径。（图像由作者生成）

所以我在上面包括了两个关于理论元动力学模拟的图形分析。第一个图是我们刚才讨论的，使用2个CV构建系统在某一状态下相对自由能的拓扑图。我在这里通过将两个维度设为空间维度来简化问题，所以我们看的是分子相对于点(0, 0)的位置以及与之相关的能量。第二个图通过展示分子在坐标空间中随时间移动的轨迹来简化这一点，并用颜色表示它在何时处于何种区域。基本上，这是球体滚动的路径，如果某个地方有一大堆点，那么很可能它对应着FES图中的一个最小值。现在我需要快速说明的是，这是一种多行走者（MW）模拟，因为我从我的档案中迅速抓取了一个；这意味着我有三个球在滚动，并且为所有三个球添加了泥土，这些泥土在所有三个球上都保持存在。我们将在未来的文章中讨论为什么这么做，但如果你仔细观察那张图，这就是为什么会有三条独立轨迹的原因。

请记住，虽然我使用了空间维度来更有效地解释类比，但此图的CV轴几乎可以是任何你可以通过算法向PLUMED解释的内容，包括扭转、角度、向量等。将其可视化为地形图只是理解CV如何工作和/或如何协同工作的一种简便方式。

好了，添加这些泥土到底是怎么回事？什么是高斯分布？

高斯分布（所添加的核），也称为正态分布，是一种钟形曲线，其特点是均值（μ）和标准差（σ）。

![](../Images/7527ef36287cd99494309e7cef4f8e83.png)

我用Python快速制作的高斯分布图，展示了均值和标准差。（图像由作者生成）

高斯分布的数学表示由以下方程给出：

![](../Images/321edf81f4fb7b88b416e43460b0775a.png)

高斯分布方程（图像由作者生成）

在元动力学的背景下，高斯丘通常按规律间隔添加到自由能表面，以防止系统重新访问之前已探索的状态。这些丘由以当前系统在集体变量（CV）空间中的位置为中心的高斯函数描述。这些高斯函数的高度和宽度决定了偏置的影响。

在元动力学中，高斯丘的方程是：

![](../Images/1812db0da59a31cf17a26bee5b7194b7.png)

偏置势能方程（图像由作者生成）

其中，s(τ) 是集体变量空间中在时间点 τ 时的位置，W 是高斯偏置的高度，δ 是偏置的宽度，求和范围涵盖了所有添加偏置的时间点 τ。基本上这就是一种复杂的说法：“我们在这些地方添加了这么多‘土’”。我将在本系列文章的第二部分深入探讨元动力学背后的数学原理，所以现在你不必强求理解这些方程，只要知道“球在丘顶”已经足够进行并分析我们的第一次元动力学模拟了。

## 开始使用 PLUMED

**安装**：

不得不说，这有点烦人，它需要被打补丁到你的 MD 引擎中。如果你不打算使用 GROMACS 作为你的 MD 引擎，以下是 PLUMED 官方主页的链接，因为关于安装的部分你需要自己处理：

[](https://www.plumed.org/doc-v2.9/user-doc/html/_installation.html?source=post_page-----0442e1196abb--------------------------------) [## PLUMED：安装

### 在此页面上，你可以学习如何配置、编译和安装 PLUMED。对于那些不耐烦的人，…

www.plumed.org](https://www.plumed.org/doc-v2.9/user-doc/html/_installation.html?source=post_page-----0442e1196abb--------------------------------)

否则，以下是如何同时安装这两个工具并正确地打补丁。如果你没有安装任何一个，请按照以下命令进行操作；如果你已经安装并且能够正常使用 GROMACS，则可以忽略 GROMACS 的安装部分。这些命令需要逐个在你的终端/命令行中执行。

```py
#Download GROMACS
wget http://ftp.gromacs.org/pub/gromacs/gromacs-2021.2.tar.gz
tar xfz gromacs-2021.2.tar.gz
cd gromacs-2021.2

#Install and source GROMACS
mkdir build
cd build
cmake .. -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON
make
sudo make install
source /usr/local/gromacs/bin/GMXRC

#Download PLUMED
wget https://github.com/plumed/plumed2/releases/download/v2.7.1/plumed-2.7.1.tgz
tar xfz plumed-2.7.1.tgz
cd plumed-2.7.1

#install PLUMED
./configure --prefix=/usr/local/plumed
make
sudo make install

#Patch GROMACS
cd gromacs-2021.2
plumed patch -p

#rebuilld GROMACS
cd build
cmake .. -DGMX_BUILD_OWN_FFTW=ON -DREGRESSIONTEST_DOWNLOAD=ON -DGMX_PLUMED=on
make
sudo make install

#Check installation
gmx mdrun -plumed 
```

你会注意到我选择了一个较旧版本的 GROMACS；这是为了让我们有更大的机会避免在这些文章中出现意外的bug，你完全可以根据自己的需求使用更新版本，只要确保它是与 PLUMED 兼容的。

**基本配置**：

+   创建一个 PLUMED 输入文件，以定义描述系统重要自由度的集体变量（CVs）。

这是一个示例文件。我将在本系列文章的第三部分更详细地讨论一些更高级的选项，但现在我们先从使用距离和扭转作为集体变量（CVs）来看一组原子的构象状态。其他可能的集体变量包括原子间的距离、角度、二面角或更复杂的函数。

```py
# Define collective variables
# Distance between atoms 1 and 10
DISTANCE ATOMS=1,10 LABEL=d1

# Dihedral angle involving atoms 4, 6, 8, and 10
TORSION ATOMS=4,6,8,10 LABEL=t1

# Print collective variables to a file
PRINT ARG=d1,t1 FILE=COLVAR STRIDE=100

# Apply metadynamics bias
METAD ...
  ARG=d1,t1         # The collective variables to bias
  PACE=500          # Add a Gaussian hill every 500 steps
  HEIGHT=0.3        # Height of the Gaussian hill
  SIGMA=0.1,0.1     # Width of the Gaussian hill for each CV
  FILE=HILLS        # File to store the hills
  BIASFACTOR=10     # Bias factor for well-tempered metadynamics
  TEMP=300          # Temperature in Kelvin
... METAD

# Print the bias potential to a file
PRINT ARG=d1,t1,bias FILE=BIAS STRIDE=500
```

该代码块中的注释应该足够详细，以便基本理解正在发生的所有内容，但我将在第三篇文章中介绍这一切，甚至会深入探讨更复杂的功能！

无论如何，一旦你拥有这个输入文件（通常命名为plumed.dat）和用于GROMACS MD运行所需的.tpr文件（请参阅gmx grompp文档了解如何生成该文件），你可以通过进入工作目录并在命令行中输入以下命令来运行元动力学模拟：

```py
gmx mdrun -s topol.tpr -plumed plumed.dat
```

PLUMED和GROMACS都接受额外的参数。我将在这系列文章的第三部分介绍一些更有用的参数，并展示我为更高级的运行编写的一些脚本，你可以查看文档以了解其他参数。

在模拟之后，使用PLUMED的分析工具重建自由能表面，并识别相关的亚稳态和过渡路径。最常用的是使用PLUMED的`sum_hills`工具来重建自由能表面。

你可以在执行完该命令后，使用以下Python代码查看自由能表面（FES），该代码将告诉你一个CV的值如何与另一个CV相关。

```py
import matplotlib.pyplot as plt
import numpy as np
import plumed
from matplotlib import cm, ticker

# Configure font
plt.rc('font', weight='normal', size=14)

# Read data from PLUMED output
data = plumed.read_as_pandas("/path/to/COLVAR")

# Extract and reshape data for contour plot
# Adjust the reshape parameters as needed, They should multiply to the
# number of bins and be as close to each other as possible
d1 = data["d1"].values.reshape(-1, 100)
t1 = data["t1"].values.reshape(-1, 100)  
bias = data["bias"].values.reshape(-1, 100)

# Plot contour lines
plt.contour(d1, t1, bias, levels=np.arange(np.min(bias), np.max(bias), 10), linewidths=0.3, colors='k')

# Plot filled contour
cntr = plt.contourf(d1, t1, bias, levels=np.arange(0, 100), cmap=cm.jet)

# Add colorbar
plt.colorbar(cntr, label="\u0394G [kJ/mol]")

# Set plot limits and labels
plt.xlim(np.min(d1), np.max(d1))
plt.ylim(np.min(t1), np.max(t1))
plt.xlabel("Distance between atoms 1 and 10 (d1) [nm]")
plt.ylabel("Dihedral angle involving atoms 4, 6, 8, and 10 (t1) [degrees]")

# Show plot
plt.show()
```

输出应该类似于我之前发布的拓扑图（我无法给你展示你的FES图，因为你有自由选择自己的系统）。

你还应该使用流行的可视化软件，如VMD，来可视化结果，从而深入了解低能量状态和亚稳态中的分子行为。

## 结论

由PLUMED驱动的元动力学提供了一个强大的框架，用于探索复杂的分子系统。通过高效地采样自由能景观，我们可以揭示分子系统中传统MD由于计算限制无法揭示的隐藏机制。

无论你是初学者还是经验丰富的研究人员，掌握PLUMED都能显著提升你的计算化学工具包，因此不要忘记查看我接下来的两篇文章，帮助你从新手变成专家！

第二篇文章将揭示将元动力学组件添加到MD引擎背后的数学概念，而第三篇文章将向你介绍元动力学中的高级技术，如多行者元动力学、将超过2个变量压缩成可读格式、在高性能集群上使用元动力学，以及更深入的分析技术，用于可视化和定量分析你的系统结果（并附有大量示例代码）。
