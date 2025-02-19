# 指控性 AI：广泛滥用 AI 技术如何伤害学生

> 原文：[`towardsdatascience.com/accusatory-ai-how-misuse-of-technology-is-harming-students-56ec50105fe5?source=collection_archive---------6-----------------------#2024-12-09`](https://towardsdatascience.com/accusatory-ai-how-misuse-of-technology-is-harming-students-56ec50105fe5?source=collection_archive---------6-----------------------#2024-12-09)

## 观点

## 当 AI 指控学生利用 AI 作弊时，应该怎么做？

[](https://objf.medium.com/?source=post_page---byline--56ec50105fe5--------------------------------)![James F. O'Brien](https://objf.medium.com/?source=post_page---byline--56ec50105fe5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--56ec50105fe5--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--56ec50105fe5--------------------------------) [James F. O'Brien](https://objf.medium.com/?source=post_page---byline--56ec50105fe5--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--56ec50105fe5--------------------------------) ·6 分钟阅读·2024 年 12 月 9 日

--

检测由 AI 系统生成的内容的反作弊工具正在被广泛[教育工作者使用](https://www.bloomberg.com/news/features/2024-10-18/do-ai-detectors-work-students-face-false-cheating-accusations)，以检测和惩罚书面和编程作业中的作弊行为。然而，这些 AI 检测系统[似乎效果并不好](https://arstechnica.com/information-technology/2023/07/why-ai-detectors-think-the-us-constitution-was-written-by-ai/)，不应该用于惩罚学生。即使是最好的系统，也会有[某种非零的误报率](https://www.researchgate.net/publication/378200791_AI_Detection's_High_False_Positive_Rates_and_the_Psychological_and_Material_Impacts_on_Students)，这导致一些真实的学生在实际独立完成作业时，却被判定为作弊。AI 检测器被广泛使用，而被错误指控的学生涵盖了从[小学](https://highschool.latimes.com/saint-ignatius-college-preparatory/opinion-a-generation-cheated-by-ai/#:~:text=Sydney%20Gill&text=Being%20falsely%20accused%20of%20using%20AI%20to%20cheat%20wasn't,partnered%20with%20my%20middle%20school)到[研究生院](https://pubmed.ncbi.nlm.nih.gov/38516933/)的学生群体。

![](img/e46843b4f32c8791e9fd8d927d643cad.png)

在这些错误指控的案例中，造成有害不公的责任可能并不在于提供工具的公司。如果你查看他们的文档，通常会看到类似以下内容：

> “AI 生成内容的性质在不断变化。因此，这些结果不应被用来惩罚学生。……总会有一些边缘案例，在这些案例中，AI 被误判为人类，或者人类被误判为 AI。”
> 
> — [*引用自 GPTZero 的 FAQ*](https://support.gptzero.me/hc/en-us/articles/15129396117143-What-are-the-limitations-of-GPTZero-s-AI-classifier)*.*

换句话说，开发这些服务的人知道它们并不完美。负责任的公司，比如上面引用的公司，明确承认这一点，并清楚声明他们的检测工具不应被用来惩罚，而是应当用于在何时与学生建立建设性联系上有所帮助。仅仅因为检测工具触发了警报就给作业判不及格，实际上是判分者的疏忽懒惰。

如果你面临与 AI 工具相关的作弊指控，或者正在提出这样的指控，请考虑以下关键问题：

+   使用了什么检测工具，工具具体宣称能做什么？如果答案类似于上面引用的文本，明确表示结果不用于惩罚学生，那么判分者显然在误用该工具。

+   在你的具体情况下，是否是判分者需要承担证明责任，来为处罚提供依据？如果是这样，那么他们应该能够提供一些证据，支持该工具有效的声明。任何人都可以建立一个网站，简单地使用 LLM 对输入进行表面上的评估，但如果这个工具要作为反对学生的证据，那么就需要对该工具进行正式的评估，以证明其可靠性。此外，这个评估需要是科学有效的，并由一个无偏的第三方进行。

+   在你的具体情况下，学生是否有权审查用于指控他们的证据和方法？如果有，那么指控可能无效，因为 AI 检测软件通常[不允许提供所需的透明度](https://rtl.berkeley.edu/news/availability-turnitin-artificial-intelligence-detection)。

+   学生或家长是否为英语作为第二语言的人？如果是，那么案件中可能存在歧视的因素。以英语为第二语言的人通常会直接翻译他们母语中的习语或其他常见短语，导致最终的文本出现不寻常的短语，这些短语[已知会错误触发](https://hai.stanford.edu/news/ai-detectors-biased-against-non-native-english-writers)这些检测器。

+   学生是否属于一个使用自己习语或英语方言的少数群体？像第二语言使用者一样，这些不太常见的短语可能会[错误触发 AI 检测器](https://www.edweek.org/technology/black-students-are-more-likely-to-be-falsely-accused-of-using-ai-to-cheat/2024/09)。

+   被指控的学生是否是神经多样性学生？如果是，那么这可能是案件中的另一个歧视方面。比如，自闭症患者可能使用一些对他们来说完全合乎逻辑的表达方式，但其他人可能觉得这些表达方式很奇怪。这些表达方式并没有问题，但它们不寻常，且[AI 检测器可能会因其而被触发](http://www.autismpolicyblog.com/2023/07/autistic-language-patterns-and-problem.html)。

+   被指控的作品是否非常简短？AI 检测器的关键理念是，它们会寻找不常见的词语组合和/或代码指令，这些组合人类很少使用，但生成性 AI 却常常使用。在较长的作品中，可能会发现很多这样的组合，从而统计上人类偶然使用这些组合的可能性就会很小。然而，作品越简短，偶然使用的可能性就越高。

+   有什么证据表明学生完成了这项工作？如果问题中的作业超过几段文字或几行代码，那么很可能有历史记录显示工作的逐步发展。Google Docs、Google Drive 和 iCloud Pages 都会保留更改历史记录。大多数计算机也会将版本历史记录作为备份系统的一部分进行保存，例如苹果的 Time Machine。也许学生将不同版本的草稿通过电子邮件发送给合作伙伴、家长，甚至是老师，而这些邮件就形成了逐步完成工作的记录。如果学生使用 GitHub 进行编码，那么就有清晰的提交历史。逐步发展的清晰历史展示了学生是如何随着时间完成这项工作的。

为了明确，我认为这些 AI 检测工具在教育中有其应用场所，但正如这些网站自己明确声明的那样，它们的作用不是抓捕作弊者并惩罚学生。事实上，许多这些网站提供了如何建设性地处理疑似作弊的指导。这些 AI 检测器是工具，像任何强大的工具一样，如果正确使用，它们可以发挥巨大作用，而如果使用不当，则可能造成很大的伤害。

***如果你或你的孩子被不公正地指控使用 AI 代写并因此受到惩罚，那么我建议你把这篇文章以及我提供的链接文章给老师/教授看。如果指控者不肯松口，那么我建议你联系律师，了解是否可以对老师和学校/学区提起诉讼。***

尽管建议咨询律师，但我并不是反对教育工作者，认为优秀的教师不应该因为成绩问题而被起诉。然而，滥用工具并伤害学生的教师并不是好老师。当然，一位有良好意图的教育者可能会因为没有意识到工具的局限性而误用它，但当获得新信息时，应该重新评估。

> “宁可让 100 个有罪的人逃脱，也不让一个无辜的人受苦。” — [*本杰明·富兰克林，1785 年*](https://en.wikipedia.org/wiki/Blackstone%27s_ratio)

作为一名教授，我也曾在课堂上与作弊问题作斗争。没有简单的解决方案，使用人工智能检测器来让学生失败不仅无效，而且不负责任。我们是教育者，不是警察或检察官。我们的角色应该是支持学生，而不是随意惩罚他们。这包括作弊者，尽管他们可能有不同的看法。作弊并不是对教育者的个人侮辱，也不是对其他学生的攻击。在课程结束时，唯一真正受害的人是作弊者自己，他们浪费了时间和金钱，却没有获得任何真正的知识或经验。（按曲线评分，或者以其他方式让学生相互竞争，[有许多不好的理由](https://www.nytimes.com/2016/09/11/opinion/sunday/why-we-should-stop-grading-students-on-a-curve.html)，在我看来，应该避免。）

最后，人工智能系统已经存在，并且像计算器和计算机一样，它们将彻底改变人们[在不久的将来如何工作](https://medium.com/towards-data-science/the-end-of-required-work-universal-basic-income-and-ai-driven-prosperity-df7189b371fe)。教育需要发展，并教导学生如何负责任且有效地使用人工智能。我自己写了这篇文章的初稿，但随后我请了一位大型语言模型（LLM）阅读它，给我反馈并提出建议。我可能也能在没有 LLM 的情况下得到类似的结果，但那样我可能会请朋友帮忙阅读并提出建议，那样会花费更长时间。与 LLM 合作的过程对我来说并不独特，我的同事们也广泛使用这种方式。或许，与其追查人工智能的使用，我们应该教会学生如何使用它。当然，学生仍然需要学习基础知识，但他们也需要学会如何使用这些强大的工具。如果他们不学会，那么那些会使用人工智能的同学将会有巨大的优势。

*关于我：* [*詹姆斯·F·奥布莱恩*](http://jamesobrien.com/) *是加利福尼亚大学伯克利分校的计算机科学教授。他的研究兴趣包括计算机图形学、计算机动画、物理系统模拟、人类感知、渲染、图像合成、机器学习、虚拟现实、数字隐私以及图像和视频的法医分析。*

*如果你觉得这个有趣，那么这里是常用的* [*关注*](https://objf.medium.com/) *和* [*订阅*](https://objf.medium.com/subscribe) *链接。你还可以在* [*Instagram*](https://www.instagram.com/jamesfobrien/)*、* [*LinkedIn*](https://www.linkedin.com/in/jamesfobrien/)* 和* [*加州大学伯克利分校*](http://obrien.berkeley.edu/)*找到我。*

*免责声明：本文中表达的任何观点仅代表作者作为个人的立场。本文中的任何内容都不应被解读为与作者在任何机构的职业身份相关的声明。*

*本文及所有嵌入的图片版权归作者所有，版权所有 2024 年。本文章由人类编写，同时使用了 LLM（Llama 3.2 3B）以及其他人类进行校对和编辑建议。编辑用图由 AI（Adobe Firefly）生成，之后由人类使用 Photoshop 进行了大量编辑。*
