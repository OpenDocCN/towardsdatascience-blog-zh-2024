# Python中的动态可视化

> 原文：[https://towardsdatascience.com/animated-plotting-in-python-with-opencv-and-matplotlib-d640462c41f4?source=collection_archive---------3-----------------------#2024-11-21](https://towardsdatascience.com/animated-plotting-in-python-with-opencv-and-matplotlib-d640462c41f4?source=collection_archive---------3-----------------------#2024-11-21)

## 如何用OpenCV和Matplotlib制作动画图表

[](https://medium.com/@flip.flo.dev?source=post_page---byline--d640462c41f4--------------------------------)[![Florian Trautweiler](../Images/63aa57830a244986c400982f7b78d614.png)](https://medium.com/@flip.flo.dev?source=post_page---byline--d640462c41f4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d640462c41f4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d640462c41f4--------------------------------) [Florian Trautweiler](https://medium.com/@flip.flo.dev?source=post_page---byline--d640462c41f4--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d640462c41f4--------------------------------) ·8分钟阅读·2024年11月21日

--

![](../Images/5c206bd2b728441d5c595ce0f3b6404f.png)

追踪球体轨迹并实时动画显示其垂直位置

在**计算机视觉**中，一个基本目标是从静态图像或视频序列中提取有意义的信息。为了理解这些信号，通常可视化它们是非常有帮助的。

例如，当在高速公路上追踪单个汽车时，我们可以在它们周围画上边界框；或者在生产线上的传送带上检测到问题时，我们可以使用不同的颜色来标记异常。但如果提取的信息是**数值**性质的，并且你想要可视化该信号的**时间动态**呢？

仅将值作为数字显示在屏幕上可能无法提供足够的洞察，尤其是当信号快速变化时。在这些情况下，可视化信号的一种非常好的方法是使用带有**时间轴**的图表。在这篇文章中，我将展示如何结合**OpenCV**和**Matplotlib**的强大功能，创建这种信号的动态实时可视化。

我在这个项目中使用的代码和视频可以在GitHub上找到：

[](https://github.com/trflorian/ball-tracking-live-plot?source=post_page-----d640462c41f4--------------------------------) [## GitHub - trflorian/ball-tracking-live-plot: 使用OpenCV追踪球体并绘制…

### 使用OpenCV追踪球体并用Matplotlib绘制轨迹 - trflorian/ball-tracking-live-plot

[github.com](https://github.com/trflorian/ball-tracking-live-plot?source=post_page-----d640462c41f4--------------------------------)

# 绘制球体轨迹

让我们来探索一个简单的问题，我录制了一个球垂直投向空中的视频。目标是跟踪视频中的球，并绘制其**位置 *p(t)***、**速度 *v(t)***和**加速度 *a(t)***随时间的变化。

![](../Images/cbb6dde91a35cfe409e6e7077d25dfa5.png)

输入视频

我们将参考帧定义为摄像头，并为简单起见，我们仅追踪图像中球体的垂直位置。我们预计位置呈抛物线形状，速度呈线性减少，且加速度保持恒定。

![](../Images/e0854134188a624ac8728d8b27a37126.png)

我们预期的图形草图

## 球体分割

在第一步，我们需要识别视频序列中每一帧的球体。由于摄像头保持静止，检测球体的一个简单方法是使用背景减除模型，并结合颜色模型来去除帧中的手部。

首先，我们通过使用[**VideoCapture**](https://docs.opencv.org/4.x/dd/d43/tutorial_py_video_display.html)从**OpenCV**库播放视频片段，并用一个简单的循环显示视频。视频播放到结尾时，我们会重新启动视频。我们还确保以原始帧率回放视频，通过计算视频的FPS来确定**sleep_time**（以毫秒为单位）。最后，记得在结束时释放资源并关闭窗口。

```py
import cv2

cap = cv2.VideoCapture("ball.mp4")
fps = int(cap.get(cv2.CAP_PROP_FPS))

while True:
    ret, frame = cap.read()
    if not ret:
        cap.set(cv2.CAP_PROP_POS_FRAMES, 0)
        continue

    cv2.imshow("Frame", frame)

    sleep_time = 1000 // fps
    key = cv2.waitKey(sleep_time) & 0xFF
    if key & 0xFF == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()
```

![](../Images/de085d4139f222d448033a6c43aa09a5.png)

输入视频的可视化

首先，我们要提取一个用于球体的二值分割掩模。这实际上意味着我们希望创建一个掩模，该掩模对于球体的像素是激活的，而对于其他所有像素是非激活的。为此，我将结合两个掩模：一个是运动掩模，另一个是颜色掩模。运动掩模提取运动部分，而颜色掩模主要去除帧中的手部。

对于颜色滤波器，我们可以将图像转换到[**HSV**](https://en.wikipedia.org/wiki/HSL_and_HSV)颜色空间，并选择一个特定的色调范围（20–100），该范围包含球体的绿色颜色，而不包含皮肤色调。我不会对饱和度或亮度值进行过滤，因此我们可以使用完整的范围（0–255）。

```py
# filter based on color
hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
mask_color = cv2.inRange(hsv, (20, 0, 0), (100, 255, 255))
```

为了创建一个运动掩模，我们可以使用一个简单的[**背景减除**](https://en.wikipedia.org/wiki/Foreground_detection#Background_mixture_models)模型。我们使用视频的第一帧作为背景，并将**学习率设置为1**。在循环中，我们应用背景模型来获取前景掩模，但通过将**学习率设置为0**，不将新帧集成到模型中。

```py
...

# initialize background model
bg_sub = cv2.createBackgroundSubtractorMOG2(varThreshold=50, detectShadows=False)
ret, frame0 = cap.read()
if not ret:
    print("Error: cannot read video file")
    exit(1)
bg_sub.apply(frame0, learningRate=1.0)

while True:
  ...
  # filter based on motion
  mask_fg = bg_sub.apply(frame, learningRate=0)
```

在下一步中，我们可以将这两个掩模合并，并应用[**开运算形态学**](https://en.wikipedia.org/wiki/Opening_(morphology))来去除小的噪声，最终得到一个完美的球体分割。

```py
# combine both masks
mask = cv2.bitwise_and(mask_color, mask_fg)
mask = cv2.morphologyEx(
    mask, cv2.MORPH_OPEN, cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (13, 13))
)
```

![](../Images/9267b761f4ad6bc0b8b5c340245d198d.png)

**左上角：** 视频序列，**右上角：** 颜色掩模，**左下角：** 运动掩模，**右下角：** 组合掩模

## 跟踪球体

我们剩下的唯一部分是图像中的球体遮罩。为了跟踪球的中心，我首先提取球的轮廓，然后以其边界框的中心作为参考点。如果有噪声通过了我们的遮罩，我会根据大小过滤掉检测到的轮廓，只关注最大的一个。

```py
# find largest contour corresponding to the ball we want to track
contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
if len(contours) > 0:
    largest_contour = max(contours, key=cv2.contourArea)
    x, y, w, h = cv2.boundingRect(largest_contour)
    center = (x + w // 2, y + h // 2)
```

我们还可以在帧中添加一些注释来可视化我们的检测。我将画两个圆，一个表示球的中心，另一个表示球的边缘。

```py
cv2.circle(frame, center, 30, (255, 0, 0), 2)
cv2.circle(frame, center, 2, (255, 0, 0), 2)
```

为了跟踪球的位置，我们可以使用**列表**。每当我们检测到球时，我们只需将中心位置添加到列表中。我们还可以通过在跟踪位置列表中的每个片段之间绘制线条来可视化轨迹。

```py
tracked_pos = []

while True:
  ...

  if len(contours) > 0:
    ... 
    tracked_pos.append(center)

  # draw trajectory
  for i in range(1, len(tracked_pos)):
      cv2.line(frame, tracked_pos[i - 1], tracked_pos[i], (255, 0, 0), 1)
```

![](../Images/f650435618b04b8ca29fd3cd5b1b6325.png)

球的轨迹可视化

## 创建图表

现在我们可以跟踪球的位置，接下来让我们探索如何使用**matplotlib**绘制信号。第一步，我们可以先在视频结束时创建最终的图表，然后在第二步再考虑如何实时动画化它。为了显示位置、速度和加速度，我们可以使用三个水平排列的子图：

```py
fig, axs = plt.subplots(nrows=1, ncols=3, figsize=(10, 2), dpi=100)

axs[0].set_title("Position")
axs[0].set_ylim(0, 700)
axs[1].set_title("Velocity")
axs[1].set_ylim(-200, 200)
axs[2].set_title("Acceleration")
axs[2].set_ylim(-30, 10)

for ax in axs:
    ax.set_xlim(0, 20)
    ax.grid(True)
```

我们只对图像中的 y 位置（数组索引 1）感兴趣，若要得到一个零偏移位置图，我们可以减去第一个位置。

```py
pos0 = tracked_pos[0][1]
pos = np.array([pos0 - pos[1] for pos in tracked_pos])
```

对于速度，我们可以使用位置差异作为近似值，对于加速度，我们可以使用速度差异。

```py
vel = np.diff(pos)
acc = np.diff(vel)
```

现在我们可以绘制这三个值：

```py
axs[0].plot(range(len(pos)), pos, c="b")
axs[1].plot(range(len(vel)), vel, c="b")
axs[2].plot(range(len(acc)), acc, c="b")

plt.show()
```

![](../Images/1d470d2c80763c501bdbc00c9af25a39.png)

位置、速度和加速度的静态图

## 动画化图表

接下来是有趣的部分，我们希望使这个图表动态化！由于我们在 OpenCV 的 GUI 循环中工作，我们不能直接使用**matplotlib**的**show**函数，因为这会阻塞循环，导致程序无法运行。相反，我们需要利用一些技巧 ✨

主要思想是将图表绘制到内存中的缓冲区，然后在 OpenCV 窗口中显示这个缓冲区。通过手动调用画布的绘制函数，我们可以强制将图形渲染到缓冲区中。然后，我们可以获取这个缓冲区并将其转换为数组。由于缓冲区是**RGB**格式，但 OpenCV 使用的是**BGR**格式，因此我们需要转换颜色顺序。

```py
fig.canvas.draw()

buf = fig.canvas.buffer_rgba()
plot = np.asarray(buf)
plot = cv2.cvtColor(plot, cv2.COLOR_RGB2BGR)
```

确保**axs.plot**调用现在位于帧循环内部：

```py
while True:
  ...

  axs[0].plot(range(len(pos)), pos, c="b")
  axs[1].plot(range(len(vel)), vel, c="b")
  axs[2].plot(range(len(acc)), acc, c="b")

  ... 
```

现在我们可以使用 OpenCV 的**imshow**函数简单地显示图表。

```py
cv2.imshow("Plot", plot)
```

![](../Images/4587f731ece8dfd1948f4c91e8287b31.png)

动画图表

然后，瞧，您得到了动画图！然而，您会注意到性能相当低下。每帧重新绘制整个图形非常耗费资源。为了提高性能，我们需要使用[**blitting**](https://matplotlib.org/stable/users/explain/animations/blitting.html)。这是一种高级渲染技术，它将图形的静态部分绘制到背景图像中，只重新绘制变化的前景元素。为了设置这一点，我们需要在帧循环之前，首先定义我们三个图形的引用。

```py
pl_pos = axs[0].plot([], [], c="b")[0]
pl_vel = axs[1].plot([], [], c="b")[0]
pl_acc = axs[2].plot([], [], c="b")[0]
```

然后我们需要在循环之前绘制一次图形的背景，并获取每个坐标轴的背景。

```py
fig.canvas.draw()
bg_axs = [fig.canvas.copy_from_bbox(ax.bbox) for ax in axs]
```

在循环中，我们现在可以改变每个图形的数据，然后对于每个子图，我们需要恢复区域的背景，绘制新的图形，并调用**blit**函数来应用更改。

```py
# Update plot data
pl_pos.set_data(range(len(pos)), pos)
pl_vel.set_data(range(len(vel)), vel)
pl_acc.set_data(range(len(acc)), acc)

# Blit Pos
fig.canvas.restore_region(bg_axs[0])
axs[0].draw_artist(pl_pos)
fig.canvas.blit(axs[0].bbox)

# Blit Vel
fig.canvas.restore_region(bg_axs[1])
axs[1].draw_artist(pl_vel)
fig.canvas.blit(axs[1].bbox)

# Blit Acc
fig.canvas.restore_region(bg_axs[2])
axs[2].draw_artist(pl_acc)
fig.canvas.blit(axs[2].bbox)
```

就这样，绘图速度加快，性能得到了显著提升。

![](../Images/e9a892839ffccb8b9c85b6df8e94d300.png)

优化后的图形

# 结论

在这篇文章中，您学会了如何应用简单的计算机视觉技术提取一个移动的前景物体并追踪它的轨迹。接着，我们使用**matplotlib**和**OpenCV**创建了一个动画图。这个绘图演示是通过一个玩具示例视频实现的，视频中一个球被垂直抛向空中。然而，这个项目中使用的工具和技术对于各种任务和实际应用都非常有用！完整的源代码可以在我的GitHub上找到。希望今天您有所收获，祝编码愉快，保重！

[](https://github.com/trflorian/ball-tracking-live-plot?source=post_page-----d640462c41f4--------------------------------) [## GitHub - trflorian/ball-tracking-live-plot: 使用OpenCV追踪一个球并绘制轨迹…

### 使用OpenCV追踪一个球并使用Matplotlib绘制轨迹 - trflorian/ball-tracking-live-plot

github.com](https://github.com/trflorian/ball-tracking-live-plot?source=post_page-----d640462c41f4--------------------------------)

*本文中的所有可视化图表均由作者创建。*
