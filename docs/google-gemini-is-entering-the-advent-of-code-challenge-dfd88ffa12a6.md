# 谷歌 Gemini 正在加入 Advent of Code 挑战

> 原文：[https://towardsdatascience.com/google-gemini-is-entering-the-advent-of-code-challenge-dfd88ffa12a6?source=collection_archive---------9-----------------------#2024-12-02](https://towardsdatascience.com/google-gemini-is-entering-the-advent-of-code-challenge-dfd88ffa12a6?source=collection_archive---------9-----------------------#2024-12-02)

## 一个开源项目，用于探索 LLM 在编程挑战中的能力与局限

[](https://heiko-hotz.medium.com/?source=post_page---byline--dfd88ffa12a6--------------------------------)[![Heiko Hotz](../Images/d08394d46d41d5cd9e76557a463be95e.png)](https://heiko-hotz.medium.com/?source=post_page---byline--dfd88ffa12a6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dfd88ffa12a6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dfd88ffa12a6--------------------------------) [Heiko Hotz](https://heiko-hotz.medium.com/?source=post_page---byline--dfd88ffa12a6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dfd88ffa12a6--------------------------------) ·阅读时长8分钟·2024年12月2日

--

![](../Images/7c62dc78a56ed7265c284edb641d6503.png)

图片由作者提供（使用 Flux 1.1 Pro 创建）

# 这是什么？

如果2024年在生成性人工智能领域教给我们什么，那就是编程是大规模语言模型（LLMs）最具前景的应用之一。

在这篇博客中，我将描述如何使用目前领先于 LMArena 排行榜的最先进的 LLM——Gemini Experimental 1121，来应对 [Advent of Code](https://adventofcode.com/) 挑战。

![](../Images/037eb3b5d51254c5340d3640600163e8.png)

图片由作者提供

我将概述我的方法，并分享我的 [开源代码库](https://github.com/heiko-hotz/advent-of-code-2024-gemini)，以便读者进一步探索并复现结果。

# 为什么我们应该关注？

LLMs + 编程是一个令人兴奋的领域，有很多原因，其中几个亮点如下：

+   代码就像语言一样，可以通过变换器模型以相同的方式学习

+   输出容易验证——我们只需运行代码并检查其结果

+   对代码助手的需求巨大——它们能够极大提高程序员的生产力

所以，这无疑是一个有趣且令人兴奋的方向，我认为通过动手挑战来进一步探索它可能会很有趣。

# Advent of Code 挑战

对于那些不熟悉 Advent of Code 挑战的人来说：这是一个每年举办的活动，从12月1日到12月25日，提供每天的编程难题，类似于圣诞倒计时日历。每天都会发布一个包含两部分的新难题，程序员可以在其中测试他们的编程和问题解决能力。这是一个让各个级别的开发者都可以练习编程的有趣方式。

每日挑战的两个部分围绕一个类似的问题展开，并使用相同的输入数据。其目的是编写一个 Python 程序来处理输入数据并生成解决方案（通常是一个数字）。一旦我们运行代码，模型计算出解决方案，我们可以将其粘贴到网站上，查看它是否正确。如果正确，第二部分将解锁，采用类似的程序。

竞赛持续 25 天，用户最多可以收集 50 星星（每天 2 星）。

# 一个很好的 LLM 挑战

如上所述，这是一个非常适合 LLM 的挑战。我们只需拿到问题陈述，插入选择的 LLM，让它生成代码，运行代码，再将生成的解决方案粘贴到网站上，查看 LLM 是否成功。

对于这个项目，我使用的是 Gemini Experimental 1121，它大大提高了编程和推理能力。它可以通过 Google 的[AI Studio](https://aistudio.google.com/)获得。我在整个挑战中使用相同的系统提示——它是一个零-shot 提示（没有链式思维），并且要求代码通过输入重定向接收输入，像这样：

```py
python day01/part1.py < day01/input.txt
```

系统提示是：

```py
Provide python code to solve a given puzzle.
Assume there is an input.txt file that can be read
via input redirection in the command line.
```

然后我会发布实际的挑战，Gemini 将生成应当产生正确解决方案的代码。我将代码复制到 GH 仓库并运行，然后将生成的解决方案粘贴到 Advent of Code 网站上查看它是否正确。

# 仓库

每天的挑战都组织在自己的目录中：

```py
dayXX/
├── input.txt         # Challenge input
├── part1-problem.txt # Problem description for part 1
├── part2-problem.txt # Problem description for part 2
├── part1.py         # Solution for part 1
└── part2.py         # Solution for part 2
```

part1 和 part2 的问题文本文件包含 Advent of Code 提出的挑战问题。我还在每个文本文件的末尾附上了正确的解决方案：

![](../Images/541b539dd8e0b40f65902baac658a0d4.png)

图片来自作者

Python 脚本包含由 Gemini 生成的代码。为了保持完全透明，我还链接到实际的对话记录，让大家可以查看并审核每一步：

![](../Images/c723907aad4658ab10216a6eb423cbea.png)

图片来自作者

要查看这些聊天中的一个示例，请访问[我与 Gemini 关于第一天挑战的对话](https://aistudio.google.com/app/prompts?state=%7B%22ids%22%3A%5B%221kkRVShxln7z6qfKgsVEtP20hozJj7YkA%22%5D%2C%22action%22%3A%22open%22%2C%22userId%22%3A%22105677632504908789218%22%2C%22resourceKeys%22%3A%7B%7D%7D&usp=sharing)

我将把所有的结果记录在一个表格中，给读者一个关于模型表现的良好初步概览：

![](../Images/72852ef645a49d51b9ae4148348992dc.png)

图片来自作者

# 示例

为了更好地了解这将是什么样子，让我们来看一下第一天挑战的第一部分。以下是问题陈述：

```py
The Chief Historian is always present for the big Christmas sleigh launch, but nobody has seen him in months! Last anyone heard, he was visiting locations that are historically significant to the North Pole; a group of Senior Historians has asked you to accompany them as they check the places they think he was most likely to visit.

As each location is checked, they will mark it on their list with a star. They figure the Chief Historian must be in one of the first fifty places they'll look, so in order to save Christmas, you need to help them get fifty stars on their list before Santa takes off on December 25th.

Collect stars by solving puzzles. Two puzzles will be made available on each day in the Advent calendar; the second puzzle is unlocked when you complete the first. Each puzzle grants one star. Good luck!

You haven't even left yet and the group of Elvish Senior Historians has already hit a problem: their list of locations to check is currently empty. Eventually, someone decides that the best place to check first would be the Chief Historian's office.

Upon pouring into the office, everyone confirms that the Chief Historian is indeed nowhere to be found. Instead, the Elves discover an assortment of notes and lists of historically significant locations! This seems to be the planning the Chief Historian was doing before he left. Perhaps these notes can be used to determine which locations to search?

Throughout the Chief's office, the historically significant locations are listed not by name but by a unique number called the location ID. To make sure they don't miss anything, The Historians split into two groups, each searching the office and trying to create their own complete list of location IDs.

There's just one problem: by holding the two lists up side by side (your puzzle input), it quickly becomes clear that the lists aren't very similar. Maybe you can help The Historians reconcile their lists?

For example:

3   4
4   3
2   5
1   3
3   9
3   3
Maybe the lists are only off by a small amount! To find out, pair up the numbers and measure how far apart they are. Pair up the smallest number in the left list with the smallest number in the right list, then the second-smallest left number with the second-smallest right number, and so on.

Within each pair, figure out how far apart the two numbers are; you'll need to add up all of those distances. For example, if you pair up a 3 from the left list with a 7 from the right list, the distance apart is 4; if you pair up a 9 with a 3, the distance apart is 6.

In the example list above, the pairs and distances would be as follows:

The smallest number in the left list is 1, and the smallest number in the right list is 3\. The distance between them is 2.
The second-smallest number in the left list is 2, and the second-smallest number in the right list is another 3\. The distance between them is 1.
The third-smallest number in both lists is 3, so the distance between them is 0.
The next numbers to pair up are 3 and 4, a distance of 1.
The fifth-smallest numbers in each list are 3 and 5, a distance of 2.
Finally, the largest number in the left list is 4, while the largest number in the right list is 9; these are a distance 5 apart.
To find the total distance between the left list and the right list, add up the distances between all of the pairs you found. In the example above, this is 2 + 1 + 0 + 1 + 2 + 5, a total distance of 11!

Your actual left and right lists contain many location IDs. What is the total distance between your lists?

### Solution: 1666427
```

当将这个挑战交给 Gemini 时，它会生成以下代码：

```py
import sys

def solve():
    left_list = []
    right_list = []

    for line in sys.stdin:
        left, right = map(int, line.strip().split())
        left_list.append(left)
        right_list.append(right)

    left_list.sort()
    right_list.sort()

    total_distance = 0
    for i in range(len(left_list)):
        total_distance += abs(left_list[i] - right_list[i])

    print(total_distance)

solve()
```

使用以下代码运行

```py
python day01/part1.py < day01/input.txt
```

得到的结果是：

![](../Images/ac065d6519cd72ddcb7ceb3031354abd.png)

图片来自作者

如我们所见，Gemini 的代码生成了正确的结果。

## 奖励：获取解释

作为额外奖励，我们还可以请Gemini提供代码的解释，说明它是做什么的，以及为什么能解决这个挑战：

![](../Images/d62b65f3db68b2f74aab2b7b37d41ed3.png)

图片由作者提供

你可以在[这里](https://aistudio.google.com/app/prompts?state=%7B%22ids%22%3A%5B%221E14ypphhy7nDgWvxRy_QVv5AM4YVLaVM%22%5D%2C%22action%22%3A%22open%22%2C%22userId%22%3A%22105677632504908789218%22%2C%22resourceKeys%22%3A%7B%7D%7D&usp=sharing)找到带有解释的聊天记录。

# 结论

通过这个项目，我想探索当前最先进的LLM（大语言模型）在解决编程挑战方面的能力。我有一个假设，认为Gemini（以及其他LLM）已经足够优秀，可以解决大多数这些挑战。当然，这并不意味着它们已经准备好解决更复杂的真实软件挑战。

话虽如此，我只是出于好奇决定开始这个有趣的小项目。希望你喜欢它，并能为你提供一些关于我们与LLM+编程未来发展方向的见解🤗

# 海科·霍茨

👋 在[Medium](https://heiko-hotz.medium.com/)和[LinkedIn](https://www.linkedin.com/in/heikohotz/)上关注我，阅读更多关于生成式人工智能、机器学习和自然语言处理的内容。

👥 如果你在伦敦，可以加入我们的[NLP London Meetups](https://www.meetup.com/nlp_london/)。

![](../Images/ed0b16f80eb6e1e9f99290b10638f4d2.png)

图片由作者提供
