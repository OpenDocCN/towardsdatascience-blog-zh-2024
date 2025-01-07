# 《Python工程师的3D高斯溅射入门（第一部分）》

> 原文：[https://towardsdatascience.com/a-python-engineers-introduction-to-3d-gaussian-splatting-part-1-e133b0449fc6?source=collection_archive---------2-----------------------#2024-06-11](https://towardsdatascience.com/a-python-engineers-introduction-to-3d-gaussian-splatting-part-1-e133b0449fc6?source=collection_archive---------2-----------------------#2024-06-11)

## 从Python工程师的角度理解和编码高斯溅射

[](https://medium.com/@dcaustin33?source=post_page---byline--e133b0449fc6--------------------------------)[![Derek Austin](../Images/1bcc5955f32cb798988af5713baae212.png)](https://medium.com/@dcaustin33?source=post_page---byline--e133b0449fc6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e133b0449fc6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e133b0449fc6--------------------------------) [Derek Austin](https://medium.com/@dcaustin33?source=post_page---byline--e133b0449fc6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e133b0449fc6--------------------------------) ·阅读时长7分钟·2024年6月11日

--

![](../Images/813c95a2e396af5470c94772b24a2b06.png)

图片来自[rivage](https://unsplash.com/@sigmund?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)于[Unsplash](https://unsplash.com/photos/white-and-blue-box-on-white-table-KznImGeQGWE?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

2023年初，来自法国蔚蓝海岸大学和德国马克斯·普朗克计算机研究所的作者们发表了一篇题为《用于实时场景渲染的3D高斯溅射》的论文¹。该论文提出了一项在实时神经渲染领域的重要进展，超越了像NeRF这样的先前方法的实用性²。高斯溅射不仅减少了延迟，还在渲染质量上与NeRF相当，甚至超越了它，迅速在神经渲染领域引起了轰动。

高斯喷洒虽然有效，但对于那些不熟悉相机矩阵和图形渲染的人员来说，可能很难理解。此外，我发现 Python 中实现高斯喷洒的资源稀缺，因为即使是作者的源代码也是用 CUDA 编写的！本教程旨在弥补这一空白，为精通 Python 和机器学习但对图形渲染经验较少的工程师提供基于 Python 的高斯喷洒入门教程。随附的 [GitHub](https://github.com/dcaustin33/intro_to_gaussian_splatting) 代码演示了如何初始化并渲染 COLMAP 扫描中的点，最终生成一个类似于喷洒应用中的前向传播图像（对于有兴趣的人，还包括一些 CUDA 代码）。本教程还提供了一个伴随的 Jupyter Notebook（[part_1.ipynb](https://github.com/dcaustin33/intro_to_gaussian_splatting/blob/main/part_1.ipynb) 在 GitHub 上），其中包含了所有跟随教程所需的代码。虽然我们不会构建一个完整的高斯喷洒场景，但如果按步骤操作，本教程应能为读者提供深入了解喷洒技术的基础知识。

首先，我们使用 COLMAP，这是一款通过结构光从运动（SfM）提取在多张图像中一致出现的点的软件。SfM 本质上是通过匹配不同图像中的点（例如门框右上角的边缘），来确定每个点在 3D 空间中的深度。这与人类立体视觉的工作原理非常相似，通过比较每只眼睛稍有不同的视角来感知深度。因此，SfM 从多张图像中找到的共同点生成了一组具有 x、y 和 z 坐标的 3D 点，进而为我们提供了场景的“结构”。

在本教程中，我们将使用一个预构建的 COLMAP 扫描，该扫描可在 [此处下载](https://storage.googleapis.com/gresearch/refraw360/360_extra_scenes.zip)（Apache 2.0 许可证）。具体来说，我们将使用下载数据集中的 Treehill 文件夹。

![](../Images/b16ab5f5a417adfd630e37b233a4855e.png)

图像以及从所有输入到 COLMAP 的图像中提取的所有点。请参阅下面的示例代码或 part_1.ipynb 文件以了解该过程。Apache 2.0 许可证。

该文件夹包含三个文件，分别对应相机参数、图像参数和实际的 3D 点。我们将从 3D 点开始。

点文件包含成千上万个 3D 点及其相关颜色。这些点围绕所谓的世界原点进行定位，基本上它们的 x、y 或 z 坐标是基于它们相对于这个世界原点被观察到的位置。世界原点的确切位置对于我们的目的并不重要，因此我们不会专注于它，因为它可以是空间中的任何一个任意点。相反，了解自己在世界中相对于该原点的位置才是至关重要的。这就是图像文件发挥作用的地方！

广义而言，图像文件告诉我们图像的拍摄位置以及相机的朝向，都是相对于世界原点的。因此，我们关心的关键参数是四元数向量和平移向量。四元数向量使用4个不同的浮动值描述相机在空间中的旋转，这些值可以用来形成一个旋转矩阵（3Blue1Brown有一个很好的视频解释四元数的概念，[观看视频](https://www.youtube.com/watch?v=d4EgbgTm0Bg)）。然后，平移向量告诉我们相机相对于原点的位置。这些参数共同构成外参矩阵，其中四元数值用于计算一个3x3的旋转矩阵（[公式](https://automaticaddison.com/how-to-convert-a-quaternion-to-a-rotation-matrix/)），平移向量则附加到该矩阵上。

![](../Images/09cc599d85fe8a84a64ec9deea9f4c11.png)

典型的“外参”矩阵。通过结合一个3x3的旋转矩阵和一个3x1的平移向量，我们能够将坐标从世界坐标系转换到相机坐标系。图片来源：作者。

外参矩阵将点从世界空间（点文件中的坐标）转换到相机空间，使得相机成为世界的新中心。例如，如果相机在y方向上移动了2个单位而没有任何旋转，我们只需从所有点的y坐标中减去2个单位，以便获得在新坐标系中的点。

当我们将坐标从世界空间转换到相机空间时，依然得到一个三维向量，其中z坐标表示相机视图中的深度。这个深度信息对于确定光点的顺序至关重要，后续渲染时需要用到。

我们通过解释相机参数文件来结束对COLMAP的讨论。相机文件提供了如高度、宽度、焦距（x和y方向）以及偏移量（x和y）等参数。通过这些参数，我们可以构建内参矩阵，它表示x和y方向的焦距以及主点坐标。

如果你对相机矩阵完全陌生，我推荐你参考Shree Nayar讲授的[计算机视觉基础课程](https://fpcv.cs.columbia.edu/)，特别是关于针孔和透视投影的[讲座](https://youtu.be/_EhY31MSbNM)，然后是关于内外参数矩阵的[讲座](https://www.youtube.com/watch?v=2XM2Rb2pfyQ&t=66s)。

![](../Images/b73bd7d9026d6ff1f86e406c282d2570.png)

典型的内参矩阵。表示x和y方向的焦距以及主点坐标。图片来源：作者。

内参矩阵用于将点从相机坐标（通过外参矩阵获得）转换到二维图像平面，也就是你所看到的“图像”。单独的相机坐标不能表明其在图像中的外观，因为必须反映深度信息才能准确评估相机看到的内容。

为了将 COLMAP 点转换为 2D 图像，我们首先使用外参矩阵将它们投影到相机坐标，然后再使用内参矩阵将它们投影到 2D。然而，一个重要的细节是，在此过程中我们使用了齐次坐标。外参矩阵是 4x4 的，而输入点是 3x1 的，因此我们需要在输入点上加一个 1，将它们变为 4x1。

这是逐步的过程：

1.  将点转换到相机坐标：将 4x4 的外参矩阵与 4x1 的点向量相乘。

1.  转换为图像坐标：将 3x4 的内参矩阵与得到的 4x1 向量相乘。

这将得到一个 3x1 的矩阵。为了获得最终的 2D 坐标，我们将这个 3x1 矩阵的第三个坐标除以它，得到图像中的 x 和 y 坐标！你可以看到图像编号 100 应该是什么样子，下面是复制结果的代码。

```py
def get_intrinsic_matrix(
    f_x: float, f_y: float, c_x: float, c_y: float
) -> torch.Tensor:
    """
    Get the homogenous intrinsic matrix for the camera
    """
    return torch.Tensor(
        [
            [f_x, 0, c_x, 0],
            [0, f_y, c_y, 0],
            [0, 0, 1, 0],
        ]
    )

def get_extrinsic_matrix(R: torch.Tensor, t: torch.Tensor) -> torch.Tensor:
    """
    Get the homogenous extrinsic matrix for the camera
    """
    Rt = torch.zeros((4, 4))
    Rt[:3, :3] = R
    Rt[:3, 3] = t
    Rt[3, 3] = 1.0
    return Rt

def project_points(
    points: torch.Tensor, intrinsic_matrix: torch.Tensor, extrinsic_matrix: torch.Tensor
) -> torch.Tensor:
    """
    Project the points to the image plane

    Args:
        points: Nx3 tensor
        intrinsic_matrix: 3x4 tensor
        extrinsic_matrix: 4x4 tensor
    """
    homogeneous = torch.ones((4, points.shape[0]), device=points.device)
    homogeneous[:3, :] = points.T
    projected_to_camera_perspective = extrinsic_matrix @ homogeneous
    projected_to_image_plane = (intrinsic_matrix @ projected_to_camera_perspective).T # Nx4

    x = projected_to_image_plane[:, 0] / projected_to_image_plane[:, 2]
    y = projected_to_image_plane[:, 1] / projected_to_image_plane[:, 2]
    return x, y

colmap_path = "treehill/sparse/0"
reconstruction = pycolmap.Reconstruction(colmap_path)

points3d = reconstruction.points3D
images = read_images_binary(f"{colmap_path}/images.bin")
cameras = reconstruction.cameras

all_points3d = []
all_point_colors = []

for idx, point in enumerate(points3d.values()):
    if point.track.length() >= 2:
        all_points3d.append(point.xyz)
        all_point_colors.append(point.color)

gaussians = Gaussians(
    torch.Tensor(all_points3d), 
    torch.Tensor(all_point_colors),
    model_path="point_clouds"
)

# we will examine the 100th image
image_num = 100    
image_dict = read_image_file(colmap_path)
camera_dict = read_camera_file(colmap_path)

# convert quaternion to rotation matrix
rotation_matrix = build_rotation(torch.Tensor(image_dict[image_num].qvec).unsqueeze(0))
translation = torch.Tensor(image_dict[image_num].tvec).unsqueeze(0)
extrinsic_matrix = get_extrinsic_matrix(
    rotation_matrix, translation
)
focal_x, focal_y = camera_dict[image_dict[image_num].camera_id].params[:2]
c_x, c_y = camera_dict[image_dict[image_num].camera_id].params[2:4]
intrinsic_matrix = get_intrinsic_matrix(focal_x, focal_y, c_x, c_y)

points = project_points(gaussians.points, intrinsic_matrix, extrinsic_matrix)
```

复习一下，现在我们可以通过任何一组 3D 点，利用我们需要的各种位置和相机参数，计算它们在 2D 图像平面上出现的位置！有了这些，我们可以继续理解[第二部分](https://medium.com/towards-data-science/a-python-engineers-introduction-to-3d-gaussian-splatting-part-2-7e45b270c1df)中高斯溅射的“高斯”部分。

1.  Kerbl, Bernhard, 等. “实时辐射场渲染的 3D 高斯溅射。” *ACM 图形学学报* 42.4（2023）：1–14。

1.  Mildenhall, Ben, 等. “Nerf：将场景表示为神经辐射场进行视图合成。” *ACM 通讯* 65.1（2021）：99–106。

1.  Snavely, Noah, Steven M. Seitz, 和 Richard Szeliski. “照片旅游：在 3D 中探索照片集合。” *ACM siggraph 2006 论文集*，2006，835–846。

1.  Barron, Jonathan T., 等. “Mip-nerf 360: 无边界抗锯齿神经辐射场。” *IEEE/CVF 计算机视觉与模式识别会议论文集*，2022。
