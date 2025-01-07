# CV VideoPlayer — 一劳永逸

> 原文：[https://towardsdatascience.com/cv-videoplayer-once-and-for-all-b7e1b3349975?source=collection_archive---------6-----------------------#2024-12-12](https://towardsdatascience.com/cv-videoplayer-once-and-for-all-b7e1b3349975?source=collection_archive---------6-----------------------#2024-12-12)

## 为计算机视觉研究专门开发的 Python 视频播放器包

[](https://medium.com/@daniel_92063?source=post_page---byline--b7e1b3349975--------------------------------)[![Daniel Tomer](../Images/872d23ccef8f51ab54d69c99cb654955.png)](https://medium.com/@daniel_92063?source=post_page---byline--b7e1b3349975--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b7e1b3349975--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b7e1b3349975--------------------------------) [Daniel Tomer](https://medium.com/@daniel_92063?source=post_page---byline--b7e1b3349975--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b7e1b3349975--------------------------------) ·6 分钟阅读·2024年12月12日

--

![](../Images/fe525d3da0b40b616d63db54a0c0a9fc.png)

图片由作者提供

在开发计算机视觉算法时，从概念到可用实现的过程通常涉及无数次观看、分析和调试视频帧的迭代。随着我深入计算机视觉项目，我发现自己反复编写相同的样板代码来进行视频可视化和调试。

有一天，我决定不再忍受，于是我创建了 [**CV VideoPlayer**](https://pypi.org/project/cvvideoplayer/)，这是一个基于 Python 的开源视频播放器包，专为计算机视觉从业者设计，旨在一劳永逸地解决这个问题。

![](../Images/fb722b9a39bac8d52043f3f1eedd1ce4.png)

CV 视频播放器“双帧模式”加入了可视化和键盘快捷键。图片由作者提供

# 调试视频相关算法很困难

如果你曾经开发过视频分析算法，你可能已经编写过以下某个版本的代码，帮助你进行可视化和调试：

```py
import cv2

cap = cv2.VideoCapture(<video_path>)
ret = True
while ret:
  ret, frame = cap.read()
  algo_output = some_video_analsys_algorithm(frame)
  frame_to_display = visualizer(frame, algo_output)
  cv2.imshow(frame_to_display)
  cv2.waitKey()
```

但在我参与的几乎所有项目中，这段代码都远远不够。随着项目的推进，我发现自己不断添加更多功能，以帮助我理解发生了什么。

**例如：**

+   在视频中逐帧前后导航。

+   具备将输出记录到文件的能力。

+   支持除简单视频文件之外的其他数据源（帧文件夹、流、远程存储等）

**但最让我烦恼的事是缺乏交互性**。使用这种代码，视觉化效果在渲染之前就已创建，且一旦显示出来便无法更改。而且，虽然这种方式适用于简单的算法，但对于更复杂的算法来说，每一帧所需的信息量实在太多了。而且，如果不能动态决定要显示什么，你会发现自己一次又一次地重复播放同一个视频，每次使用不同的可视化参数。

这个过程既繁琐又令人疲惫。

# 进入CV VideoPlayer

![](../Images/321d31566897a63f73c1694372814775.png)

作者提供的图片

[CV VideoPlayer](https://pypi.org/project/cvvideoplayer/)的诞生源于对一个简单可定制解决方案的需求，该方案能够交互式地渲染视频和帧。它允许添加任意数量的叠加层、侧边栏或其他帧编辑，用户可以在运行时轻松开启或关闭这些功能。让我们看看如何实现这一点的示例：

## 安装

我们首先通过`pip install cvvideoplayer`安装这个包。

## 播放原始视频

然后，我们可以导入视频播放器并使用以下代码运行未编辑的视频：

```py
from cvvideoplayer import create_video_player

VIDEO_OR_FRAME_FOLDER_PATH = "<add local path here>"

video_player = create_video_player(video_source=VIDEO_OR_FRAME_FOLDER_PATH)
video_player.run()
```

这将打开视频播放器，并允许你使用空格键或箭头键播放视频，还会添加一些默认的内置`frame-edit-callbacks`，我们将在接下来的部分详细阐述。

![](../Images/0f99aa9cb9dbb81615f8607d90bafae6.png)

作者提供的图片

# 编辑帧以添加可视化效果

要为视频添加自定义的可视化效果，我们可以像这样使用`create_video_player`构造函数的`frame_edit_callbacks`参数：

```py
from cvvideoplayer import VideoPlayer

VIDEO_OR_FRAME_FOLDER_PATH = "<add local path here>"

video_player = create_video_player(
  video_source=VIDEO_OR_FRAME_FOLDER_PATH,
  frame_edit_callbacks=[
            FitFrameToScreen(),
            FrameInfoOverlay(),
            KeyMapOverlay(),
        ]
)
video_player.run()
```

如果未指定，默认列表将与上面示例中的列表完全一致。

## 内置回调

有许多内置的回调可以使用，例如：

+   `FitFrameToScreen` — 自动调整帧的大小以适应屏幕尺寸。

+   `FrameInfoOverlay` — 在左上角打印帧编号和原始帧分辨率。

+   `KeyMapOverlay` — 自动检测并打印所有可用的快捷键（包括用户自定义的快捷键）。

+   `DetectionCsvPlotter` — 绘制CSV文件中指定的边界框，CSV的表头为：frame_id, label, x1, y1, width, height, score

+   `FrameNormlizer` — 允许用户调整图像的动态范围。

+   `HistogramEqulizer` — 不言自明

每个版本都会添加更多功能。

## 创建自定义回调

这里正是这个包的有用之处。为了添加你自己的自定义可视化，你需要创建一个继承自`BaseFrameEditCallback`的新类，并实现`edit_frame`方法，例如：

```py
class MyCallback(BaseFrameEditCallback):
    def __init__(
        self,
        enable_by_default: bool = True,
        enable_disable_key: Optional[str] = None,
        additional_keyboard_shortcuts: Optional[List[KeyFunction]] = None
        **any_other_needed_params
    ):
        super().__init__(
            enable_by_default,
            enable_disable_key,
            additional_keyboard_shortcuts
         )

    def edit_frame(
        self,
        video_player: "VideoPlayer",
        frame: np.ndarray,
        frame_num: int,
        original_frame: np.ndarray,
    ) -> np.ndarray:
        """
        This function receives the displayed frame and should return it
        after it has been altered in any way desirable by the user

        Args:
            video_player: an instance fo VideoPlayer
            frame (): the frame to be edited and displayed
            frame_num ():
            original_frame () the frame before any alterations

        Returns: the edited frame
        """
        frame = add_any_visalizations(frame)
        return frame
```

此外，你还可以通过重写父类中的这些方法来添加设置和清理方法：

```py
class MyCallback(BaseFrameEditCallback):
    ... 
    def setup(self, video_player: "VideoPlayer", frame) -> None:
        """
        Optionally configure more parameters according to the
        first incoming frame
        """

    def teardown(self) -> None:
        """
        Optionally define how the callback should close when the
        video player is closed
        """
```

# 添加自定义快捷键

对于每个回调，CV Video Player允许你添加自定义快捷键，这些快捷键可以在运行时改变可视化效果。

最基本的快捷键是启用/禁用回调，它是通过 `enable_disable_key` 参数创建的，像这样：

```py
my_callback = MyCallback(
    enable_disable_key="ctrl+a"
)
```

传递的字符串可以是任何修饰符（ctrl、alt 和 shift）与字母或数字的组合，例如：**“ctrl+alt+s”**、**“g”**、**“shift+v”**、**“ctrl+1”**，等等。

要添加更改可视化本身的快捷键，你可以覆盖 `additional_keyboard_shortcuts` 属性，该属性返回一个包含 `KeyFunction` 数据类的列表。

```py
from cvvideoplayer import KeyFunction

class MyCallback(BaseFrameEditCallback):
    ...
    @property
    def additional_keyboard_shortcuts(self) -> List[KeyFunction]:
      [
          KeyFunction(
              key="alt+r",
              function=self.a_function_to_modify_the_visualiztion,
              description="what this does"
          )
      ]
```

`KeyFunction` 是通过三个参数构造的：

+   `key` 参数——与 `enable_disable_key` 相同，传递的字符串可以是任何修饰符（ctrl、alt 和 shift）与字母或数字的组合，例如：**“ctrl+alt+s”**、**“g”**、**“shift+v”**、**“ctrl+1”**。

+   `description` 参数——这个参数由 `KeyMapOverlay` 回调使用，用于在屏幕上打印所有可用的快捷键。

+   `function` 参数——必须是一个不接受任何参数的函数。

在许多情况下，KeyFunction 将接收一个函数，该函数切换回调的某些布尔属性，从而改变 `edit_frame` 方法的某些行为。所以像这样：

```py
from cvvideoplayer import KeyFunction

class MyCallback(BaseFrameEditCallback):
...
    @property
    def additional_keyboard_shortcuts(self) -> List[KeyFunction]:
      [
          KeyFunction(
              key="alt+r",
              function=self.a_function_to_modify_the_visualiztion,
              description="what this does"
          )
      ]
    def a_function_to_modify_the_visualiztion():
        self._draw_something = bool(1 - self._draw_somthing)
```

# 双帧模式

很多时候，我发现自己想要并排比较两种不同的可视化。例如，比较两个探测器，或将算法的输出与原始帧进行对比（没有修改），等等。

为了做到这一点，我添加了 `double_frame_mode`，可以通过以下方式开启：

```py
video_player = create_video_player(
  ...
  double_frame_mode=True
)
```

本博客开头的视频展示了此模式的样子。

在此模式下，你可以使用“ctrl+1”和“ctrl+2”来决定用键盘控制哪个帧的可视化。

默认情况下，两个帧将具有相同的回调可用，但如果你希望右帧使用不同的回调，你可以使用 `right_frame_callback` 参数为右帧提供不同的回调集合（左帧将使用传递给 `frame_edit_callback` 参数的回调）。

```py
video_player = create_video_player(
    ...
    double_frame_mode=True
    right_frame_callbacks = [callback1, callback2, ...]
)
```

# 总结

希望这个工具能对大家有所帮助。如果你有任何改进的想法，请在项目的 [**GitHub 页面**](https://github.com/danieltomer1/CVVideoPlayer) 的问题标签中告诉我，顺便别忘了给项目加个星标 :) …
