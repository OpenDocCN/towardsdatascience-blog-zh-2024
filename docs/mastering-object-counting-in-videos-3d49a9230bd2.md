# 视频中的物体计数

> 原文：[https://towardsdatascience.com/mastering-object-counting-in-videos-3d49a9230bd2?source=collection_archive---------3-----------------------#2024-06-25](https://towardsdatascience.com/mastering-object-counting-in-videos-3d49a9230bd2?source=collection_archive---------3-----------------------#2024-06-25)

## 按步骤指导如何使用检测和追踪技术计数树上行走的蚂蚁。

[](https://medium.com/@lihigurarie?source=post_page---byline--3d49a9230bd2--------------------------------)[![Lihi Gur Arie, 博士](../Images/7a1eb30725a95159401c3672fa5f43ab.png)](https://medium.com/@lihigurarie?source=post_page---byline--3d49a9230bd2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3d49a9230bd2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3d49a9230bd2--------------------------------) [Lihi Gur Arie, 博士](https://medium.com/@lihigurarie?source=post_page---byline--3d49a9230bd2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3d49a9230bd2--------------------------------) ·阅读时间7分钟·2024年6月25日

--

视频中的蚂蚁计数。左上角显示进入和退出的计数。每只蚂蚁被分配一个独特的ID和颜色。标签由作者提供，原始视频由 [Lui Lo Franco在Pexels](https://www.pexels.com/video/ants-carrying-leaves-on-tree-trunk-9888614/) 提供。

# **介绍**

在视频中计数物体是一个具有挑战性的计算机视觉任务。与静态图像中的物体计数不同，视频包含更多的复杂性，因为物体可以移动、被遮挡或在不同时间出现和消失，这使得计数过程更加复杂。

在本教程中，我们将演示如何使用物体检测和追踪技术计数沿树木移动的蚂蚁。我们将利用Ultralytics平台集成YOLOv8模型进行检测，使用BoT-SORT进行追踪，并通过计数线来计算蚂蚁的数量。

> 如果你没有付费的Medium账户，可以在[这里](/mastering-object-counting-in-videos-3d49a9230bd2?sk=8ec6a61e5dc66ec0ebc762ba01b6af73)免费阅读。

# 流程概述

在典型的视频物体计数流程中，每一帧会经历一系列的处理步骤：检测、追踪和计数。以下是每个步骤的简要概述：

1.  **检测：**一个物体检测器在每一帧中识别并定位物体，生成围绕物体的边界框。

1.  **追踪：**追踪器在每一帧之间跟踪这些物体，给每个物体分配唯一的ID，确保它们只被计数一次。

1.  **计数：**计数模块汇总这些信息，并将每个新物体加入，以提供准确的结果。

![](../Images/020922d351e2affc484485704545edb2.png)

图片由作者提供

连接物体检测器、追踪器和计数器可能需要大量编码。幸运的是，Ultralytics库[1]通过提供一个便捷的流水线来简化这一过程，能够无缝地集成这些组件。

# 1\. 使用YOLOv8检测物体

第一步是在每一帧中检测蚂蚁，并为它们生成边界框。在本教程中，我们将使用我提前训练的YOLOv8检测器来检测蚂蚁。我使用了[Grounding DINO](/automatic-labeling-of-object-detection-datasets-using-groundingdino-b66c486656fe?sk=7c98df89b60ea49a6de9efd5278f645e) [2]来标注数据，然后使用这些标注数据训练YOLOv8模型。如果你想了解更多关于训练YOLO模型的信息，可以参考我之前的文章[训练YOLOv5](/the-practical-guide-for-object-detection-with-yolov5-algorithm-74c04aac4843?sk=00d2a9d6dd84d6ac4de153cab3dba7c0)，因为概念是相似的。对于你的应用，你可以使用一个预训练模型或自己训练一个自定义模型。

要开始使用，我们需要用预训练的权重初始化检测器：

```py
from ultralytics import YOLO

# Initialize YOLOv8 model with pre-trained weights
model = YOLO("/path/to/your/yolo_model.pt")
```

接下来，我们将使用检测器在视频循环中的每一帧中检测蚂蚁，并将检测与追踪过程相结合。

# 2\. 使用BoT-SORT追踪物体

由于蚂蚁在视频帧中出现多次，因此必须追踪每只蚂蚁并分配一个唯一的ID，以确保每只蚂蚁只被计数一次。Ultralytics支持BoT-SORT [3]和ByteTrack [4]进行追踪。

+   **ByteTrack：** 提供了精度和速度之间的平衡，并且具有较低的计算复杂度。它可能无法像BoT-SORT那样处理遮挡和摄像头运动。

+   **BoT-SORT：** 在追踪准确性和鲁棒性方面优于ByteTrack，尤其是在有遮挡和摄像头运动的挑战性场景中。然而，它的代价是更高的计算复杂度和较低的帧率。

这些算法的选择取决于你应用的具体需求。

**BoT-SORT的工作原理：** BoT-SORT是一个多物体追踪器，意味着它可以同时追踪多个物体。它结合了运动和外观信息，并进行了摄像头运动补偿。物体的位置通过卡尔曼滤波器进行预测，现有轨迹的匹配基于物体的位置和视觉特征。这种方法使BoT-SORT即使在有遮挡或摄像头运动的情况下，也能保持准确的轨迹。

一个配置良好的追踪器可以弥补检测器的轻微故障。例如，如果物体检测器暂时未能检测到一只蚂蚁，追踪器可以通过运动和外观线索保持蚂蚁的轨迹。

检测器和追踪器在视频循环中的每一帧上都被迭代使用，以生成轨迹。这是将其集成到你的视频处理循环中的方法：

```py
tracks = model.track(frame, persist=True, tracker=’botsort.yaml’, iou=0.2)
```

跟踪器配置在‘botsort.yaml’文件中定义。你可以调整这些参数以最好地适应你的需求。要将跟踪器更改为ByteTrack，只需将‘bytetrack.yaml’传递给跟踪器参数。

确保交并比（IoU）值符合你的应用需求；IoU阈值（用于非最大抑制）决定了检测结果必须多接近才被视为同一物体。`persist=True`参数告诉跟踪器当前帧是序列的一部分，并期望上一帧的轨迹在当前帧中持续存在。

# 3\. 计数物体

现在我们已经检测并跟踪了蚂蚁，最后一步是统计在视频中穿过指定线的唯一蚂蚁。来自Ultralytics库的`ObjectCounter`类允许我们定义一个计数区域，该区域可以是线条或多边形。在本教程中，我们将使用一条简单的线作为计数区域。这种方法通过确保每只蚂蚁在穿过线时只被计数一次，减少了错误，即使由于跟踪错误它的唯一ID发生了变化。

首先，我们在视频循环之前初始化`ObjectCounter`：

```py
counter = solutions.ObjectCounter( 
  view_img=True,                     # Display the image during processing 
  reg_pts=[(512, 320), (512, 1850)], # Region of interest points 
  classes_names=model.names,         # Class names from the YOLO model 
  draw_tracks=True,                  # Draw tracking lines for objects 
  line_thickness=2,                  # Thickness of the lines drawn 
  )
```

在视频循环内部，`ObjectCounter`将计数由跟踪器生成的轨迹。线的端点作为`reg_pts`参数传递给计数器，格式为[(x1, y1), (x2, y2)]。当一只蚂蚁的边界框的中心点第一次穿过线时，它将根据其轨迹方向加入计数。向某一方向移动的物体被计为‘In’，而向另一方向移动的物体则计为‘Out’。

```py
 # Use the Object Counter to count new objects 
  frame = counter.start_counting(frame, tracks)
```

# 完整代码

现在我们已经看到了计数组件，让我们将代码与视频循环集成并保存结果视频。

```py
# Install and import Required Libraries
%pip install ultralytics
import cv2
from ultralytics import YOLO, solutions

# Define paths:
path_input_video = '/path/to/your/input_video.mp4' 
path_output_video = "/path/to/your/output_video.avi"
path_model = "/path/to/your/yolo_model.pt"

# Initialize YOLOv8 Detection Model
model = YOLO(path_model)

# Initialize Object Counter
counter = solutions.ObjectCounter( 
  view_img=True,                     # Display the image during processing 
  reg_pts=[(512, 320), (512, 1850)], # Region of interest points 
  classes_names=model.names,         # Class names from the YOLO model 
  draw_tracks=True,                  # Draw tracking lines for objects 
  line_thickness=2,                  # Thickness of the lines drawn 
  )

# Open the Video File
cap = cv2.VideoCapture(path_input_video) 
assert cap.isOpened(), "Error reading video file"

# Initialize the Video Writer to save resulted video
video_writer = cv2.VideoWriter(path_output_video, cv2.VideoWriter_fourcc(*"mp4v"), 30, (1080, 1920))

# itterate over video frames:
frame_count = 0 
while cap.isOpened(): 
  success, frame = cap.read() 
  if not success: 
    print("Video frame is empty or video processing has been successfully completed.") 
    break 

  # Perform object tracking on the current frame 
  tracks = model.track(frame, persist=True, tracker='botsort.yaml', iou=0.2) 

  # Use the Object Counter to count objects in the frame and get the annotated image 
  frame = counter.start_counting(frame, tracks) 

  # Write the annotated frame to the output video 
  video_writer.write(frame) 
  frame_count += 1

# Release all Resources:
cap.release() 
video_writer.release() 
cv2.destroyAllWindows()

# Print counting results:
print(f'In: {counter.in_counts}\nOut: {counter.out_counts}\nTotal: {counter.in_counts + counter.out_counts}')
print(f'Saves output video to {path_output_video}')
```

上述代码将物体检测和跟踪集成到视频处理循环中，以保存带注释的视频。使用OpenCV，我们打开输入视频并设置输出视频的写入器。在每一帧中，我们使用BoTSORT执行物体跟踪，统计物体数量并注释该帧。带注释的帧包括边界框、唯一ID、轨迹以及‘in’和‘out’计数，并保存到输出视频中。‘in’和‘out’的计数可以分别从`counter.in_counts`和`counter.out_counts`中获取，并且也会打印在输出视频中。

![](../Images/b7f437e548c99be9e341810585847d79.png)

带注释的帧。每只蚂蚁都分配了一个边界框和一个唯一ID。蚂蚁在越过粉红色线时会被计数。蚂蚁的‘in’和‘out’计数显示在图像的角落。

# 结论

在带注释的视频中，我们正确地统计了总共85只蚂蚁，其中34只进入，51只退出。为了准确计数，检测器需要表现良好，且跟踪器需要配置得当。配置良好的跟踪器能够弥补检测器的遗漏，确保跟踪的连续性。

在注释视频中，我们可以看到跟踪器很好地处理了缺失的检测，这一点可以通过一个蚂蚁周围的边界框消失以及它在后续帧中以正确的ID返回来证明。此外，将不同ID分配给同一物体的跟踪错误（例如，蚂蚁#42变成#48）并未影响计数，因为只有穿过线的蚂蚁才会被计数。

在本教程中，我们探讨了如何使用先进的物体检测和跟踪技术计算视频中的物体。我们使用YOLOv8进行蚂蚁检测，并采用BoT-SORT进行稳健的跟踪，所有技术都无缝集成在Ultralytics库中。

# 感谢阅读！

恭喜你已经完成了整个过程。点击👍以表示感谢并提升算法的自尊心🤓

**想了解更多？**

+   [**探索**](https://medium.com/@lihigurarie)我所写的其他文章

+   [**订阅**](https://medium.com/@lihigurarie/subscribe)以便在我发布文章时收到通知

+   在[**Linkedin**](https://www.linkedin.com/in/lihi-gur-arie/)上关注我

> 参考文献

[1] [Ultralytics GitHub](https://github.com/ultralytics/ultralytics)

[2] [Grounding DINO: 将DINO与基于领域的预训练相结合，用于开放集物体检测](https://arxiv.org/pdf/2303.05499)

[3] [BoT-SORT: 稳健的多行人跟踪](https://arxiv.org/pdf/2206.14651)

[4] [ByteTrack: 通过关联每个检测框进行多物体跟踪](https://arxiv.org/pdf/2110.06864)
