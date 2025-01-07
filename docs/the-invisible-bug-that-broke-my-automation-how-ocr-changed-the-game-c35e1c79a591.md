# 打破我的自动化的隐形错误：OCR 如何改变了游戏

> 原文：[https://towardsdatascience.com/the-invisible-bug-that-broke-my-automation-how-ocr-changed-the-game-c35e1c79a591?source=collection_archive---------9-----------------------#2024-12-17](https://towardsdatascience.com/the-invisible-bug-that-broke-my-automation-how-ocr-changed-the-game-c35e1c79a591?source=collection_archive---------9-----------------------#2024-12-17)

## 测试自动化中的 AI 发展：从定位器到生成性 AI（第三部分）

[](https://abdel-k.medium.com/?source=post_page---byline--c35e1c79a591--------------------------------)[![Abdelkader HASSINE](../Images/b067c4283fc44816e7ab90fccdcaba57.png)](https://abdel-k.medium.com/?source=post_page---byline--c35e1c79a591--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c35e1c79a591--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c35e1c79a591--------------------------------) [Abdelkader HASSINE](https://abdel-k.medium.com/?source=post_page---byline--c35e1c79a591--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c35e1c79a591--------------------------------) ·8分钟阅读·2024年12月17日

--

![](../Images/ad032e5584fe048212d91127b9744000.png)

测试自动化中的 AI 发展：从定位器到生成性 AI（第三部分） — © Tung Nguyen 来自 Pixabay

现在是星期天早上10点，我盯着又一份失败的测试报告。这个测试案例可不简单，是我在上周某些UI更改后检查并调试过的。结果现在它神秘地失败了。再次……

错误？显然没有错误。

应该显示的内容是：欢迎屏幕上应该显示这段文本：

“欢迎使用 my-app-name 应用程序！”

我查看了应用程序的截图，文本显示正确……没有问题：

![](../Images/fe604688096dcb599f3dfe2855ed971a.png)

作者的手机截图，显示预期的文本正确显示 —— 由于保密问题已模糊处理

> 非会员可以通过[此链接](https://medium.com/towards-data-science/the-invisible-bug-that-broke-my-automation-how-ocr-changed-the-game-c35e1c79a591?sk=3fa30cb96860ab8a456a1098c99f1057)继续阅读（来自Medium移动应用的读者可能由于现有错误无法点击该链接，请在评论中找到该链接）

我决定查看一下 Appium 的 XML：

```py
<XCUIElementTypeStaticText type="XCUIElementTypeStaticText" 
value="Bienvenue à bord de l'application&#10;my-app-name !" 
enabled="true"…
```
