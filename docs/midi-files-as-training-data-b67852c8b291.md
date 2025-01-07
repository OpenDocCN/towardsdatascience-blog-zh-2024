# MIDI文件作为训练数据

> 原文：[https://towardsdatascience.com/midi-files-as-training-data-b67852c8b291?source=collection_archive---------3-----------------------#2024-09-13](https://towardsdatascience.com/midi-files-as-training-data-b67852c8b291?source=collection_archive---------3-----------------------#2024-09-13)

## 一个根本的区别：MIDI乐谱与MIDI表演

[](https://medium.com/@foscarin.francesco?source=post_page---byline--b67852c8b291--------------------------------)[![Francesco Foscarin](../Images/f4d238b54771adc6d03c9a62f28d3152.png)](https://medium.com/@foscarin.francesco?source=post_page---byline--b67852c8b291--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b67852c8b291--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b67852c8b291--------------------------------) [Francesco Foscarin](https://medium.com/@foscarin.francesco?source=post_page---byline--b67852c8b291--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b67852c8b291--------------------------------) ·10分钟阅读·2024年9月13日

--

![](../Images/8cc863eae2ddaf0ad98de4390333a286.png)

在开始任何MIDI文件的深度学习项目之前，确保你知道**MIDI乐谱与MIDI表演之间的区别**！

本文适用于计划或刚开始使用MIDI文件的人。这个格式在音乐界被广泛使用，并且由于数据集的可用性，引起了计算机音乐研究人员的关注。

然而，不同类型的信息可以编码在MIDI文件中。特别是，MIDI乐谱和MIDI表演之间有很大的区别。没有意识到这一点会导致**浪费时间在无用的任务上**或选择错误的**训练数据和方法**。

我将提供这两种格式的基本介绍，并给出如何在Python中开始使用它们的实用示例。

## *什么是MIDI？*

MIDI最初作为合成器之间的实时通信协议引入。其主要思想是每当在MIDI键盘上**按下音符**（note on）时发送一条消息，而在**释放音符**（note off）时发送另一条消息。然后接收端的合成器将知道生成何种声音。

## *欢迎来到MIDI文件！*

如果我们收集并保存所有这些消息（确保添加它们的时间位置），那么我们就得到了一个MIDI文件，可以用来重现一段音乐。除了note-on和note-off，还有许多其他类型的消息，例如指定踏板信息或其他控制器。

你可以考虑用*pianoroll*来绘制这些信息。

请注意，这不是MIDI文件，而只是其内容的一种可能表示！一些软件（例如[Reaper](https://www.reaper.fm/)）在钢琴滚动视图旁边添加了一个小型钢琴键盘，以便更容易地进行视觉解读。

![](../Images/8017dddfc17763d1f6f5b970e6dcf361.png)

## *MIDI文件是如何创建的？*

MIDI文件可以主要通过**两种方式**创建：1）通过MIDI乐器演奏，2）通过手动在音序器（如Reaper、Cubase、GarageBand、Logic）或乐谱编辑器（例如MuseScore）中编写。

每种生成MIDI文件的方式也会产生不同类型的文件：

1.  在MIDI乐器上演奏→ **MIDI表演**

1.  手动编写音符（音序器或乐谱）→ **MIDI乐谱**

现在我们将深入探讨每种类型，然后总结它们之间的区别。

在开始之前，免责声明：**我不会专门关注信息如何编码，而是关注可以从文件中提取哪些信息**。例如，当我说“时间以秒为单位表示”时，这意味着我们可以获得秒数，尽管编码本身更加复杂。

# MIDI表演

我们可以在MIDI表演中找到四种信息：

+   当音符开始时：音符起始

+   当音符结束时：音符偏移（或音符持续时间计算为偏移 - 开始）

+   演奏了哪个音符：音符音高

+   键按下的“强度”如何：音符速度

**音符起始和结束（以及持续时间）**以秒为单位表示，对应于演奏者按下和释放音符的秒数。

**音符音高**通过一个整数编码，范围从0（最低）到127（最高）；请注意，比钢琴能演奏的音符范围更广；钢琴的音域对应21–108\。

**音符速度**也通过一个整数进行编码，范围从0（静音）到127（最大强度）。

绝大多数MIDI表演都是钢琴表演，因为大多数MIDI乐器是MIDI键盘。其他MIDI乐器（例如MIDI萨克斯风、MIDI鼓和MIDI吉他传感器）也存在，但并不那么常见。

最大的人类MIDI表演数据集（古典钢琴音乐）是由Google Magenta提供的[Maestro数据集](https://magenta.tensorflow.org/datasets/maestro)。

## MIDI表演的主要特性

MIDI表演的一个基本特性是**永远不会有完全相同起始或持续时间的音符**（理论上这是可能的，但在实践中极不可能）。

事实上，即使他们真的努力，演奏者也无法准确地同时按下两个（或更多）音符，因为人类的精确度是有限的。音符持续时间也同样如此。此外，这对大多数音乐家来说并不是优先考虑的，因为时间偏差有助于产生更具表现力或更具律动感的感觉。最后，连续的音符之间会有一些静默，或者部分重叠。

![](../Images/8683281fa2d944677d7994e066df482c.png)

因此，MIDI 演奏有时也被称为 *未量化 MIDI*。时间位置分布在一个连续的时间尺度上，而不是量化到离散的位置（由于数字编码的原因，技术上它是离散的尺度，但非常细腻，因此我们可以认为它是连续的）。

## 实践示例

让我们来看一个 MIDI 演奏。我们将使用 [ASAP 数据集](https://github.com/fosfrancesco/asap-dataset)，它托管在 GitHub 上。

在你喜欢的终端（我在 Windows 上使用 PowerShell）中，去到一个方便的位置并克隆仓库。

```py
git clone https://github.com/fosfrancesco/asap-dataset
```

我们还将使用 Python 库 [Partitura](https://github.com/CPJKU/partitura) 来打开 MIDI 文件，因此你可以在你的 Python 环境中安装它。

```py
pip install partitura
```

现在一切已设置好，让我们打开 MIDI 文件，并打印前 10 个音符。由于这是一个 MIDI 演奏，我们将使用 `load_midi_performance` 函数。

```py
from pathlib import Path
import partitura as pt

# set the path to the asap dataset (change it to your local path!)
asap_basepath = Path('../asap-dataset/')
# select a performance, here we use Bach Prelude BWV 848 in C#
performance_path = Path("Bach/Prelude/bwv_848/Denisova06M.mid")
print("Loading midi file: ", asap_basepath/performance_path)
# load the performance
performance = pt.load_performance_midi(asap_basepath/performance_path)
# extract the note array
note_array = performance.note_array()
# print the dtype of the note array (helpful to know how to interpret it)
print("Numpy dtype:")
print(note_array.dtype)
# print the first 10 notes in the note array
print("First 10 notes:")
print(performance.note_array()[:10])
```

这个 Python 程序的输出应该是这样的：

```py
Numpy dtype:
[('onset_sec', '<f4'), ('duration_sec', '<f4'), ('onset_tick', '<i4'), ('duration_tick', '<i4'), ('pitch', '<i4'), ('velocity', '<i4'), ('track', '<i4'), ('channel', '<i4'), ('id', '<U256')]
First 10 notes:
[(1.0286459, 0.21354167,  790, 164, 49, 53, 0, 0, 'n0')
 (1.03125  , 0.09765625,  792,  75, 77, 69, 0, 0, 'n1')
 (1.1302084, 0.046875  ,  868,  36, 73, 64, 0, 0, 'n2')
 (1.21875  , 0.07942709,  936,  61, 68, 66, 0, 0, 'n3')
 (1.3541666, 0.04166667, 1040,  32, 73, 34, 0, 0, 'n4')
 (1.4361979, 0.0390625 , 1103,  30, 61, 62, 0, 0, 'n5')
 (1.4361979, 0.04296875, 1103,  33, 77, 48, 0, 0, 'n6')
 (1.5143229, 0.07421875, 1163,  57, 73, 69, 0, 0, 'n7')
 (1.6380209, 0.06380209, 1258,  49, 78, 75, 0, 0, 'n8')
 (1.6393229, 0.21484375, 1259, 165, 51, 54, 0, 0, 'n9')]
```

你可以看到，我们有音符的起始时间和时长（秒）、音高和力度。其他字段对于 MIDI 演奏来说不那么重要。

起始时间和时长也以 *ticks*（时隙）表示。这更接近 MIDI 文件中实际编码这种信息的方式：选择一个非常短的时间持续单位（= 1 tick），然后所有时间信息都作为该单位的倍数进行编码。当你处理音乐演奏时，通常可以忽略这些信息，直接使用秒数信息。

你可以验证，永远不会有两个音符的起始时间或时长完全相同！

# MIDI 乐谱

MIDI 乐谱使用 **更丰富的 MIDI 消息集** 来编码信息，如时间签名、调性签名、小节和拍号位置。

因此，**它们类似于音乐乐谱**（乐谱纸），尽管 **它们仍然缺少一些重要信息**，例如音高拼写、连音符、附点、休止符、连线等……

时间信息不是以秒为单位编码的，而是以更具音乐抽象性的单位编码，如四分音符。

## MIDI 乐谱的主要特性

MIDI 乐谱的一个基本特征是，**所有音符的起始时间都对齐到一个量化网格**，该网格首先由小节位置定义，然后通过递归的整数分割（主要是 2 和 3，但也有其他分割，如 5、7、11 等）来定义小节内的附点音符。

## 实践示例

我们现在将查看巴赫前奏曲 BWV 848 C# 大调的乐谱，这是我们之前加载的演奏的乐谱。Partitura 有一个专门的 `load_score_midi` 函数。

```py
from pathlib import Path
import partitura as pt

# set the path to the asap dataset (change it to your local path!)
asap_basepath = Path('../asap-dataset/')
# select a score, here we use Bach Prelude BWV 848 in C#
score_path = Path("Bach/Prelude/bwv_848/midi_score.mid")
print("Loading midi file: ", asap_basepath/score_path)
# load the score
score = pt.load_score_midi(asap_basepath/score_path)
# extract the note array
note_array = score.note_array()
# print the dtype of the note array (helpful to know how to interpret it)
print("Numpy dtype:")
print(note_array.dtype)
# print the first 10 notes in the note array
print("First 10 notes:")
print(score.note_array()[:10])
```

这个 Python 程序的输出应该是这样的：

```py
Numpy dtype:
[('onset_beat', '<f4'), ('duration_beat', '<f4'), ('onset_quarter', '<f4'), ('duration_quarter', '<f4'), ('onset_div', '<i4'), ('duration_div', '<i4'), ('pitch', '<i4'), ('voice', '<i4'), ('id', '<U256'), ('divs_pq', '<i4')]
First 10 notes:
[(0\. , 1.9958333 , 0\.  , 0.99791664,   0, 479, 49, 1, 'P01_n425', 480)
 (0\. , 0.49583334, 0\.  , 0.24791667,   0, 119, 77, 1, 'P00_n0', 480)
 (0.5, 0.49583334, 0.25, 0.24791667, 120, 119, 73, 1, 'P00_n1', 480)
 (1\. , 0.49583334, 0.5 , 0.24791667, 240, 119, 68, 1, 'P00_n2', 480)
 (1.5, 0.49583334, 0.75, 0.24791667, 360, 119, 73, 1, 'P00_n3', 480)
 (2\. , 0.99583334, 1\.  , 0.49791667, 480, 239, 61, 1, 'P01_n426', 480)
 (2\. , 0.49583334, 1\.  , 0.24791667, 480, 119, 77, 1, 'P00_n4', 480)
 (2.5, 0.49583334, 1.25, 0.24791667, 600, 119, 73, 1, 'P00_n5', 480)
 (3\. , 1.9958333 , 1.5 , 0.99791664, 720, 479, 51, 1, 'P01_n427', 480)
 (3\. , 0.49583334, 1.5 , 0.24791667, 720, 119, 78, 1, 'P00_n6', 480)]
```

你可以看到音符的起始时间完全落在一个网格上。如果我们考虑 `onset_quarter`（第三列），我们可以看到 16 分音符每 0.25 四分之一音符出现一次，正如预期的那样。

![](../Images/ca2dffd6524f8a72dd58c8dc6c7d411b.png)

时值稍微有些问题。例如，在这段乐谱中，一个十六分音符的`quarter_duration`应该是0.25。但从Python输出中我们可以看到，时值实际上是0.24791667。发生了什么？使用来生成这个MIDI文件的MuseScore将每个音符的时值稍微缩短了一些。为什么？仅仅是为了让这个MIDI文件的音频演绎听起来更好。确实如此，代价是给那些用这些文件做计算机音乐研究的人带来了许多问题。类似的问题也存在于广泛使用的数据集中，比如Lakh MIDI数据集。

# MIDI乐谱与MIDI表演

鉴于我们所看到的MIDI乐谱和MIDI表演之间的差异，下面我将给出一些通用的指导方针，以帮助正确设置您的深度学习系统。

对于音乐生成系统，更倾向使用MIDI乐谱，因为量化的音符位置可以用相当小的词汇表示，且其他简化也可能实现，比如只考虑单音旋律。

对于那些旨在模拟人类演奏和感知音乐的系统，使用MIDI表演数据，例如节拍跟踪系统、节奏估计系统和情感识别系统（专注于表现性演奏）。

使用这两种数据类型来完成诸如音符跟踪（输入：表演，输出：乐谱）和表现性表演生成（输入：乐谱，输出：表演）等任务。

## 额外的问题

我已经介绍了MIDI乐谱与MIDI表演之间的主要差异。然而，正如常常发生的那样，**事情可能会更复杂**。

例如，某些数据集（如AMAPS数据集）最初是MIDI乐谱，但作者在每个音符上引入了时间变化，**以模拟真实人类演奏者的时间偏差**（注意，这仅发生在不同时间位置的音符之间；和弦中的所有音符仍然是完全同时发生的）。

此外，某些MIDI导出文件，如来自MuseScore的文件，也会通过更改节奏标记（如果作品改变了节奏）或在连续音符之间插入非常短的小停顿（我们在前面的例子中看到过）来尝试让MIDI乐谱更接近MIDI表演，并通过在参考音符起始之前稍微演奏一个非常短的音符来表现装饰音。

事实上，装饰音在MIDI乐谱中是一个非常令人头痛的问题。它们的时值在音乐术语中没有明确规定，我们只知道它们应该是“短”的。而它们的起始点与参考音符的起始点相同，但如果我们听MIDI文件的音频演绎时，会觉得非常奇怪。那么我们是应该缩短前一个音符，还是下一个音符，为装饰音腾出空间呢？

其他装饰音也存在问题，因为关于如何演奏它们没有统一的规则。例如，颤音应该包含多少个音符？应该从实际音符还是上行音符开始演奏一个刺音？

# 结论

MIDI 文件非常棒，因为它们明确提供了每个音符的音高、起始时间和持续时间等信息。这意味着，例如，与音频文件相比，针对 MIDI 数据的模型可以更小，并且可以使用更小的数据集进行训练。

这也带来了一些代价：MIDI 文件，以及一般意义上符号编码的音乐格式，是非常复杂的，因为它们以多种不同的方式编码了许多种类的信息。

为了正确使用 MIDI 数据作为训练数据，了解编码的数据类型非常重要。希望这篇文章能为你提供一个很好的起点，让你进一步了解这个话题！

[所有图表均来自作者。]
