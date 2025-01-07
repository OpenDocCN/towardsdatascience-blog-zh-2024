# 设置新Apple M3 MacBook Pro的必备清单

> 原文：[https://towardsdatascience.com/essential-checklist-for-setting-up-your-new-apple-m3-macbook-pro-306e94e974b4?source=collection_archive---------3-----------------------#2024-02-10](https://towardsdatascience.com/essential-checklist-for-setting-up-your-new-apple-m3-macbook-pro-306e94e974b4?source=collection_archive---------3-----------------------#2024-02-10)

## 一本关于迁移书签、终端增强和AWS CLI设置的实用参考

[](https://medium.com/@wen_yang?source=post_page---byline--306e94e974b4--------------------------------)[![Wen Yang](../Images/5eac438762d015a0ab128757cc951967.png)](https://medium.com/@wen_yang?source=post_page---byline--306e94e974b4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--306e94e974b4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--306e94e974b4--------------------------------) [Wen Yang](https://medium.com/@wen_yang?source=post_page---byline--306e94e974b4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--306e94e974b4--------------------------------)·8分钟阅读·2024年2月10日

--

![](../Images/ae16cee74e6192c5420ce3b6eb073ddf.png)

图片由作者使用MidJourney生成

我最近收到了一台配有最新Apple M3芯片的16英寸MacBook Pro作为我的工作电脑。我听说过Apple M1和M2芯片的高速表现，所以我非常激动地拿到了这台搭载M3芯片的机器。

在这篇博客中，我将介绍我为顺利过渡到工作流程所做的设备配置步骤。

我的主要目标是：

+   通过导出Chrome书签和1Password，方便地访问常用链接和登录信息。

+   使用`iTerm2`、`Oh My Zsh`和`PowerLevel10K主题`自定义一个美观的终端环境

+   使用`PyCharm`和`AWS Cli`正确运行我的工作项目仓库。

如果你是数据科学家、Python开发者，或是任何每天使用终端和AWS CLI的人，那么在设置新MacBook时，特别是从Intel芯片过渡到Apple Silicon芯片的设备时，你可能会发现本指南非常有帮助。

# 1. 导出Chrome书签
