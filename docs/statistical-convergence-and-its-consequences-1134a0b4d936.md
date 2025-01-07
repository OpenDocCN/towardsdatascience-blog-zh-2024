# 统计收敛及其后果

> 原文：[https://towardsdatascience.com/statistical-convergence-and-its-consequences-1134a0b4d936?source=collection_archive---------4-----------------------#2024-05-15](https://towardsdatascience.com/statistical-convergence-and-its-consequences-1134a0b4d936?source=collection_archive---------4-----------------------#2024-05-15)

![](../Images/fe5810a04224f0edfa5eaa5ee5cbdead.png)

一幅描绘约1800年代的桨轮蒸汽船的画作，船只正遭遇风暴，船员已放弃她。艺术家：约翰·亨德里克·路易斯·梅耶（Johan Hendrik Louis Meijer）。([公有领域图片](https://commons.wikimedia.org/wiki/File:Louis_Meijer_French_paddle_steamer_at_sea.jpg))

## 这是一个关于随机变量收敛的故事，发生在尼姆罗德号这次非凡航行的背景下。

[](https://timeseriesreasoning.medium.com/?source=post_page---byline--1134a0b4d936--------------------------------)[![Sachin Date](../Images/bd023298b414caf88f79b00ef032d065.png)](https://timeseriesreasoning.medium.com/?source=post_page---byline--1134a0b4d936--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1134a0b4d936--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1134a0b4d936--------------------------------) [Sachin Date](https://timeseriesreasoning.medium.com/?source=post_page---byline--1134a0b4d936--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1134a0b4d936--------------------------------) ·30分钟阅读·2024年5月15日

--

1860年2月25日星期六早晨，客轮尼姆罗德号（Nimrod）从英格兰利物浦的码头启航，目的地是爱尔兰的科克港——大约300英里的距离。尼姆罗德号之前已经多次往返这条航线，像她一样的数百艘船只穿越爱尔兰和英格兰之间的海域，将来自爱尔兰港口的家庭接走，并送往英格兰的港口，随后他们会继续移民到美国。

尼姆罗德号是一艘强劲的铁桨蒸汽船，配备蒸汽发动机和全套帆具——是她那个时代现代、强大且经过海上考验的船只。船长莱尔（Lyall）掌舵——他是一位经验丰富的海员，曾参与过[克里米亚战争](https://en.wikipedia.org/wiki/Crimean_War)，并且是船员们敬爱的、深受喜爱的领导者。船上还有45名船员和价值数千英镑的货物。1860年是一个异常寒冷和潮湿的年份，但在2月25日的那个周末，海面平静，天际没有暴风雨的迹象。

![](../Images/a7e855ec1a28d820020e133ed67d96b9.png)

斯莫尔斯灯塔，拍摄于2018年夏季。原始结构在1831年遭受严重的风暴损坏后，于1857年至1861年间重新修建。1978年在其上建造了一个直升机停机坪。（来源：[Wikimedia](https://commons.wikimedia.org/wiki/File:Smalls_LIghthouse_-_10th_June_2018.jpg) 采用[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.en)协议）
