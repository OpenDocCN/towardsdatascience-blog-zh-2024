# Pre-Commit & Git 钩子：自动化高质量代码

> 原文：[https://towardsdatascience.com/pre-commit-git-hooks-automate-high-code-quality-fbcbaa720e52?source=collection_archive---------9-----------------------#2024-08-09](https://towardsdatascience.com/pre-commit-git-hooks-automate-high-code-quality-fbcbaa720e52?source=collection_archive---------9-----------------------#2024-08-09)

## 如何通过 pre-commit 和 git 钩子提高代码质量

[](https://medium.com/@egorhowell?source=post_page---byline--fbcbaa720e52--------------------------------)[![Egor Howell](../Images/1f796e828f1625440467d01dcc3e40cd.png)](https://medium.com/@egorhowell?source=post_page---byline--fbcbaa720e52--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fbcbaa720e52--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fbcbaa720e52--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--fbcbaa720e52--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fbcbaa720e52--------------------------------) ·阅读时间：6分钟·2024年8月9日

--

![](../Images/0bb1682ecb04d7a2da46cba3d1e853e9.png)

图片来自 [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) 提供的 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 什么是 Pre-Commit？

[Pre-commit](https://pre-commit.com/) 是一个 Python 包，它使得创建预提交钩子变得更加容易。钩子是 git 的本地功能，它们是在特定的 git 命令执行之前运行的脚本。

你可以在仓库的 `.git/hooks` 目录中找到钩子文件，这个目录是由 git 自动填充的。如果你查看这个目录，你会发现类似于下面的文件：

```py
applypatch-msg.sample     pre-commit.sample         prepare-commit-msg.sample
commit-msg.sample         pre-merge-commit.sample   push-to-checkout.sample
fsmonitor-watchman.sample pre-push.sample           update.sample
post-update.sample        pre-rebase.sample
pre-applypatch.sample     pre-receive.sample
```

`.sample` 扩展名防止这些钩子被执行。要启用钩子，请删除 `.sample` 扩展名并编辑文件。

然而，这种方式繁琐且不太友好，而且很难使用版本控制来管理。这时，pre-commit 就派上用场了。它为 `commit` 命令创建了一个钩子，可以自动检测代码中的问题，并使得创建脚本变得更加流畅。
