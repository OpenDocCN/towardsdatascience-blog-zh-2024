# 使用 MediaPipe 进行实时手部追踪和手势识别：Rerun 展示

> 原文：[https://towardsdatascience.com/real-time-hand-tracking-and-gesture-recognition-with-mediapipe-rerun-showcase-9ec57cb0c831?source=collection_archive---------4-----------------------#2024-03-05](https://towardsdatascience.com/real-time-hand-tracking-and-gesture-recognition-with-mediapipe-rerun-showcase-9ec57cb0c831?source=collection_archive---------4-----------------------#2024-03-05)

## 如何使用 Rerun 可视化 MediaPipe 的手部追踪与手势识别

[](https://andreasnaoum.medium.com/?source=post_page---byline--9ec57cb0c831--------------------------------)[![Andreas Naoum](../Images/e14d545f270170877e0af31572275e17.png)](https://andreasnaoum.medium.com/?source=post_page---byline--9ec57cb0c831--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9ec57cb0c831--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9ec57cb0c831--------------------------------) [Andreas Naoum](https://andreasnaoum.medium.com/?source=post_page---byline--9ec57cb0c831--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9ec57cb0c831--------------------------------) ·8 分钟阅读·2024年3月5日

--

![](../Images/8ec748453f3b800b051414e9d6c7ca7b.png)

手部追踪与手势识别 | 作者提供的图片

在这篇文章中，我将展示一个使用 [**MediaPipe**](https://mediapipe-studio.webapps.google.com/home) **Python** **和** [**Rerun SDK**](https://www.rerun.io) 进行的**手部追踪和手势识别**示例。

如果你有兴趣深入了解并扩展你的理解，我将指导你**如何安装** [**MediaPipe**](https://mediapipe-studio.webapps.google.com/home) **Python** **和** [**Rerun SDK**](https://www.rerun.io)，以便进行手部追踪、识别不同手势并可视化数据。

## 因此，你将学习：

+   如何安装 MediaPipe Python 和 Rerun

+   如何使用 [MediaPipe 手势识别](https://developers.google.com/mediapipe/solutions/vision/gesture_recognizer#models) 进行手部追踪与手势识别

+   如何在 [Rerun Viewer](https://www.rerun.io/docs/getting-started/viewer-walkthrough) 中可视化手部追踪和手势识别的结果

如果你迫不及待想尝试这个示例，可以直接使用提供的代码：

```py
# Clone the rerun GitHub repository to your local machine.
git clone https://github.com/rerun-io/rerun

# Navigate to the rerun repository directory.
cd rerun

# Install the required Python packages specified in the requirements file
pip install -r examples/python/gesture_detection/requirements.txt

# Run the main Python script for the example
python examples/python/gesture_detection/main.py

# Run the main Python script for a specific image
python examples/python/gesture_detection/main.py --image path/to/your/image.jpg

# Run the main Python script for a specific video
python examples/python/gesture_detection/main.py --video path/to/your/video.mp4

# Run the main Python script with camera stream
python examples/python/gesture_detection/main.py --camera
```

# 手部追踪和手势识别技术

在我们继续之前，先对使这一切成为可能的技术给予应有的认可。手部追踪和手势识别技术旨在赋予设备解读手部动作和手势作为指令或输入的能力。在这项技术的核心，一种预训练的机器学习模型分析视觉输入并识别手部地标和手势。这项技术的实际应用范围广泛，手部动作和手势可用于控制智能设备。人机交互、机器人学、游戏和增强现实是这项技术潜在应用最有前景的一些领域。

然而，我们在使用此类技术时应时刻保持警觉。因为模型可能会误解手势，且假阳性或假阴性的可能性不容忽视，所以在敏感和关键系统中使用它是非常具有挑战性的。利用这项技术还可能带来伦理和法律方面的问题，因为用户可能不希望在公共场所被记录他们的手势。如果你打算在现实世界的场景中实施这项技术，考虑任何伦理和法律问题是非常重要的。

# 前提条件与设置

首先，你需要安装必要的库，包括 OpenCV、MediaPipe 和 Rerun。[**MediaPipe Python**](https://mediapipe-studio.webapps.google.com/home) 是一个方便的工具，供开发者在计算机视觉和机器学习领域集成设备端 ML 解决方案，[**Rerun**](https://www.rerun.io) 是一个用于可视化随时间变化的多模态数据的 SDK。

```py
# Install the required Python packages specified in the requirements file
pip install -r examples/python/gesture_detection/requirements.txt
```

然后，你需要从此处下载预定义模型：[HandGestureClassifier](https://storage.googleapis.com/mediapipe-models/gesture_recognizer/gesture_recognizer/float16/latest/gesture_recognizer.task)

# 使用 MediaPipe 进行手部追踪和手势识别

![](../Images/47ff9b3771ef5e9e98811c796da311bf.png)

图片来自 [手势识别任务指南](https://developers.google.com/mediapipe/solutions/vision/gesture_recognizer) 由 [Google](https://about.google/brand-resource-center/) 提供

> “MediaPipe 手势识别任务允许你实时识别手势，并提供识别到的手势结果以及检测到的手的地标。你可以使用此任务识别用户的特定手势，并调用与这些手势对应的应用功能。” 引自 [手势识别任务指南](https://developers.google.com/mediapipe/solutions/vision/gesture_recognizer)

现在，让我们尝试使用 MediaPipe 预训练模型进行手势识别，处理一张示例图像。总体而言，以下代码为初始化和配置 MediaPipe 手势识别解决方案奠定了基础。

```py
from mediapipe.tasks.python import vision
from mediapipe.tasks import python

class GestureDetectorLogger:

    def __init__(self, video_mode: bool = False):
        self._video_mode = video_mode

        base_options = python.BaseOptions(
            model_asset_path='gesture_recognizer.task'
        )
        options = vision.GestureRecognizerOptions(
            base_options=base_options,
            running_mode=mp.tasks.vision.RunningMode.VIDEO if self._video_mode else mp.tasks.vision.RunningMode.IMAGE
        )
        self.recognizer = vision.GestureRecognizer.create_from_options(options)

    def detect(self, image: npt.NDArray[np.uint8]) -> None:
          image = mp.Image(image_format=mp.ImageFormat.SRGB, data=image)

          # Get results from Gesture Detection model
          recognition_result = self.recognizer.recognize(image)

          for i, gesture in enumerate(recognition_result.gestures):
              # Get the top gesture from the recognition result
              print("Top Gesture Result: ", gesture[0].category_name)

          if recognition_result.hand_landmarks:
              # Obtain hand landmarks from MediaPipe
              hand_landmarks = recognition_result.hand_landmarks
              print("Hand Landmarks: " + str(hand_landmarks))

              # Obtain hand connections from MediaPipe
              mp_hands_connections = mp.solutions.hands.HAND_CONNECTIONS
              print("Hand Connections: " + str(mp_hands_connections))
```

`GestureDetectorLogger`类中的`detect`函数接受一张图片作为参数，并打印模型结果，突出显示识别出的手势及检测到的手部地标。有关模型的更多细节，请参考其[模型卡片](https://storage.googleapis.com/mediapipe-assets/gesture_recognizer/model_card_hand_gesture_classification_with_faireness_2022.pdf)。

![](../Images/af87cc0b5c9dacee7900b4dab9433aee.png)

图片来源：[手势识别任务指南](https://developers.google.com/mediapipe/solutions/vision/gesture_recognizer) 由[Google](https://about.google/brand-resource-center/)提供

你可以使用以下代码亲自尝试：

```py
def run_from_sample_image(path)-> None:
    image = cv2.imread(str(path))
    show_image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
    logger = GestureDetectorLogger(video_mode=False)
    logger.detect_and_log(show_image)

# Run the gesture recognition on a sample image
run_from_sample_image(SAMPLE_IMAGE_PATH)
```

# 使用Rerun进行验证、调试和演示

这一步骤帮助你确保解决方案的可靠性和有效性。现在模型已经准备好，接下来可视化结果以验证准确性，调试潜在问题，并展示其功能。使用Rerun SDK，可以简单快速地实现结果的可视化。

## 我们如何使用Rerun？

![](../Images/dd9474d4f31555cc7fc2c9f655597e18.png)

图片来源：[Rerun文档](https://www.rerun.io/docs) 由[Rerun](https://www.rerun.io)提供

1.  通过使用Rerun SDK记录数据，将多模态数据从代码流式传输

1.  可视化并与**实时或录制的流**进行交互，无论是本地的还是远程的

1.  互动式构建布局并自定义可视化

1.  在需要时扩展Rerun

在进入代码之前，你应该访问页面[安装Rerun查看器](https://www.rerun.io/docs/getting-started/installing-viewer)来安装查看器。然后，我强烈建议通过阅读以下指南来熟悉Rerun SDK：[Python快速入门](https://www.rerun.io/docs/getting-started/python)和[在Python中记录数据](https://www.rerun.io/docs/getting-started/logging-python)。这些初始步骤将确保顺利设置，并帮助你开始接下来的代码实现。

## 从视频或实时运行

对于视频流，使用了OpenCV。你可以选择特定视频的文件路径，或者通过提供参数0或1来访问你自己的摄像头（使用0为默认摄像头；在Mac上，你可以使用1）。

值得注意的是，要强调[时间轴](https://www.rerun.io/docs/concepts/timelines)的介绍。Rerun时间轴的功能使得可以将数据与一个或多个时间轴关联。因此，视频的每一帧都与其对应的时间戳相关联。

```py
def run_from_video_capture(vid: int | str, max_frame_count: int | None) -> None:
    """
    Run the detector on a video stream.

    Parameters
    ----------
    vid:
        The video stream to run the detector on. Use 0/1 for the default camera or a path to a video file.
    max_frame_count:
        The maximum number of frames to process. If None, process all frames.
    """
    cap = cv2.VideoCapture(vid)
    fps = cap.get(cv2.CAP_PROP_FPS)

    detector = GestureDetectorLogger(video_mode=True)

    try:
        it: Iterable[int] = itertools.count() if max_frame_count is None else range(max_frame_count)

        for frame_idx in tqdm.tqdm(it, desc="Processing frames"):
            ret, frame = cap.read()
            if not ret:
                break

            if np.all(frame == 0):
                continue

            frame_time_nano = int(cap.get(cv2.CAP_PROP_POS_MSEC) * 1e6)
            if frame_time_nano == 0:
                frame_time_nano = int(frame_idx * 1000 / fps * 1e6)

            frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

            rr.set_time_sequence("frame_nr", frame_idx)
            rr.set_time_nanos("frame_time", frame_time_nano)
            detector.detect_and_log(frame, frame_time_nano)
            rr.log(
                "Media/Video",
                rr.Image(frame)
            )

    except KeyboardInterrupt:
        pass

    cap.release()
    cv2.destroyAllWindows()
```

## 为可视化记录数据

![](../Images/669757c874516e02624cf43b52a69a60.png)

使用Rerun SDK记录2D数据 | 图片来源：作者

要在 Rerun 查看器中可视化数据，必须使用 Rerun SDK 记录数据。前面提到的指南提供了关于这一过程的深入见解。在这个上下文中，我们提取手部地标点作为归一化值，然后利用图像的宽度和高度将其转换为图像坐标。这些坐标随后作为[2D 点](https://www.rerun.io/docs/reference/types/archetypes/points2d)记录到 Rerun SDK 中。此外，我们识别地标之间的连接，并将其作为[2D 线段](https://www.rerun.io/docs/reference/types/archetypes/line_strips2d)记录。

对于手势识别，结果会打印到控制台。然而，在源代码中，你可以探索一种方法，通过[TextDocument](https://www.rerun.io/docs/reference/types/archetypes/text_document)和表情符号将这些结果呈现给查看器。

```py
class GestureDetectorLogger:

    def detect_and_log(self, image: npt.NDArray[np.uint8], frame_time_nano: int | None) -> None:
        # Recognize gestures in the image
        height, width, _ = image.shape
        image = mp.Image(image_format=mp.ImageFormat.SRGB, data=image)

        recognition_result = (
            self.recognizer.recognize_for_video(image, int(frame_time_nano / 1e6))
            if self._video_mode
            else self.recognizer.recognize(image)
        )

        # Clear the values
        for log_key in ["Media/Points", "Media/Connections"]:
            rr.log(log_key, rr.Clear(recursive=True))

        for i, gesture in enumerate(recognition_result.gestures):
            # Get the top gesture from the recognition result
            gesture_category = gesture[0].category_name if recognition_result.gestures else "None"
            print("Gesture Category: ", gesture_category) # Log the detected gesture

        if recognition_result.hand_landmarks:
            hand_landmarks = recognition_result.hand_landmarks

            # Convert normalized coordinates to image coordinates
            points = self.convert_landmarks_to_image_coordinates(hand_landmarks, width, height)

            # Log points to the image and Hand Entity
            rr.log(
               "Media/Points",
                rr.Points2D(points, radii=10, colors=[255, 0, 0])
            )

            # Obtain hand connections from MediaPipe
            mp_hands_connections = mp.solutions.hands.HAND_CONNECTIONS
            points1 = [points[connection[0]] for connection in mp_hands_connections]
            points2 = [points[connection[1]] for connection in mp_hands_connections]

            # Log connections to the image and Hand Entity 
            rr.log(
               "Media/Connections",
                rr.LineStrips2D(
                   np.stack((points1, points2), axis=1),
                   colors=[255, 165, 0]
                )
             )

    def convert_landmarks_to_image_coordinates(hand_landmarks, width, height):
        return [(int(lm.x * width), int(lm.y * height)) for hand_landmark in hand_landmarks for lm in hand_landmark]
```

## 3D 点

最后，我们研究如何将手部地标呈现为 3D 点。我们首先在初始化函数中使用来自[注释上下文](https://www.rerun.io/docs/concepts/annotation-context)的关键点定义点之间的连接，然后将它们作为[3D 点](https://www.rerun.io/docs/reference/types/archetypes/points3d)进行记录。

![](../Images/140447578f2915dba04718f2d11f1588.png)

使用 Rerun SDK 记录 3D 数据 | 图片来自作者

```py
class GestureDetectorLogger:
–

  def __init__(self, video_mode: bool = False):
      # ... existing code ...
      rr.log(
            "/",
            rr.AnnotationContext(
                rr.ClassDescription(
                    info=rr.AnnotationInfo(id=0, label="Hand3D"),
                    keypoint_connections=mp.solutions.hands.HAND_CONNECTIONS
                )
            ),
            timeless=True,
        )
       rr.log("Hand3D", rr.ViewCoordinates.RIGHT_HAND_X_DOWN, timeless=True)

   def detect_and_log(self, image: npt.NDArray[np.uint8], frame_time_nano: int | None) -> None:
      # ... existing code ...

      if recognition_result.hand_landmarks:
         hand_landmarks = recognition_result.hand_landmarks

         landmark_positions_3d = self.convert_landmarks_to_3d(hand_landmarks)
         if landmark_positions_3d is not None:
              rr.log(
                 "Hand3D/Points",
                 rr.Points3D(landmark_positions_3d, radii=20, class_ids=0, keypoint_ids=[i for i in range(len(landmark_positions_3d))]),
              )

      # ... existing code ...
```

你准备好了！让魔法开始吧：

```py
# For image
run_from_sample_image(IMAGE_PATH)

# For saved video
run_from_video_capture(VIDEO_PATH)

# For Real-Time
run_from_video_capture(0) # mac may need 1
```

这个示例的完整源代码可以在[GitHub](https://github.com/rerun-io/rerun/tree/main/examples/python/gesture_detection)上找到。欢迎随意探索、修改并理解实现的内部工作原理。

# 超越手部跟踪与手势识别

![](../Images/e3e98cf7eca2c494781c91cf7e150a09.png)

Rerun 示例 | 图片来自 [Rerun](https://www.rerun.io)

最后，如果你对可视化跨多种应用场景的多模态数据流有浓厚兴趣，我鼓励你探索[Rerun 示例](https://www.rerun.io/examples/real-data)。这些示例展示了潜在的现实世界案例，并提供了有关这些可视化技术实际应用的宝贵见解。

*如果你觉得这篇文章有用且富有启发性，更多内容即将发布！我定期分享关于机器人技术和计算机视觉可视化的深度内容，绝对不容错过。为了获取未来的更新和令人兴奋的项目，记得关注我！*

*此外，你还可以在* [***LinkedIn***](http://www.linkedin.com/in/andreas-naoum)***上找到我。***

*类似文章：*

[](https://ai.gopubby.com/real-time-face-and-face-landmark-detection-with-mediapipe-rerun-showcase-40481baa1763?source=post_page-----9ec57cb0c831--------------------------------) [## 使用 MediaPipe 进行实时人脸和人脸地标检测：Rerun 展示

### 如何轻松地在 2D 和 3D 中可视化 MediaPipe 的人脸和人脸地标检测，使用 Rerun

ai.gopubby.com](https://ai.gopubby.com/real-time-face-and-face-landmark-detection-with-mediapipe-rerun-showcase-40481baa1763?source=post_page-----9ec57cb0c831--------------------------------) [](/human-pose-tracking-with-mediapipe-rerun-showcase-125053cfe64f?source=post_page-----9ec57cb0c831--------------------------------) [## 使用MediaPipe进行2D和3D人体姿态跟踪：Rerun展示

### 如何轻松地通过Rerun可视化MediaPipe的人体姿态跟踪

towardsdatascience.com](/human-pose-tracking-with-mediapipe-rerun-showcase-125053cfe64f?source=post_page-----9ec57cb0c831--------------------------------)

> 本页面的部分内容转载自[Google](https://developers.google.com/readme/policies)创建并分享的作品，并根据[创意共享4.0署名许可协议](https://creativecommons.org/licenses/by/4.0/)中的条款使用。
