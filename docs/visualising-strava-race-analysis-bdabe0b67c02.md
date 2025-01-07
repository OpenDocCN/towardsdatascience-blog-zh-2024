# 可视化Strava比赛分析。

> 原文：[https://towardsdatascience.com/visualising-strava-race-analysis-bdabe0b67c02?source=collection_archive---------6-----------------------#2024-08-06](https://towardsdatascience.com/visualising-strava-race-analysis-bdabe0b67c02?source=collection_archive---------6-----------------------#2024-08-06)

## *两个新的图表，比较同一场比赛中的跑者*

[](https://medium.com/@juanhernanz?source=post_page---byline--bdabe0b67c02--------------------------------)[![Juan Hernanz](../Images/005f79506da011de2d13c8360bd9fd62.png)](https://medium.com/@juanhernanz?source=post_page---byline--bdabe0b67c02--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdabe0b67c02--------------------------------)[![数据科学之路](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bdabe0b67c02--------------------------------) [Juan Hernanz](https://medium.com/@juanhernanz?source=post_page---byline--bdabe0b67c02--------------------------------)

·发布于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--bdabe0b67c02--------------------------------)·阅读时间：15分钟·2024年8月6日

--

![](../Images/0d0bbb903e8e9787b96827f334f29276.png)

显示跑者表现比较的图表。图片由作者提供。

*你有没有想过在同一场比赛中，两个跑者如何相互比较？*

在这篇文章中，我展示了我设计的两个新图表，因为我觉得Strava缺少这些图表。这些图表的设计方式使得人们可以一目了然地了解比赛的情况，因为它们比较了参加同一场赛事的不同运动员。你可以轻松看到各选手之间位置的变化，以及圈速和竞赛者之间的时间差异。

我的解释将从**我如何发现这个机会**开始。接下来，我将展示**图表设计**，并**解释驱动这些图表的算法**和数据处理技术。

# Strava并没有讲述完整的故事。

Strava是一款社交健身应用，用户可以记录并与100多万的社区成员分享他们的运动活动[1]。这款应用广泛用于骑行和跑步者之间，它不仅记录你的活动，还根据你的健康数据提供个性化的表现分析。

作为一名跑者，我觉得这款应用对我有两个主要好处：

1.  它提供的数据分析帮助我更好地理解我的跑步表现。

1.  它激励我保持动力，因为我可以看到我的朋友和社区分享的内容。

每当我和朋友一起完成一场跑步比赛时，我们都会将手表中的健身数据上传到Strava，查看如下一些分析：

+   **总时间**、**距离**和**平均配速**。

+   赛跑中的每个**分段时间**或圈速。

+   **心率**指标的变化。

+   与之前活动相比的**相对努力**。

最精彩的部分是我们从每个人的视角来讨论比赛。Strava能够识别出你和你的朋友（如果你们互相关注）以及其他人参加了同一场比赛，但它并不提供对比数据。因此，如果你想要了解你和朋友们的完整比赛故事，你需要深入查看每个人的活动并尝试进行对比。

所以，在我和3个朋友今年的最后一次10公里赛后，我决定从Strava获取数据，并设计了两张可视化图表，以查看我们比赛表现的对比分析。

# 展示可视化图表

这个项目背后的想法很简单：利用我和朋友在比赛过程中通过Strava记录的GPX数据（位置、时间戳），并将它们结合起来，生成比较我们比赛的可视化图表。

挑战不仅是验证我的想法是否可行，还要设计灵感来自Strava的图表，以证明它们如何作为新功能无缝集成到当前的应用程序中。让我们来看一下结果。

## 赛程差距分析

**指标：** 赛程差距的演变（以秒为单位），其中灰色线代表的是参考选手，其他选手则根据其与参考选手的距离变化而变化。位于上方的线表示该选手在比赛中领先。

**洞察：** 这张折线图非常适合查看一组选手的排名和距离变化。

![](../Images/460ad46fa47e712a322f36ce8d3f8954.png)

同一场比赛中3位不同选手的赛程差距分析动画。图片由作者提供。

如果你看一下这些线条的右端，你可以看到我们三个示例选手的比赛最终结果：

1.  第一个选手（我）由灰色线表示，是参考选手。

1.  Pedro（穿紫色衣服）是第二个选手，他在12秒后才到达终点。

1.  Jimena（穿蓝色衣服）在10公里赛后60秒完成比赛。

![](../Images/95fd5e59eead930dac07697c172c7541.png)![](../Images/5168b2d354043221d54bdfd073f5c809.png)

***赛程差距分析***图表整合到Strava活动中的提案。图片由作者提供。

但是，感谢这张图表，我们可以看到这些赛程差距在比赛中的变化。而这些洞察对于理解赛程中的位置和距离变化非常有趣：

1.  我们三个人一起开始了比赛。Jimena（穿蓝色衣服）在第一公里约5秒时开始落后，而我（穿灰色衣服）和Pedro（穿紫色衣服）则在一起。

1.  我记得Pedro告诉我开始得太快了，所以他稍微放慢了步伐，直到在第2公里遇到了Jimena。他们的轨迹显示，他们一起跑到了第5公里，而我则在不断拉开与他们的差距。

1.  第6公里是关键点，我与Pedro的差距在那时达到了20秒（我所达到的最大差距），与Jimena的差距则接近30秒。Jimena在那时比我慢了节奏，并一直保持较慢的速度直到比赛结束。然而，Pedro开始加速，并在最后4公里里努力缩小与我的差距。

当然，线条会根据参考点的不同而发生变化。这样，每个跑者都会看到同一场比赛的故事，但会根据他们的视角和与其他人的比较进行个性化展示。**这就是同一个故事，不同的主角。**

![](../Images/f04c215a2f18db2af0436b54d04f2035.png)![](../Images/516bf750c42bd8f946f426e7202337ba.png)![](../Images/efc9800546d599221bb7316ec3d16453.png)

使用不同参考点的赛程差距分析。参考点是胡安（左）。参考点是佩德罗（中）。参考点是吉梅纳（右）。图片来源：作者。

如果我是Strava，我会将这个图表集成到用户标记为“比赛”的活动中。分析可以通过所有注册了相同活动的用户的追随者来进行。上面显示了一个集成的示例。

## 头对头赛程分析

**度量标准：** 这条线表示两位跑者之间的时间差（秒）变化。条形图则表示每圈，某位跑者相较于另一位跑者的速度是更快（蓝色）还是更慢（红色）。

**见解：** 这个组合图表非常适合分析每圈比赛中的头对头表现。

![](../Images/eda6b248da26e98664a20785606d3f45.png)![](../Images/f486aca17ca420420cdc800f9524ee3c.png)

关于**佩德罗与胡安对比分析**在Strava上的集成建议。图片来源：作者。

这个图表专门设计用来比较两位跑者在比赛分段（圈次）中的表现。

这个例子代表了佩德罗与胡安相比的时间损失。

+   **橙色线条**代表了时间损失，如同另一个图表的解释：两人一起起跑，但佩德罗在第一个公里之后开始失去时间，直到第六公里。然后，他开始加速，缩小这个差距。

+   **这些条形图**为我们的比较带来了新的见解，展示了每圈的时间损失（红色）或时间增益（蓝色）。一眼看去，佩德罗可以看到时间损失最大的一圈是在第三公里（8秒）。而且他只在一半的分段中输了时间。两人在第1和第4公里的配速相同，佩德罗在第7、第8和第9公里的速度更快。

多亏了这个图表，我们可以看到，在前6公里我比佩德罗跑得更快，获得了一个佩德罗无法缩小的优势，尽管他在比赛的最后部分速度更快。这也证实了我们在比赛后常有的感觉：“佩德罗在比赛的最后冲刺更强。”

# 数据处理与算法

如果你想知道这些图表是如何创建的，可以继续阅读关于实现的这一部分内容。

我不想过多讨论背后的编码细节。像所有软件问题一样，你可以通过不同的解决方案来实现目标。这就是为什么我更感兴趣的是解释我遇到的挑战以及我的解决逻辑。

## 数据加载中

没有数据，就没有解决方案。在这种情况下不需要 Strava API。如果你登录 Strava 账户并进入某个活动，你可以通过点击 *Export GPX* 来下载该活动的 GPX 文件，如屏幕截图所示。GPX 文件包含 XML 格式的数据点，如下所示。

![](../Images/44846a62ae44815980e3f68bd2437b7b.png)![](../Images/d26c929437711195dc64b29237a91036.png)

如何从 Strava 下载 GPX 文件（左）。GPX 文件示例（右）。图片来源：作者。

为了获取我朋友们相同活动的数据，我只是让他们按照相同的步骤进行，并将 .gpx 文件发给我。

## 数据准备

对于这个用例，我只对几个属性感兴趣：

+   位置：*纬度，经度* 和 *海拔*

+   时间戳：*时间*。

对我来说，第一个问题是将 .gpx 文件转换为 pandas 数据框，这样我就可以使用 Python 来处理和处理数据。我使用了 ***gpxpy*** 库。下面是代码。

```py
import pandas as pd
import gpxpy

# read file
with open('juan.gpx', 'r') as gpx_file:
    juan_gpx = gpxpy.parse(gpx_file)

# Convert Juan´s gpx to dataframe
juan_route_info = []

for track in juan_gpx.tracks:
    for segment in track.segments:
        for point in segment.points:
            juan_route_info.append({
                'latitude': point.latitude,
                'longitude': point.longitude,
                'elevation': point.elevation,
                'date_time': point.time
            })

juan_df =  pd.DataFrame(juan_route_info)
juan_df
```

在那之后，我有了 667 个数据点存储在数据框中。每一行代表 **我** 在活动期间的 **位置** 和 **时间**。

我了解到，并不是每一行的数据捕捉频率都是一致的（0和1之间间隔1秒，然后是3秒，再是4秒，接着是1秒……）

![](../Images/e68884938e5f0f12dd8a742d6fc5a4cc.png)

存储在 pandas 数据框中的 .gpx 数据示例。图片来源：作者。

## 获取一些指标

数据中的每一行代表一个不同的时刻和地点，所以我的第一个想法是计算两行之间的时间、海拔和距离差异：***seconds_diff***、***elevation_diff*** 和 ***distance_diff***。

时间和海拔使用 *.diff()* 方法在 pandas 数据框的每一列上进行计算，比较直接。

```py
# First Calculate elevation diff
juan_df['elevation_diff'] = juan_df['elevation'].diff()

# Calculate the difference in seconds between datapoints
juan_df['seconds_diff'] = juan_df['date_time'].diff()
```

不幸的是，由于地球不是平的，我们需要使用一种叫做**哈弗辛距离** [2]**：**计算两个点在球面上最短距离的度量，给定它们的纬度和经度坐标。我使用了 *haversine* 库。请参见下面的代码。

```py
import haversine as hs

# Function to calculate haversine distances
def haversine_distance(lat1, lon1, lat2, lon2) -> float:
    distance = hs.haversine(
        point1=(lat1, lon1),
        point2=(lat2, lon2),
        unit=hs.Unit.METERS
    )

    # Returns the distance between the first point and the second point
    return np.round(distance, 2)

#calculate the distances between all data points
distances = [np.nan]

for i in range(len(track_df)):
    if i == 0:
        continue
    else:
        distances.append(haversine_distance(
            lat1=juan_df.iloc[i - 1]['latitude'],
            lon1=juan_df.iloc[i - 1]['longitude'],
            lat2=juan_df.iloc[i]['latitude'],
            lon2=juan_df.iloc[i]['longitude']
        ))

juan_df['distance_diff'] = distances
```

累计距离作为一个新列 ***distance_cum*** 被添加，使用了 *cumsum()* 方法，如下所示。

```py
# Calculate the cumulative sum of the distance
juan_df['distance_cum'] = juan_df['distance_diff'].cumsum()
```

此时，包含我的轨迹数据的数据框已包括4个具有有用指标的新列：

![](../Images/f44598fea204f0be62a99fd6fd7a8e1f.png)

每一行的新指标数据框。图片来源：作者。

我将相同的逻辑应用到其他跑步者的轨迹：*jimena_df* 和 *pedro_df*。

![](../Images/50e2a0e6bd554af943b9e056fbe429d5.png)![](../Images/955862c10068818bdde7587af22c0166.png)

其他跑步者的数据框：Pedro（左）和Jimena（右）。图片来源：作者。

现在我们准备好开始操作数据并创建可视化效果。

## **挑战：**

为了获得用于可视化所需的数据，我的第一直觉是：查看每个跑步者的累计距离列，识别每个跑步者完成一圈的时刻（1000、2000、3000 等），并计算时间戳的差异。

这个算法看起来简单，可能有效，但它有一些我需要解决的限制：

1.  精确的圈速距离通常是在两个注册数据点之间完成的。为了更精确，我需要对**位置**和**时间**进行**插值**。

1.  由于**设备精度的差异**，不同跑者之间可能会存在不对齐的情况。最典型的情况是，即使两名跑者始终在同一轨迹上，某一跑者的圈速通知在另一跑者之前响起。为尽量减少这种情况，我决定**使用参考跑者来设置每圈在轨道上的位置标记**。当其他跑者跨越这些标记时，将计算时间差（即使他们的累计距离领先或滞后）。这种方法更接近比赛的实际情况：如果某人先跨越一个标记，他们就领先（无论其设备的累计距离如何）。

1.  在前一点中，另一个问题随之而来：参考标记的纬度和经度可能永远不会完全在其他跑者的数据中注册。我使用了**最近邻算法**来根据位置找到最接近的数据点。

1.  最后，如果轨迹在不同的时间点经过相同的位置，最近邻算法可能会引入错误的数据点。因此，需要将最近邻查找最佳匹配的区域**缩小为一个较小的候选集**。我定义了一个**20个数据点的窗口大小**，以便围绕目标距离进行处理（*distance_cum*）。

## **算法**

考虑到之前所有的限制，算法应如下：

> 1\. 选择参考数据和圈速距离（默认= 1公里）。
> 
> 2\. 使用参考数据，确定每一圈完成的时间和位置：参考标记。
> 
> 3\. 进入其他跑者的数据，找出他们跨越这些位置标记的时刻。然后计算两名跑者跨越这些标记的时间差。最后，计算这个时间差的变化，表示间隔的演变。

## **代码示例**

> 1\. 选择参考数据和圈速距离（默认= 1公里）。

+   Juan将作为示例中的参考（*juan_df*）。

+   其他跑者将是Pedro（*pedro_df*）和Jimena（*jimena_df*）。

+   圈速距离将为1000米。

> 2\. 创建**interpolate_laps()**：这个函数用来查找或插值每一圈完成的精确点，并将其返回为一个新的DataFrame*。*插值是通过函数**interpolate_value()**完成的，这个函数也被创建了。

```py
## Function: **interpolate_value**()

**Input**: 
    - *start*: The starting value.
    - *end*: The ending value.
    - *fraction*: A value between 0 and 1 that represents the position between 
      the start and end values where the interpolation should occur.
**Return**:
    - The interpolated value that lies between the *start* and *end* values 
      at the specified *fraction*.
```

```py
def interpolate_value(start, end, fraction):
    return start + (end - start) * fraction
```

```py
## Function: **interpolate_laps()**

**Input**: 
    - track_df: dataframe with track data.
    - lap_distance: metres per lap (default 1000)
**Return**:
    - track_laps: dataframe with lap metrics. As many rows as laps identified.
```

```py
def interpolate_laps(track_df , lap_distance = 1000):
  #### 1\. Initialise track_laps with the first row of track_df 
  track_laps = track_df.loc[0][['latitude','longitude','elevation','date_time','distance_cum']].copy()

  # Set distance_cum = 0
  track_laps[['distance_cum']] = 0

  # Transpose dataframe
  track_laps = pd.DataFrame(track_laps)
  track_laps = track_laps.transpose()

  #### 2\. Calculate number_of_laps = Total Distance / lap_distance
  number_of_laps = track_df['distance_cum'].max()//lap_distance

  #### 3\. For each lap i from 1 to number_of_laps:
  for i in range(1,int(number_of_laps+1),1):

    # a. Calculate target_distance = i * lap_distance
    target_distance = i*lap_distance

    # b. Find first_crossing_index where track_df['distance_cum'] > target_distance
    first_crossing_index = (track_df['distance_cum'] > target_distance).idxmax()

    # c. If match is exactly the lap distance, copy that row
    if (track_df.loc[first_crossing_index]['distance_cum'] == target_distance):
      new_row = track_df.loc[first_crossing_index][['latitude','longitude','elevation','date_time','distance_cum']]

    # Else: Create new_row with interpolated values, copy that row.
    else: 

      fraction = (target_distance - track_df.loc[first_crossing_index-1, 'distance_cum']) / (track_df.loc[first_crossing_index, 'distance_cum'] - track_df.loc[first_crossing_index-1, 'distance_cum'])

      # Create the new row
      new_row = pd.Series({
          'latitude': interpolate_value(track_df.loc[first_crossing_index-1, 'latitude'], track_df.loc[first_crossing_index, 'latitude'], fraction),
          'longitude': interpolate_value(track_df.loc[first_crossing_index-1, 'longitude'], track_df.loc[first_crossing_index, 'longitude'], fraction),
          'elevation': interpolate_value(track_df.loc[first_crossing_index-1, 'elevation'], track_df.loc[first_crossing_index, 'elevation'], fraction),
          'date_time': track_df.loc[first_crossing_index-1, 'date_time'] + (track_df.loc[first_crossing_index, 'date_time'] - track_df.loc[first_crossing_index-1, 'date_time']) * fraction,
          'distance_cum': target_distance
      }, name=f'lap_{i}')

    # d. Add the new row to the dataframe that stores the laps
    new_row_df = pd.DataFrame(new_row)
    new_row_df = new_row_df.transpose()

    track_laps = pd.concat([track_laps,new_row_df])

  #### 4\. Convert date_time to datetime format and remove timezone
  track_laps['date_time'] = pd.to_datetime(track_laps['date_time'], format='%Y-%m-%d %H:%M:%S.%f%z')
  track_laps['date_time'] = track_laps['date_time'].dt.tz_localize(None)

  #### 5\. Calculate seconds_diff between consecutive rows in track_laps
  track_laps['seconds_diff'] = track_laps['date_time'].diff()

  return track_laps
```

将插值函数应用到参考DataFrame中，将生成以下DataFrame：

```py
juan_laps = interpolate_laps(juan_df , lap_distance=1000)
```

![](../Images/6489422f28f6ea842bcb5bfeb6256a1e.png)

插值后的圈速度量的DataFrame。图片来源：作者。

请注意，由于这是一次10公里的比赛，因此已经识别出10圈，每圈1000米（见*distance_cum*列）。*seconds_diff*列显示每圈的时间。其他列（*latitude*，*longitude*，*elevation*和*date_time*）表示参考数据每圈的位置和时间，这是通过插值得到的结果。

> 3. 为了计算参考与其他跑者之间的时间差，我创建了函数 **gap_to_reference()**

```py
## Helper Functions:
- **get_seconds**(): Convert timedelta to total seconds
- **format_timedelta**(): Format timedelta as a string (e.g., "+01:23" or "-00:45")
```

```py
# Convert timedelta to total seconds
def get_seconds(td):
    # Convert to total seconds
    total_seconds = td.total_seconds()    

    return total_seconds

# Format timedelta as a string (e.g., "+01:23" or "-00:45")
def format_timedelta(td):
    # Convert to total seconds
    total_seconds = td.total_seconds()

    # Determine sign
    sign = '+' if total_seconds >= 0 else '-'

    # Take absolute value for calculation
    total_seconds = abs(total_seconds)

    # Calculate minutes and remaining seconds
    minutes = int(total_seconds // 60)
    seconds = int(total_seconds % 60)

    # Format the string
    return f"{sign}{minutes:02d}:{seconds:02d}"
```

```py
## Function: **gap_to_reference**()

**Input**: 
    - laps_dict: dictionary containing the df_laps for all the runnners' names
    - df_dict: dictionary containing the track_df for all the runnners' names
    - reference_name: name of the reference
**Return**:
    - matches: processed data with time differences.
```

```py
 def gap_to_reference(laps_dict, df_dict, reference_name):
  #### 1\. Get the reference's lap data from laps_dict
  matches = laps_dict[reference_name][['latitude','longitude','date_time','distance_cum']]

  #### 2\. For each racer (name) and their data (df) in df_dict:
  for name, df in df_dict.items():

    # If racer is the reference: 
    if name == reference_name:

      # Set time difference to zero for all laps
      for lap, row  in matches.iterrows():
        matches.loc[lap,f'seconds_to_reference_{reference_name}'] = 0

    # If racer is not the reference:
    if name != reference_name:

      # a. For each lap find the nearest point in racer's data based on lat, lon.
      for lap, row  in matches.iterrows():

        # Step 1: set the position and lap distance from the reference
        target_coordinates = matches.loc[lap][['latitude', 'longitude']].values
        target_distance = matches.loc[lap]['distance_cum']

        # Step 2: find the datapoint that will be in the centre of the window
        first_crossing_index = (df_dict[name]['distance_cum'] > target_distance).idxmax()

        # Step 3: select the 20 candidate datapoints to look for the match
        window_size = 20
        window_sample = df_dict[name].loc[first_crossing_index-(window_size//2):first_crossing_index+(window_size//2)]
        candidates = window_sample[['latitude', 'longitude']].values

        # Step 4: get the nearest match using the coordinates
        nn = NearestNeighbors(n_neighbors=1, metric='euclidean')
        nn.fit(candidates)
        distance, indice = nn.kneighbors([target_coordinates])

        nearest_timestamp = window_sample.iloc[indice.flatten()]['date_time'].values
        nearest_distance_cum = window_sample.iloc[indice.flatten()]['distance_cum'].values
        euclidean_distance = distance

        matches.loc[lap,f'nearest_timestamp_{name}'] = nearest_timestamp[0]
        matches.loc[lap,f'nearest_distance_cum_{name}'] = nearest_distance_cum[0]
        matches.loc[lap,f'euclidean_distance_{name}'] = euclidean_distance

      # b. Calculate time difference between racer and reference at this point
      matches[f'time_to_ref_{name}'] = matches[f'nearest_timestamp_{name}'] - matches['date_time']

      # c. Store time difference and other relevant data
      matches[f'time_to_ref_diff_{name}'] = matches[f'time_to_ref_{name}'].diff()
      matches[f'time_to_ref_diff_{name}'] = matches[f'time_to_ref_diff_{name}'].fillna(pd.Timedelta(seconds=0))

      # d. Format data using helper functions
      matches[f'lap_difference_seconds_{name}'] = matches[f'time_to_ref_diff_{name}'].apply(get_seconds)
      matches[f'lap_difference_formatted_{name}'] = matches[f'time_to_ref_diff_{name}'].apply(format_timedelta)

      matches[f'seconds_to_reference_{name}'] = matches[f'time_to_ref_{name}'].apply(get_seconds)
      matches[f'time_to_reference_formatted_{name}'] = matches[f'time_to_ref_{name}'].apply(format_timedelta)

#### 3\. Return processed data with time differences
  return matches
```

以下是实现逻辑并将结果存储在数据框 **matches_gap_to_reference：** 中的代码：

```py
# Lap distance
lap_distance = 1000

# Store the DataFrames in a dictionary
df_dict = {
    'jimena': jimena_df,
    'juan': juan_df,
    'pedro': pedro_df,
}

# Store the Lap DataFrames in a dictionary
laps_dict = {
    'jimena': interpolate_laps(jimena_df , lap_distance),
    'juan': interpolate_laps(juan_df , lap_distance),
    'pedro': interpolate_laps(pedro_df , lap_distance)
}

# Calculate gaps to reference
reference_name = 'juan'
matches_gap_to_reference  = gap_to_reference(laps_dict, df_dict, reference_name) 
```

结果数据框的列包含将在图表上显示的重要信息：

![](../Images/f2ece348885eb7e4fc99db1bd014b0c2.png)

来自函数 gap_to_reference() 返回的某些数据框列。图片由作者提供。

## 比赛间隙分析图

**要求：**

+   可视化需要针对作为 **参考** 的跑者进行定制。每个跑者都将由一条线形图表示。

+   **X轴表示距离。**

+   **Y轴表示与参考的间隙**，单位为秒

+   参考将设定基准线。y轴=0 的常数灰线

+   其他跑者的线条将位于参考线之上（如果他们在赛道上领先）或之下（如果他们落后）。

![](../Images/fab024dd6ddd7f803ea74b8be5714aaf.png)

*比赛间隙分析* 图表（10圈，1000米）。图片由作者提供。

为了呈现图表，我使用了 *plotly* 库，并使用了 **matches_gap_to_reference：** 中的数据。

**X轴**：每圈的累计距离。列 **distance_cum**

**Y轴：** 表示与参考的间隙，单位为秒：

+   灰色线：参考的间隙始终为0。

+   紫色线：Pedro 与参考之间的间隙 **(-) seconds_to_reference_pedro**。

+   蓝色线：Jimena 与参考之间的间隙 **(-) seconds_to_reference_jimena**。

## 头对头圈次分析图

**要求：**

+   可视化需要比较两名跑者的数据。一名参考跑者和一名竞争者。

+   **X轴表示距离**

+   **Y轴表示秒数**

+   将绘制两项指标来比较跑者的表现：线形图将显示每个比赛点的总间隙，条形图则表示每圈该间隙是增加（正值）还是减少（负值）。

![](../Images/3d35fdd833da8132fe85c33fa15f9b23.png)

*头对头圈次分析* 图表（10圈，1000米）。图片由作者提供。

再次说明，示例中呈现的数据来自 **matches_gap_to_reference：**

**X轴**：每圈的累计距离。列 **distance_cum**

**Y轴：**

+   橙色线：Pedro 与 Juan 之间的间隙 **(+) seconds_to_reference_pedro**

+   条形图：每圈之间该间隙的变化 **lap_difference_formatted_pedro。** 如果Pedro失去时间，间隙为正并以红色表示。否则，条形图为蓝色。

我改进了两种视觉效果的风格，使其更加贴合Strava的设计美学。

# 为本文点赞？

我在上次比赛后开始了这个想法。我非常喜欢这些视觉效果的结果，因此认为它们可能对Strava社区有用。所以我决定通过撰写这篇文章与大家分享。

# 参考资料

[1] S. Paul, [Strava的下一篇章：新任CEO谈AI、包容性以及为什么‘暗黑模式’花了这么长时间才推出。](https://techcrunch.com/2024/07/20/stravas-next-chapter-new-ceo-talks-ai-inclusivity-and-why-dark-mode-took-so-long/?guccounter=1&guce_referrer=aHR0cHM6Ly93d3cubGlua2VkaW4uY29tLw&guce_referrer_sig=AQAAALrriMjjcs0gk_LkHSHhEYNqeEB_G3h2ZqbXA0KyemE5fIqpbYVbEaq70qgL5eTHmyTNsAHLol_fEefFsj3PLGzxpyB7xfU_wcUPkvT4Iso2kOe8h_Gf5pLpXrIP4hstEUxnbZ1LUWjAq2pAC3LUf-ucvEfU4eNTXCRxVMwipGZm) (2024)

[2] D. Grabiele, [“哈弗辛公式”，Baeldung上的计算机科学。](https://www.baeldung.com/cs/haversine-formula) (2024)
