# 如何创建精美样式的Streamlit数据框架，第1部分：使用Pandas Styler

> 原文：[https://towardsdatascience.com/how-to-create-well-styled-streamlit-dataframes-part-1-using-the-pandas-styler-e239c0fbe145?source=collection_archive---------6-----------------------#2024-08-14](https://towardsdatascience.com/how-to-create-well-styled-streamlit-dataframes-part-1-using-the-pandas-styler-e239c0fbe145?source=collection_archive---------6-----------------------#2024-08-14)

## Streamlit和pandas的Styler对象并不兼容。但我们将改变这一点！

[](https://medium.com/@joparga3?source=post_page---byline--e239c0fbe145--------------------------------)[![Jose Parreño](../Images/707d5179926d36fba257f5476494e10e.png)](https://medium.com/@joparga3?source=post_page---byline--e239c0fbe145--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e239c0fbe145--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e239c0fbe145--------------------------------) [Jose Parreño](https://medium.com/@joparga3?source=post_page---byline--e239c0fbe145--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e239c0fbe145--------------------------------) ·阅读时长7分钟·2024年8月14日

--

![](../Images/cfca92ce3276a325243056712ff79c6d.png)

我一直是[pandas中的styler方法](https://pandas.pydata.org/docs/user_guide/style.html)的粉丝。当我开始构建Streamlit应用时，我很清楚我希望为数据框架添加样式，以帮助可视化数据框架，但……惊喜！截至目前，Streamlit [st.dataframe()](https://docs.streamlit.io/develop/api-reference/data/st.dataframe) **不支持styler对象**，只支持数据框架对象。好吧，修正一下，它确实支持它们，但UI显示非常糟糕！

这就是为什么我想与大家分享我的解决方法和想法，用于在Streamlit中构建一个样式良好的数据框架。我们将涵盖：

1.  **如何在数字中添加千位分隔符。**

1.  **如何将数字显示为百分比**（*将数据中的0.24转为UI中的24%*）

1.  **如何添加货币符号。**

1.  **如何为单元格添加颜色。** 更棒的是，我将分享我最喜欢的颜色分级函数。

1.  **如何添加表情符号！** 是的，我们离不开表情符号😊！

# st.dataframe()的默认视图

Streamlit实际上在根据数据类型推断最佳显示效果方面表现得相当不错。想象一下……
