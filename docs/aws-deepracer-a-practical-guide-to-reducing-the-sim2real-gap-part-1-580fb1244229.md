# AWS DeepRacer：减少Sim2Real差距的实用指南 — 第1部分 || 准备赛道

> 原文：[https://towardsdatascience.com/aws-deepracer-a-practical-guide-to-reducing-the-sim2real-gap-part-1-580fb1244229?source=collection_archive---------9-----------------------#2024-08-21](https://towardsdatascience.com/aws-deepracer-a-practical-guide-to-reducing-the-sim2real-gap-part-1-580fb1244229?source=collection_archive---------9-----------------------#2024-08-21)

## 最小化视觉干扰以最大化成功圈次

[](https://shrey-pareek.medium.com/?source=post_page---byline--580fb1244229--------------------------------)[![Shrey Pareek, PhD](../Images/e1169ff2f538e8bc9f64c6f591bf1f80.png)](https://shrey-pareek.medium.com/?source=post_page---byline--580fb1244229--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--580fb1244229--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--580fb1244229--------------------------------) [Shrey Pareek, PhD](https://shrey-pareek.medium.com/?source=post_page---byline--580fb1244229--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--580fb1244229--------------------------------) ·阅读时间8分钟·2024年8月21日

--

是否曾经想过，为什么你的DeepRacer在模拟环境中表现得完美无缺，但在现实世界中却连一个转弯都无法完成？继续阅读，了解其中的原因及如何解决常见问题。

AWS DeepRacer在真实赛道上的视频演示。视频由作者提供。

+   [**第1部分 (2024年8月20日)**](/aws-deepracer-a-practical-guide-to-reducing-the-sim2real-gap-part-1-580fb1244229) : 赛道和周围环境的设置。

+   [**第2部分 (2024年8月26日)**](https://medium.com/towards-data-science/aws-deepracer-a-practical-guide-to-reducing-the-sim2real-gap-part-2-training-guide-e96805cd7141): 动作空间和奖励函数设计以及训练范式。

在本指南中，我将分享一些实用的技巧和方法，帮助你让[AWS DeepRacer](https://aws.amazon.com/deepracer/)自主地在赛道上运行。我将包括有关在模拟环境中训练强化学习代理的信息，以及*更为重要*的实际建议，帮助你成功地让汽车在真实赛道上运行——这就是所谓的模拟到现实（**sim2real**）挑战。

在**第1部分**中，我将描述在真实赛道上驾驶汽车时需要注意的物理因素。我会讲解汽车的摄像头传感器（及其局限性）以及如何准备你的物理空间和赛道。在后续部分中，我们将讨论训练过程和奖励函数的最佳实践。我决定首先关注物理因素，而非训练，因为我认为在模拟训练之前理解物理限制更为重要。

正如你将通过这系列文章看到的那样，关键目标是减少由光照变化和背景运动引起的相机干扰。

# 汽车与传感器

![](../Images/f21e194429877554df724df9c32b9d84.png)

AWS DeepRacer。图片来自作者。

这辆车是一款1/18比例的赛车，配备RGB（红绿蓝）相机传感器。来自[AWS](https://docs.aws.amazon.com/deepracer/latest/developerguide/deepracer-choose-race-type.html#deepracer-how-it-works-autonomous-driving-sensors)：

> 相机配备有120度广角镜头，并捕捉RGB图像，这些图像随后被转换为160 x 120像素的灰度图像，以每秒15帧（fps）的速度显示。这些传感器属性在模拟器中得以保留，以最大限度地提高训练模型从模拟到现实世界的转移效果。

这里需要注意的关键点是相机使用*160 x 120像素的灰度图像*。这大致意味着相机擅长将浅色或*白色*像素与深色或*黑色*像素分开。介于这两者之间的像素，即*灰色*，可以用来表示额外的信息。

DeepRacer的RGB视图（左）和灰度视图（右）。尽管相机捕捉到的是RGB图像，但它们在推理时会被转换为灰度图像。请注意轨道上的褶皱和光反射，这增加了模拟与现实之间的差距。视频由作者提供。

**从本文中需要记住的最重要**的事情如下：

> 汽车仅使用**黑白图像**来理解其周围环境。它并不*识别*物体，而是学习避免或停留在不同的灰色像素值上（从黑到白）。

**因此，我们所采取的所有步骤，从*轨道准备*到*训练模型*，都将考虑到上述事实。**

在DeepRacer的案例中，**可以为汽车识别出三个基于颜色的基本目标**：

1.  **保持在白色轨道边界内：** 越接近*白色*（255）的像素值越高或越浅，汽车就越容易将其解释为轨道边界，并会尽力保持在这个像素边界内。

1.  **在黑色轨道上行驶：** 更暗或较低的*黑色*（0）像素值将被解释为驾驶表面本身，汽车应该尽量在其上行驶。

1.  **绿色/黄色：** 尽管汽车会将绿色和黄色视为灰度阴影——它仍然可以学会（a）靠近*虚线黄色*中心线行驶；并且（b）避免进入*实心绿色*的禁区。

![](../Images/5db9aa456a6f381afcf84014dc1e7beb.png)

实际相机视图（左）和模拟视图（右）在RGB空间中的显示。这些图像在推理前被转换为灰度图像。来源²。

# DeepRacer的模拟与现实之间的表现差距

AWS DeepRacer使用强化学习（RL）¹在*模拟*环境中训练一个缩小版赛车，使其能够自动在赛道上行驶。这使得赛车可以首先在虚拟环境中*学习*一个最优且安全的策略或行为。然后，我们可以将模型部署到真实的汽车上，并在实际赛道上进行比赛。

不幸的是，现实世界中的性能往往无法与模拟器中观察到的完全相同。这是因为模拟器无法准确捕捉到现实世界的所有细节。值得称赞的是，AWS提供了一个关于[优化训练以缩小模拟与现实差距](https://docs.aws.amazon.com/deepracer/latest/developerguide/deepracer-console-train-evaluate-models.html#deepracer-evaluate-model-test-approaches)的指南。虽然这里提供的建议很有用，但对我而言并不完全奏效。汽车自带了AWS提供的内置模型，理论上应该适用于多个赛道，并且开箱即用。不幸的是，至少在我的实验中，这个模型甚至无法完成一圈（尽管我做了多次硬件调整）。AWS的指南中缺少了一些信息，最终我通过在线博客和讨论论坛才将其拼凑出来。

通过我的实验，识别出以下几个关键因素，导致了*模拟与现实差距*的增大：

1.  **相机光线/噪声敏感度：** 最大的挑战是相机对光线和/或背景噪声的敏感度。任何光斑都会使相机传感器过曝，导致汽车出现意外行为。尽量减少周围光线和任何背景干扰。（稍后会详细讨论）

1.  **摩擦力：** 汽车轮子与赛道之间的摩擦力为校准油门带来了挑战。我们通过AWS的[商店购买了推荐的赛道](https://www.amazon.com/Speedway-Printed-Track-DeepRacer-Matte/dp/B0BT8CGKTP/ref=s9_acsd_al_ot_c2_x_0_t?_encoding=UTF8&pf_rd_m=ATVPDKIKX0DER&pf_rd_s=merchandised-search-2&pf_rd_r=58S30ZXVWXMZH7T5DBP2&pf_rd_p=904aa3c6-ee70-4b7c-9984-72e3ba23e425&pf_rd_t=&pf_rd_i=32957528011)（继续阅读，了解为什么我**不推荐**这款赛道）。这款赛道是哑光乙烯基材料，在我的设置中，我将它放置在办公室的餐厅区域地毯上。看起来，乙烯基材料与地毯的组合产生了较高的静摩擦力，导致汽车在缓慢转弯或尝试从静止起步时不断卡住。

1.  **虚拟车与真实车的传感能力差异：** 真实车与模拟车在输入参数/状态空间上存在差距。AWS提供了[输入参数列表](https://docs.aws.amazon.com/deepracer/latest/developerguide/deepracer-reward-function-input.html)，但像赛道长度、进度、步骤等参数仅在模拟中可用，无法用于真实车。据我所知，通过一些网络搜索——似乎***车只能访问来自摄像头传感器的信息***。有很小的可能性，像*车的x, y位置和航向*这样的参数是已知的。我的研究表明这些信息是***无法获得的***，因为车大概率没有IMU，即使有——基于IMU的定位是一个非常难以解决的问题。这些信息对于设计正确的奖励函数非常有帮助（未来的部分将详细介绍）。

# 赛道——自建与购买

如前所述，我购买了AWS推荐的[A To Z Speedway赛道](https://www.amazon.com/Speedway-Printed-Track-DeepRacer-Matte/dp/B0BT8CGKTP)。这条赛道是[意大利蒙扎的Autodroma Nazionale Monza F1赛道](https://www.formula1.com/en/information/italy-autodromo-nazionale-monza.FiJN1jnQlRLeHqOxIt13m)的简化版。

## 赛道评审——不要购买

![](../Images/348dde094c2e4150f6a2ecb9134ebb7c.png)

这条赛道质量极差，我不建议购买。表面非常起皱、薄弱，并且反光非常强烈。图像来源：作者。

就个人而言，我**不**推荐购买[这条赛道](https://www.amazon.com/Speedway-Printed-Track-DeepRacer-Matte/dp/B0BT8CGKTP)。它的价格为760美元加税（这辆车[售价](https://www.amazon.com/AWS-DeepRacer-Fully-autonomous-developers/dp/B07JMHRKQG/ref=sr_1_1?crid=30BVZW4UKAM5S&dib=eyJ2IjoiMSJ9.qyF0JnH5YYnFSRDVg9ViSV45_uIjGjxt_KK8SfhNITtHJx6iNeg9BmxLpl7RZDh_YlRRiO1WbcFsOUge4YuMZEfD9MCdRIngz8Fmo-y128P3RdHAB0lwDsvtnI7kvO4NzSeu_wM9G0qMdD-cQnvItTchdk-JthTNJKaT9d82PcJGU9AvZKtPLFcI6qaPcklH7HaTOp-1kImXR963CHm5gxGJaZP8bfKLSpZmVo665HS8hcmbkCzTdDUJSoQc16gmE8Bpd8SQyQR3c1Zwdf3oQgA82jI8lfXiNajo-mE7mIg.Fvk9UaaxYngFN1wsp6d2RIHNYm9dRrlGIH7JG_AzdV0&dib_tag=se&keywords=aws+deepracer&qid=1724204524&sprefix=aws+deep%2Caps%2C140&sr=8-1)几乎是它的一半)，总的来说，令人有些失望。

1.  **反光表面：** 哑光乙烯基印刷的质量很差且高度反光。任何环境光都会使摄像头图像过曝，导致碰撞和其他意外行为。

1.  **褶皱：** 赛道有很多褶皱，这会导致汽车卡住。你可以通过将赛道放在阳光下几天来在一定程度上解决这个问题。我在这方面的成功有限。你也可以使用蒸汽熨斗[(参考这篇指南)](https://www.signs.com/blog/how-to-remove-wrinkles-from-vinyl-banners-signage-101/#:~:text=Use%20a%20heat%20gun%2C%20handheld%20steamer%2C%20or%20hairdryer%20on%20low,the%20creases%20on%20the%20banner.)。我没有尝试过这个方法，所以请自行承担风险。

1.  **尺寸：** 这其实不是赛道本身的问题，但赛道的尺寸是18' x 27'，对于我的房子来说太大了。它甚至无法进入我的双车库。幸运的是，我的办公室同事很友好，允许我使用午餐室。它也非常笨重，折叠和携带非常困难。

总体而言，我对这款赛道的质量并不感到满意，只会在时间紧迫或不想自己动手制作的情况下推荐购买。

## 自己动手建造赛道（如果可能的话）

如果可以，试着自己动手构建一个。这里有一份来自AWS的[官方指南](https://docs.aws.amazon.com/deepracer/latest/developerguide/deepracer-build-your-track.html)，以及[来自Medium的另一份指南](https://medium.com/@autonomousracecarclub/guide-to-creating-a-full-re-invent-2018-deepracer-track-in-7-steps-979aff28a6f5)，用户[@autonomousracecarclu](http://twitter.com/autonomousracecarclu)发布的这份指南看起来更有前景。

使用互锁泡沫垫构建赛道或许是最好的方法。这可以解决乙烯基赛道的反射性和摩擦问题。此外，这些垫子重量轻，易于堆叠；因此，移动和存储它们更为便捷。

你也可以在FedEx打印赛道图纸，并将其粘贴在橡胶或混凝土表面上。无论是自己建造还是打印赛道，这些方法*比购买AWS推荐的赛道要好*（无论是从经济角度还是性能角度）。

# 准备你的空间——照明与干扰

记住，汽车只使用**黑白图像**来*理解和导航*它周围的环境。它无法*识别*物体——它只是学会避免或紧贴不同的灰度（从黑到白）。保持在黑色赛道上，避免白色边界和绿色禁区。

以下部分概述了为确保你的车能够顺利绕过赛道并减少碰撞，推荐的物理设置。

![](../Images/be6cd908be0851c97f4b88d2391a6ccf.png)

赛道准备步骤 - (a) 我通过拉下所有窗帘并关闭天花板灯来减少环境光。有几盏灯无法关闭，因为它们总是保持开启以应对紧急情况。 (b) 障碍物有助于减少背景干扰和反射。彩色障碍物比黑色的效果更好。绿色障碍物最为有效。我没有足够的绿色障碍物，所以我将它们放在更难的弯道周围。图像由作者提供。

## 最小化环境光

尽量减少环境光照。这包括来自窗户和天花板灯的任何自然光。当然，你需要一些光线让相机能够看到，但光线越少越好。

如果无法减少照明，尽量让它尽可能均匀。光点比光本身带来的问题更多。如果你的轨道像我之前那样有褶皱，光点会更频繁，从而导致更多的失败。

## 彩色拼接障碍物

障碍物的颜色和摆放位置都至关重要。可能比我最初预想的还要重要。人们可能认为它们是用来保护汽车免于撞击的。虽然这是其中的一部分，但障碍物更有用的是减少背景的干扰。

我使用了这些[Costco的2x2英尺拼接垫](https://www.samsclub.com/p/reversible-foam-mats-8-pack/prod3310384)。AWS推荐使用至少[2.5x2.5英尺且*任何颜色但不包括白色*](https://docs.aws.amazon.com/deepracer/latest/developerguide/deepracer-build-your-track-construction.html#deepracer-build-your-track-construction-dimensions)*.* 我意识到甚至*黑色*也会干扰汽车的表现。所以我建议使用多彩的拼接垫。

最好的选择是**绿色的**，因为汽车在模拟中学会避免绿色。尽管训练和推理图像是灰度的，使用绿色的障碍物效果更好。我使用了不同颜色的障碍物，因此我把绿色障碍物放在车容易偏离轨道的弯道上。

记得之前提到的——这辆车只使用**黑白图像**来理解周围的环境。它并不*识别*周围的物体——而是学会避免或坚持使用不同的灰度（从黑到白）。

# 接下来做什么？

在未来的文章中，我将专注于模型训练技巧和车辆校准。

# 致谢

特别感谢[Wes Strait](https://www.linkedin.com/in/wesstrait/)分享他的最佳实践和关于减少Sim2Real差距的详细笔记。[Abhishek Roy](https://www.linkedin.com/in/abhishekkroy/)和[Kyle Stahl](https://medium.com/u/7d0b0a351a27?source=post_page---user_mention--580fb1244229--------------------------------)帮助进行实验、记录和调试不同的车辆行为。最后，感谢[嘉吉](https://www.cargill.com)研发团队让我多次使用他们的午餐空间来进行汽车和轨道的实验。

# 参考文献

[1] Sutton, Richard S. “强化学习：导论。” *A Bradford Book*（2018）。

[2] Balaji, Bharathan 等. “Deepracer：用于Sim2Real强化学习实验的教育性自动驾驶平台。” *arXiv预印本 arXiv:1911.01562*（2019）。
