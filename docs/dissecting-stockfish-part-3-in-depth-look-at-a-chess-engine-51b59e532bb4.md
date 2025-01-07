# 解剖Stockfish 第三部分：深入了解棋类引擎

> 原文：[https://towardsdatascience.com/dissecting-stockfish-part-3-in-depth-look-at-a-chess-engine-51b59e532bb4?source=collection_archive---------6-----------------------#2024-07-22](https://towardsdatascience.com/dissecting-stockfish-part-3-in-depth-look-at-a-chess-engine-51b59e532bb4?source=collection_archive---------6-----------------------#2024-07-22)

## 大规模浏览棋局树

[](https://medium.com/@antoine.champion?source=post_page---byline--51b59e532bb4--------------------------------)[![Antoine Champion](../Images/dfd05ce8d2081c94e388254731fe7174.png)](https://medium.com/@antoine.champion?source=post_page---byline--51b59e532bb4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--51b59e532bb4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--51b59e532bb4--------------------------------) [Antoine Champion](https://medium.com/@antoine.champion?source=post_page---byline--51b59e532bb4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--51b59e532bb4--------------------------------) ·阅读时间5分钟·2024年7月22日

--

![](../Images/e3759642068a88760c83493078f2d5eb.png)

Stockfish 棋类引擎，背景照片由[ᴊᴀᴄʜʏᴍ ᴍɪᴄʜᴀʟ](https://unsplash.com/@jachymmichal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)提供，图片来自[Unsplash](https://unsplash.com/s/photos/chess?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

欢迎回到我们关于Stockfish棋类引擎内部运作的系列文章。本系列的目标是解释使Stockfish成为世界上最强大的棋类引擎之一的算法和技术。通过了解这些机制，我们可以深入理解计算机科学、人工智能和博弈论的交汇点。

本系列的前几部分探讨了Stockfish是如何找到一个可行的棋步（[第一部分](https://medium.com/towards-data-science/dissecting-stockfish-part-1-in-depth-look-at-a-chess-engine-7fddd1d83579)）并评估该棋步的局势质量（[第二部分](/dissecting-stockfish-part-2-in-depth-look-at-a-chess-engine-2643cdc35c9a)）。但如何考虑我们的对手接下来可能的棋步呢？我们又该如何回应？

为了应对这种情况，Stockfish依赖于一个最终概念：深度。

# 所有棋步的树

游戏开始：兵到e4。对手回应e5。然后是Nf3，Nc6，依此类推。这个序列在所有可能的棋步树中形成了一条分支。

Stockfish浏览这个树状结构，以基于所有可能的结果确定最佳棋步。

![](../Images/16ab623cbace833e3d4b82cb6a350c6a.png)

高层概述 — 图片由作者提供

## 最坏的最优结果

在博弈论中，有一个适用于回合制游戏的通用算法：**Minimax 算法**。其核心在于，由于无法预测对手的棋步，因此假设对手总是为自己选择最好的棋步。

下面的图示展示了这一点：

![](../Images/be81af5a92f71021445ee059068626cf.png)

国际象棋的 Minimax 算法。正分意味着白方占优，负分意味着黑方占优 — 图片由作者提供

+   对于*主教到 G3*这一步，Stockfish 会考虑对手的所有可能回应。

+   尽管某些棋步，如*兵到 A7*或*A6*，会得到正分，但*车到 E8*的棋步却导致了 -5.6 的不利局面。

+   这个最坏的结果使得*主教到 G3*的分数为 -5.6，因为假设对手（黑方）会找到并下出最佳的棋步。

+   计算完所有棋步和回应后，Stockfish 为自己（白方）选择了最佳的选项，即*骑士到 D4*。

尽管那个例子展示了如何使用单一棋步及对手的回应，Minimax 算法可以递归地扩展到无限深度。限制因素是计算资源，这也引出了下一部分。

# 优化与权衡

尽管 Stockfish 每秒可以评估数百万个棋步，但由于每增加一层深度，可能的棋步呈指数级增长，它仍然无法在合理的时间内评估整个棋局。

例如，[克劳德·香农证明](https://en.wikipedia.org/wiki/Shannon_number)，要从起始位置达到10步的深度，需要评估690亿个位置。仅使用 Minimax 算法，这将需要几天时间。

Stockfish 对 Minimax 算法进行了多项改进。其中之一是**Alpha-Beta 剪枝**，它优化了移动树的遍历。如下图所示：

![](../Images/30800d0c726948a806121b8fc2cae162.png)

国际象棋中的 Alpha-Beta 剪枝 — 图片由作者提供

+   Stockfish 计算得出，序列*车到 E8 -> 骑士到 G4 -> 主教到 G4*将导致 -5 的不利局面。

+   另一序列*车到 E8 -> 主教到 C6*已经被探索过，并得出了 -1 的分数，这比当前正在探索的分支要好。

+   因此，*骑士到 G4*可以被舍弃，转而选择更好的选项：*主教到 C6*。

其他技术，如迭代加深，进一步提升了这个过程：当引擎在深度 N 上进行计算时，它会存储搜索的最佳线路，这样在深度 N+1 时，搜索时可以优先探索这些棋步。

Stockfish 的最终大局搜索算法非常复杂（见 [search.cpp](https://github.com/official-stockfish/Stockfish/blob/master/src/search.cpp)），但它利用了另一种现代计算技术：多线程。

## 分布式搜索

现代计算机可以使用多线程，从而使 Stockfish 能够利用分布式计算能力进行扩展。

为此，Stockfish利用多个线程并行搜索最佳走法，每个线程通过并发内存存储系统进行通信。

![](../Images/cbbe0e8bdfd6d63504325cdb915331fb.png)

使用共享字典的并行计算 — 图片由作者提供

计算线程并行搜索树。

+   当一个线程完成一个分支时，它将结果写入共享字典。

+   当一个线程开始一个新分支时，它会检查字典中是否有其他线程已经计算过该分支。

还有一个主线程充当协调者：

+   它从线程池中存储并启动计算线程。

+   它为每个计算线程提供初始条件（例如，搜索树时的顺序偏移，以增加搜索熵并更快地填充字典）。

+   它监视线程是否完成计算，若完成，则停止所有计算线程，并从字典中读取评估结果。

> 有趣的是，访问“真正的”并发字典时，内存锁所需的几纳秒会产生过多的开销。因此，Stockfish的程序员开发了自己的分布式表格（参见[tt.h](https://github.com/official-stockfish/Stockfish/blob/master/src/tt.h)）。

# 结论

总结：

+   Stockfish为给定深度生成每一个候选走法。

+   它在树中使用各种优化方法评估这些走法。

+   它增加了评估深度，并重复这一过程。

通过这个系列，我们揭示了Stockfish如何将经典算法与现代计算技术和神经网络结合，实现最先进的性能。

理解Stockfish的内部工作原理，不仅能解开这款最强国际象棋引擎之一的神秘面纱，还能为计算和人工智能中的挑战及解决方案提供更广泛的见解。由于其固有特性，Stockfish主要关注效率，这一主题在计算能力不断提升的背景下，已在人工智能领域变得越来越少见。此外，Stockfish还是如何从AI核心构建一个完整的分布式系统的一个范例。

我希望这个系列对你有所启发并具有教育意义。感谢阅读！

若要进一步阅读，你可以查阅[国际象棋编程百科](https://www.chessprogramming.org/Main_Page)，并加入[计算机国际象棋俱乐部](https://talkchess.com/)论坛，讨论国际象棋编程。

[https://antoinechampion.com](https://antoinechampion.com/)
