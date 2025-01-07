# 在 Python 和 Mathematica 中可视化复值函数

> 原文：[https://towardsdatascience.com/today-i-was-pouring-through-my-complex-variables-and-analytic-functions-book-written-by-the-e9205f71485d?source=collection_archive---------8-----------------------#2024-02-19](https://towardsdatascience.com/today-i-was-pouring-through-my-complex-variables-and-analytic-functions-book-written-by-the-e9205f71485d?source=collection_archive---------8-----------------------#2024-02-19)

## 解锁数学难题的视觉洞察

[](https://medium.com/@Dani_Lisle?source=post_page---byline--e9205f71485d--------------------------------)[![Dani Lisle](../Images/2933bbbca26cf198e7964547a91b2751.png)](https://medium.com/@Dani_Lisle?source=post_page---byline--e9205f71485d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e9205f71485d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e9205f71485d--------------------------------) [Dani Lisle](https://medium.com/@Dani_Lisle?source=post_page---byline--e9205f71485d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e9205f71485d--------------------------------) ·3 分钟阅读·2024年2月19日

--

今天我正在翻阅由受尊敬的Fornberg和Piret编写的*《复变函数与解析函数》*，尽力理解复值函数的行为。由于这些函数既有实数输入，也有虚数输入，并且输出两个分量，因此，单一的3D图形不足以展示函数的行为。我们需要将这种可视化拆分为实部和虚部的单独图表，或者按幅度和相位（角度）来展示。

我希望能够随意操作任何想到的函数，拖动和缩放其图表，并在可视化细节中探索它，以理解它如何从方程中得出。对于这样的任务，Wolfram Mathematica 是一个很好的起始工具。

# Wolfram 语言

```py
plotComplexFunction[f_]:=Module[{z,rePlot,imPlot,magPlot,phasePlot},z=x+I y;

rePlot = Plot3D[Re[f[z]],{x,-2,2},{y,-2,2},AxesLabel->{"Re(z)","Im(z)","Re(f(z))"},Mesh->None];

imPlot = Plot3D[Im[f[z]],{x,-2,2},{y,-2,2},
 AxesLabel->{"Re(z)","Im(z)","Im(f(z))"},
 Mesh->None];

magPlot = Plot3D[Abs[f[z]], {x, -2, 2}, {y, -2, 2}, 
 AxesLabel -> {"Re(z)", "Im(z)", "Abs(f(z))"}, 
 Mesh -> None, 
 ColorFunction -> Function[{x, y, z}, ColorData["Rainbow"][Rescale[Arg[x + I y], {-Pi, Pi}]]], 
 ColorFunctionScaling -> False];

phasePlot=DensityPlot[Arg[f[z]],{x,-2,2},{y,-2,2},
 ColorFunction->"Rainbow",
 PlotLegends->Automatic,
 AxesLabel->{"Re(z)","Im(z)"},
 PlotLabel->"Phase"];

GraphicsGrid[{{rePlot,imPlot},{magPlot,phasePlot}},ImageSize->800]];

f[z_]:=(1/2)*(z+1/z);

plotComplexFunction[f]
https://github.com/dreamchef/complex-functions-visualization
```

[https://github.com/dreamchef/complex-functions-visualization](https://github.com/dreamchef/complex-functions-visualization)

我编写了上述 Mathematica 代码，用于生成一个图表网格，展示如上所述的两种方式的函数。在顶部显示了函数的虚部和实部，

![](../Images/9b6073b85b9639da070285491a0935ae.png)

底部显示了幅度和相位，以颜色表示：

![](../Images/0234fbdcc4486ce649d41dee4561a6a0.png)

来自 Wolfram 的分量和幅度-相位图 f(z)

在用这段代码玩了几个函数并确信它们有意义之后，我对如何在 Python 中实现相同功能产生了兴趣，以便将其与其他数学编程项目连接起来。

# Python、PyPlot和complex-plotting-tools

我在GitHub上找到了一个很棒的项目（[https://github.com/artmenlope/complex-plotting-tools](https://github.com/artmenlope/complex-plotting-tools)），我决定将其作为起点，并有可能在未来做出贡献。这个仓库提供了一个非常简单的界面，用于以多种方式绘制复值函数。感谢[https://github.com/artmenlope](https://github.com/artmenlope)！例如，在导入`numpy`、`matplotlib`和该仓库的`cplotting_tools`模块后，定义函数并调用`cplt.complex_plot3D(x,y,f,log_mode=False)`，将生成以下内容：

![](../Images/da80e1f86e6827c86fbbf1432799ac18.png)

来自complex-plotting-tools的f(z)幅度和相位图

这些图都是用于上面相同的f(z)。要查看该函数的虚部和实部并排显示，可以使用`cplot.plot_re_im(x,y,f,camp="twilight",contour=False,alpha=0.9)`：

![](../Images/4d9943d906a41a280988bde245410cb8.png)

来自complex-plotting-tools的分量绘图

此外，该库还提供了其他酷炫的方式来研究函数，包括流线图：

![](../Images/c026babc96d69ed06a58d6453cc920af.png)

来自complex-plotting-tools的f(z)流线图

# 未来方向

这个库展示了很大的潜力，且相对容易使用！它确实需要定义一个`pts`变量，用于编码给定函数的极点和零点。Wolfram不需要这个，因为它在幕后计算这些点的位置。如果complex-plotting-tools也具备这个功能，将为用户节省大量的精力。我计划在不久的将来将其实现到该模块中。

与此同时，尽情享受使用Wolfram和Python绘图的乐趣，欢迎在下方评论中分享您的想法和问题，或通过[LinkedIn](http://linkedin.com/in/danilisle)与我联系，或在[GitHub](http://github.com/dreamchef)与我合作！

*除非另有说明，所有图片均由作者创建。*
