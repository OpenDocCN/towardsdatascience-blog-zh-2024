# Graphcast：如何完成任务

> 原文：[https://towardsdatascience.com/graphcast-how-to-get-things-done-f2fd5630c5fb?source=collection_archive---------0-----------------------#2024-01-29](https://towardsdatascience.com/graphcast-how-to-get-things-done-f2fd5630c5fb?source=collection_archive---------0-----------------------#2024-01-29)

## 本文介绍了如何使用谷歌最新的工具进行预测，从获取数据到格式化等等。

[](https://abhinavyesss.medium.com/?source=post_page---byline--f2fd5630c5fb--------------------------------)[![Abhinav Kumar](../Images/96f52cd59bc0dcfc91a500c965c0d5a2.png)](https://abhinavyesss.medium.com/?source=post_page---byline--f2fd5630c5fb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f2fd5630c5fb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f2fd5630c5fb--------------------------------) [Abhinav Kumar](https://abhinavyesss.medium.com/?source=post_page---byline--f2fd5630c5fb--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f2fd5630c5fb--------------------------------) ·14分钟阅读·2024年1月29日

--

![](../Images/588d8016408891c86301bee99f160e65.png)

图片由 [NOAA](https://unsplash.com/@noaa?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

天气预测是一个非常复杂的问题。数值天气预报（NWP）模型、天气研究与预报（WRF）模型已被用来解决这个问题，但其准确性和精确性有时仍然不足。

由于其复杂性，这个问题吸引了从数据科学家到数据科学爱好者，再到气象工程师的广泛关注和解决方案的追求。虽然已经找到了解决方案，但一致性和统一性依然缺乏。解决方案因地区、山脉、高原、沼泽到苔原而异。根据我个人的经验，我相信其他人的经历也是如此，天气预测被发现是一个难以攻克的难题。引用一位虾类亿万富翁的话：

> 这就像一盒巧克力，你永远不知道自己会得到什么。

最近，Deepmind发布了一款新工具：[**Graphcast，一个用于更快、更准确的全球天气预报的AI模型**](https://deepmind.google/discover/blog/graphcast-ai-model-for-faster-and-more-accurate-global-weather-forecasting/)，试图让这包巧克力变得更加美味和高效。在谷歌TPU v4机器上，使用Graphcast，可以在不到一分钟的时间内，以0.25度的空间分辨率获取预测。它解决了使用传统方法预测时可能面临的许多问题：

+   预测结果是针对所有坐标一次性生成的，

+   根据坐标编辑逻辑现在变得多余。

+   令人费解的高效性和响应时间。

不那么令人费解的是使用上述工具获取预测所需的数据准备。

![](../Images/375e9e1395130580aa5d391c57e3527e.png)

[Ali Kokab](https://unsplash.com/@_alikokab_?utm_source=medium&utm_medium=referral) 的照片，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

然而，不用担心，我将成为你黑暗且阴郁盔甲下的骑士，在本文中解释准备和格式化数据所需的步骤，最后使用Graphcast获取预测。

**注意**：如今使用“AI”这个词让我非常想起在漫威电影中使用“量子”一词的方式。

获取预测是一个过程，可以分为以下几个部分：

1.  获取输入数据。

1.  创建目标。

1.  创建强迫数据。

1.  处理并将数据格式化为合适的格式。

1.  将它们整合在一起并进行预测。

Graphcast表示，使用当前的天气数据和6小时前的数据，可以预测未来6小时的情况。为了简单起见，举个例子：

+   **如果需要预测**：2024–01–01 18:00，

+   **然后输入数据**：2024–01–01 12:00 和 2024–01–01 06:00。

需要注意的是，**2024–01–01 18:00将是第一个被获取的预测**。Graphcast还可以额外获取未来10天的数据，每次预测之间间隔6小时。因此，其他可以获取预测的时间戳为：

+   2024–01–02 00:00，06:00，12:00，18:00，

+   2024–01–03 00:00，06:00，类似的时间戳一直到，

+   2024–01–10 06:00，12:00。

总结来说，**可以通过两个时间戳的输入** **预测40个时间戳的数据**。

# 假设和重要参数

在本文中，我将展示的代码中，我为某些参数指定了以下值，这些参数决定了你获得预测的速度以及使用的内存。

+   **输入时间戳**：2024–01–01 6:00，12:00。

+   **第一次预测时间戳**：2024–01–01 18:00。

+   **预测数量**：4。

+   **空间分辨率**：1度。

+   **压力水平**：13。

以下是导入所需包、初始化用于输入和预测的字段数组以及其他有用变量的代码。

```py
import cdsapi
import datetime
import functools
from graphcast import autoregressive, casting, checkpoint, data_utils as du, graphcast, normalization, rollout
import haiku as hk
import isodate
import jax
import math
import numpy as np
import pandas as pd
from pysolar.radiation import get_radiation_direct
from pysolar.solar import get_altitude
import pytz
import scipy
from typing import Dict
import xarray

client = cdsapi.Client() # Making a connection to CDS, to fetch data.

# The fields to be fetched from the single-level source.
singlelevelfields = [
                        '10m_u_component_of_wind',
                        '10m_v_component_of_wind',
                        '2m_temperature',
                        'geopotential',
                        'land_sea_mask',
                        'mean_sea_level_pressure',
                        'toa_incident_solar_radiation',
                        'total_precipitation'
                    ]

# The fields to be fetched from the pressure-level source.
pressurelevelfields = [
                        'u_component_of_wind',
                        'v_component_of_wind',
                        'geopotential',
                        'specific_humidity',
                        'temperature',
                        'vertical_velocity'
                    ]

# The 13 pressure levels.
pressure_levels = [50, 100, 150, 200, 250, 300, 400, 500, 600, 700, 850, 925, 1000]

# Initializing other required constants.
pi = math.pi
gap = 6 # There is a gap of 6 hours between each graphcast prediction.
predictions_steps = 4 # Predicting for 4 timestamps.
watts_to_joules = 3600
first_prediction = datetime.datetime(2024, 1, 1, 18, 0) # Timestamp of the first prediction.
lat_range = range(-180, 181, 1) # Latitude range.
lon_range = range(0, 360, 1) # Longitude range.

# A utility function used for ease of coding.
# Converting the variable to a datetime object.
def toDatetime(dt) -> datetime.datetime:
    if isinstance(dt, datetime.date) and isinstance(dt, datetime.datetime):
        return dt

    elif isinstance(dt, datetime.date) and not isinstance(dt, datetime.datetime):
        return datetime.datetime.combine(dt, datetime.datetime.min.time())

    elif isinstance(dt, str):
        if 'T' in dt:
            return isodate.parse_datetime(dt)
        else:
            return datetime.datetime.combine(isodate.parse_date(dt), datetime.datetime.min.time())
```

# 输入

在机器学习中，为了获取一些预测，你必须给机器学习模型一些数据，它会基于这些数据给出预测。例如，在预测一个人是否是蝙蝠侠时，输入数据可能是：

+   他们每晚睡多少小时？

+   他们的脸上有晒痕吗？

+   他们在早晨的会议上会打瞌睡吗？

+   他们的净资产是多少？

同样，Graphcast也需要某些输入，这些输入通过[**CDS**](https://cds.climate.copernicus.eu/cdsapp#!/home)获得，使用其Python库：[**cdsapi**](https://pypi.org/project/cdsapi/)。目前，数据发布者[**使用创作共用 4.0 国际许可协议**](https://publications.copernicus.org/for_authors/licence_and_copyright.html)，这意味着任何人都可以复制、分发、传输和修改该作品，只要注明原作者。

然而，在使用cdsapi获取数据之前，需要进行身份验证，[**具体说明见此**](https://cds.climate.copernicus.eu/api-how-to)，CDS提供了相关说明，且过程相对简单。

假设你现在已经获得CDS认证，可以创建输入数据，具体步骤如下：

1.  **获取单层值**：这些值依赖于***坐标***和***时间***。所需的输入字段之一是***total_precipitation_6hr***。顾名思义，这是指从该特定时间戳开始的过去6小时的降水量。因此，我们不仅仅获取两个输入时间戳的值，而是需要获取从**2024-01-01 00:00到12:00**的时间戳数据。

1.  **获取压力层值**：除了依赖于***坐标***外，还依赖于***压力层***。因此，在请求数据时，我们会指定所需的压力层数据。在这种情况下，我们只会获取两个输入时间戳的值。

1.  **合并单层和压力值**：在上述数据上，基于***时间***、***纬度***和***经度***进行内连接操作。

1.  **整合年份和天数进度**：除了单层和压力字段外，还需要向输入数据中添加四个字段：***year_progress_sin***、***year_progress_cos***、***day_progress_sin***和***day_progress_cos***。这可以通过[**graphcast**](https://github.com/google-deepmind/graphcast?tab=readme-ov-file)包中提供的函数来实现。

其他小步骤包括：

+   从CDS获取数据后重命名列，因为CDS输出的是天气变量的简化形式。

+   将***geopotential***变量重命名为***geopotential_at_surface***，用于单层数据，因为压力层有相同的字段名。

+   使用[**math**](https://docs.python.org/3/library/math.html)库中的函数，在从graphcast获得***progress***值后，计算sin和cos值。

+   将***latitude***重命名为***lat***，将***longitude***重命名为***lon***，并引入另一个索引：***batch***，其值为0。

创建输入数据的代码如下。

```py
# Getting the single and pressure level values.
def getSingleAndPressureValues():

    client.retrieve(
        'reanalysis-era5-single-levels',
        {
            'product_type': 'reanalysis',
            'variable': singlelevelfields,
            'grid': '1.0/1.0',
            'year': [2024],
            'month': [1],
            'day': [1],
            'time': ['00:00', '01:00', '02:00', '03:00', '04:00', '05:00', '06:00', '07:00', '08:00', '09:00', '10:00', '11:00', '12:00'],
            'format': 'netcdf'
        },
        'single-level.nc'
    )
    singlelevel = xarray.open_dataset('single-level.nc', engine = scipy.__name__).to_dataframe()
    singlelevel = singlelevel.rename(columns = {col:singlelevelfields[ind] for ind, col in enumerate(singlelevel.columns.values.tolist())})
    singlelevel = singlelevel.rename(columns = {'geopotential': 'geopotential_at_surface'})

    # Calculating the sum of the last 6 hours of rainfall.
    singlelevel = singlelevel.sort_index()
    singlelevel['total_precipitation_6hr'] = singlelevel.groupby(level=[0, 1])['total_precipitation'].rolling(window = 6, min_periods = 1).sum().reset_index(level=[0, 1], drop=True)
    singlelevel.pop('total_precipitation')

    client.retrieve(
        'reanalysis-era5-pressure-levels',
        {
            'product_type': 'reanalysis',
            'variable': pressurelevelfields,
            'grid': '1.0/1.0',
            'year': [2024],
            'month': [1],
            'day': [1],
            'time': ['06:00', '12:00'],
            'pressure_level': pressure_levels,
            'format': 'netcdf'
        },
        'pressure-level.nc'
    )
    pressurelevel = xarray.open_dataset('pressure-level.nc', engine = scipy.__name__).to_dataframe()
    pressurelevel = pressurelevel.rename(columns = {col:pressurelevelfields[ind] for ind, col in enumerate(pressurelevel.columns.values.tolist())})

    return singlelevel, pressurelevel

# Adding sin and cos of the year progress.
def addYearProgress(secs, data):

    progress = du.get_year_progress(secs)
    data['year_progress_sin'] = math.sin(2 * pi * progress)
    data['year_progress_cos'] = math.cos(2 * pi * progress)

    return data

# Adding sin and cos of the day progress.
def addDayProgress(secs, lon:str, data:pd.DataFrame):

    lons = data.index.get_level_values(lon).unique()
    progress:np.ndarray = du.get_day_progress(secs, np.array(lons))
    prxlon = {lon:prog for lon, prog in list(zip(list(lons), progress.tolist()))}
    data['day_progress_sin'] = data.index.get_level_values(lon).map(lambda x: math.sin(2 * pi * prxlon[x]))
    data['day_progress_cos'] = data.index.get_level_values(lon).map(lambda x: math.cos(2 * pi * prxlon[x]))

    return data

# Adding day and year progress.
def integrateProgress(data:pd.DataFrame):

    for dt in data.index.get_level_values('time').unique():
        seconds_since_epoch = toDatetime(dt).timestamp()
        data = addYearProgress(seconds_since_epoch, data)
        data = addDayProgress(seconds_since_epoch, 'longitude' if 'longitude' in data.index.names else 'lon', data)

    return data

# Adding batch field and renaming some others.
def formatData(data:pd.DataFrame) -> pd.DataFrame:

    data = data.rename_axis(index = {'latitude': 'lat', 'longitude': 'lon'})
    if 'batch' not in data.index.names:
        data['batch'] = 0
        data = data.set_index('batch', append = True)

    return data

if __name__ == '__main__':

    values:Dict[str, xarray.Dataset] = {}

    single, pressure = getSingleAndPressureValues()
    values['inputs'] = pd.merge(pressure, single, left_index = True, right_index = True, how = 'inner')
    values['inputs'] = integrateProgress(values['inputs'])
    values['inputs'] = formatData(values['inputs'])
```

# Targets

有11个预测字段：

+   u_component_of_wind,

+   v_component_of_wind,

+   geopotential,

+   specific_humidity,

+   temperature,

+   vertical_velocity,

+   10m_u_component_of_wind,

+   10m_v_component_of_wind,

+   2m_temperature,

+   mean_sea_level_pressure,

+   total_precipitation.

![](../Images/812a9485ce1659178f38f686944419c2.png)

由 [Ricardo Arce](https://unsplash.com/@jrarce?utm_source=medium&utm_medium=referral) 拍摄，图片来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

传递的目标本质上是一个空的 xarray，用于所有预测字段：

+   每个 ***coordinate***，

+   prediction ***timestamps*** 和

+   ***pressure level***。

实现这一功能的代码如下所示。

```py
# Includes the packages imported and constants assigned.
# The functions created for the inputs also go here.

predictionFields = [
                        'u_component_of_wind',
                        'v_component_of_wind',
                        'geopotential',
                        'specific_humidity',
                        'temperature',
                        'vertical_velocity',
                        '10m_u_component_of_wind',
                        '10m_v_component_of_wind',
                        '2m_temperature',
                        'mean_sea_level_pressure',
                        'total_precipitation_6hr'
                    ]

# Creating an array full of nan values.
def nans(*args) -> list:
    return np.full((args), np.nan)

# Adding or subtracting time.
def deltaTime(dt, **delta) -> datetime.datetime:
    return dt + datetime.timedelta(**delta)

def getTargets(dt, data:pd.DataFrame):

    # Creating an array consisting of unique values of each index.
    lat, lon, levels, batch = sorted(data.index.get_level_values('lat').unique().tolist()), sorted(data.index.get_level_values('lon').unique().tolist()), sorted(data.index.get_level_values('level').unique().tolist()), data.index.get_level_values('batch').unique().tolist()
    time = [deltaTime(dt, hours = days * gap) for days in range(4)]

    # Creating an empty dataset using latitude, longitude, the pressure levels and each prediction timestamp.
    target = xarray.Dataset({field: (['lat', 'lon', 'level', 'time'], nans(len(lat), len(lon), len(levels), len(time))) for field in predictionFields}, coords = {'lat': lat, 'lon': lon, 'level': levels, 'time': time, 'batch': batch})

    return target.to_dataframe()

if __name__ == '__main__':

    # The code for creating inputs will be here.

    values['targets'] = getTargets(first_prediction, values['inputs'])
```

# 强迫因子

正如 **targets** 的情况一样，**forcings** 中也包含每个坐标和预测时间戳的值，**但不包括压力层级**。**forcings** 中的字段包括：

+   total_incident_solar_radiation，

+   year_progress_sin，

+   year_progress_cos，

+   day_progress_sin，

+   day_progress_cos。

需要注意的是，以上值是 **相对于预测时间戳** 进行分配的。正如在处理 **inputs** 时的情况，***year*** 和 ***day progress*** 仅依赖于时间戳，而 ***solar radiation*** 来自单层数据源。然而，由于我们正在进行预测，即获取未来的值，对于 **forcings**，太阳辐射值在 CDS 数据集中是不可用的。为此，我们使用 [**pysolar**](https://pypi.org/project/pysolar/) 库来模拟太阳辐射值。

```py
# Includes the packages imported and constants assigned.
# The functions created for the inputs and targets also go here.

# Adding a timezone to datetime.datetime variables.
def addTimezone(dt, tz = pytz.UTC) -> datetime.datetime:
    dt = toDatetime(dt)
    if dt.tzinfo == None:
        return pytz.UTC.localize(dt).astimezone(tz)
    else:
        return dt.astimezone(tz)

# Getting the solar radiation value wrt longitude, latitude and timestamp.
def getSolarRadiation(longitude, latitude, dt):

    altitude_degrees = get_altitude(latitude, longitude, addTimezone(dt))
    solar_radiation = get_radiation_direct(dt, altitude_degrees) if altitude_degrees > 0 else 0

    return solar_radiation * watts_to_joules

# Calculating the solar radiation values for timestamps to be predicted.
def integrateSolarRadiation(data:pd.DataFrame):

    dates = list(data.index.get_level_values('time').unique())
    coords = [[lat, lon] for lat in lat_range for lon in lon_range]
    values = []

    # For each data, getting the solar radiation value at a particular coordinate.
    for dt in dates:
        values.extend(list(map(lambda coord:{'time': dt, 'lon': coord[1], 'lat': coord[0], 'toa_incident_solar_radiation': getSolarRadiation(coord[1], coord[0], dt)}, coords)))

    # Setting indices.
    values = pd.DataFrame(values).set_index(keys = ['lat', 'lon', 'time'])

    # The forcings dataset will now contain the solar radiation values.
    return pd.merge(data, values, left_index = True, right_index = True, how = 'inner')

def getForcings(data:pd.DataFrame):

    # Since forcings data does not contain batch as an index, it is dropped.
    # So are all the columns, since forcings data only has 5, which will be created.
    forcingdf = data.reset_index(level = 'level', drop = True).drop(labels = predictionFields, axis = 1)

    # Keeping only the unique indices.
    forcingdf = pd.DataFrame(index = forcingdf.index.drop_duplicates(keep = 'first'))

    # Adding the sin and cos of day and year progress.
    # Functions are included in the creation of inputs data section.
    forcingdf = integrateProgress(forcingdf)

    # Integrating the solar radiation values.
    forcingdf = integrateSolarRadiation(forcingdf)

    return forcingdf

if __name__ == '__main__':

    # The code for creating inputs and targets will be here.

    values['forcings'] = getForcings(values['targets'])
```

# 后处理输入、目标和强迫因子

现在三大支柱 Graphcast 已经创建完成，我们进入了最后冲刺阶段。就像 NBA 总决赛中，已经赢得了 3 场比赛，现在我们进入最关键的部分，完成任务。

就像科比·布莱恩特曾经说过的，

> 工作还没有完成。

![](../Images/e70074bb06ef6d6bd94183c390f84b2f.png)

由 [Mike Von](https://unsplash.com/@thevoncomplex?utm_source=medium&utm_medium=referral) 拍摄，图片来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

提到 xarray 时，数据主要有两种类型：

+   坐标，索引：***lat***、***lon***、***time***……以及

+   数据变量，列：***land_sea_mask***、***geopotential*** 等等。

每个数据变量包含的每个值，都有一定的坐标分配给它。坐标是数据变量值所依赖的那些坐标。例如，在我们自己的数据中，

+   ***land_sea_mask*** 完全依赖于 ***latitude*** 和 ***longitude***，即其坐标。

+   ***geopotential*** 的坐标是 ***batch***、***latitude***、***longitude***、***time*** 和 ***pressure level***。

+   与此形成鲜明对比，但却合乎逻辑的是，***geopotential_at_surface*** 的坐标是 ***latitude*** 和 ***longitude***。

因此，在我们继续进行天气预测之前，我们确保每个数据变量都分配到正确的坐标，相关的代码如下所示。

```py
# Includes the packages imported and constants assigned.
# The functions created for the inputs, targets and forcings also go here.

# A dictionary created, containing each coordinate a data variable requires.
class AssignCoordinates:

    coordinates = {
                    '2m_temperature': ['batch', 'lon', 'lat', 'time'],
                    'mean_sea_level_pressure': ['batch', 'lon', 'lat', 'time'],
                    '10m_v_component_of_wind': ['batch', 'lon', 'lat', 'time'],
                    '10m_u_component_of_wind': ['batch', 'lon', 'lat', 'time'],
                    'total_precipitation_6hr': ['batch', 'lon', 'lat', 'time'],
                    'temperature': ['batch', 'lon', 'lat', 'level', 'time'],
                    'geopotential': ['batch', 'lon', 'lat', 'level', 'time'],
                    'u_component_of_wind': ['batch', 'lon', 'lat', 'level', 'time'],
                    'v_component_of_wind': ['batch', 'lon', 'lat', 'level', 'time'],
                    'vertical_velocity': ['batch', 'lon', 'lat', 'level', 'time'],
                    'specific_humidity': ['batch', 'lon', 'lat', 'level', 'time'],
                    'toa_incident_solar_radiation': ['batch', 'lon', 'lat', 'time'],
                    'year_progress_cos': ['batch', 'time'],
                    'year_progress_sin': ['batch', 'time'],
                    'day_progress_cos': ['batch', 'lon', 'time'],
                    'day_progress_sin': ['batch', 'lon', 'time'],
                    'geopotential_at_surface': ['lon', 'lat'],
                    'land_sea_mask': ['lon', 'lat'],
                }

def modifyCoordinates(data:xarray.Dataset):

    # Parsing through each data variable and removing unneeded indices.
    for var in list(data.data_vars):
        varArray:xarray.DataArray = data[var]
        nonIndices = list(set(list(varArray.coords)).difference(set(AssignCoordinates.coordinates[var])))
        data[var] = varArray.isel(**{coord: 0 for coord in nonIndices})
    data = data.drop_vars('batch')

    return data

def makeXarray(data:pd.DataFrame) -> xarray.Dataset:

    # Converting to xarray.
    data = data.to_xarray()
    data = modifyCoordinates(data)

    return data

if __name__ == '__main__':

    # The code for creating inputs, targets and forcings will be here.

    values = {value:makeXarray(values[value]) for value in values}
```

# 使用 Graphcast 进行预测

计算、处理和组装好 **inputs**、**targets** 和 **forcings** 后，接下来就是进行 **predictions** 的时候了。

现在我们需要模型权重和归一化统计文件，这些文件是[**由Deepmind提供**](https://console.cloud.google.com/storage/browser/dm_graphcast;tab=objects?prefix=&forceOnObjectsSortingFiltering=false)的。

需要下载的文件包括：

+   stats/diffs_stddev_by_level.nc，

+   stats/stddev_by_level.nc，

+   stats/mean_by_level.nc 和

+   params/GraphCast_small — ERA5 1979–2015 — 分辨率 1.0 — 压力层级 13 — 网格 2to5 — 降水输入和输出.npz。

下方显示了上述文件相对于预测文件的相对路径。**保持结构的重要性**在于能够成功导入并读取所需的文件。

```py
.
├── prediction.py
├── model
    ├── params
        ├── GraphCast_small - ERA5 1979-2015 - resolution 1.0 - pressure levels 13 - mesh 2to5 - precipitation input and output.npz
    ├── stats
        ├── diffs_stddev_by_level.nc
        ├── mean_by_level.nc
        ├── stddev_by_level.nc
```

使用[**Deepmind提供的预测代码**](https://colab.research.google.com/drive/1X9WcRis_PC_DyuHYIiUwKWCAIr8T-4Pd#scrollTo=Sd99tPA3TBa4)，上述所有功能最终通过下面的代码片段完成预测。

```py
# Includes the packages imported and constants assigned.
# The functions created for the inputs, targets and forcings also go here.

with open(r'model/params/GraphCast_small - ERA5 1979-2015 - resolution 1.0 - pressure levels 13 - mesh 2to5 - precipitation input and output.npz', 'rb') as model:
    ckpt = checkpoint.load(model, graphcast.CheckPoint)
    params = ckpt.params
    state = {}
    model_config = ckpt.model_config
    task_config = ckpt.task_config

with open(r'model/stats/diffs_stddev_by_level.nc', 'rb') as f:
    diffs_stddev_by_level = xarray.load_dataset(f).compute()

with open(r'model/stats/mean_by_level.nc', 'rb') as f:
    mean_by_level = xarray.load_dataset(f).compute()

with open(r'model/stats/stddev_by_level.nc', 'rb') as f:
    stddev_by_level = xarray.load_dataset(f).compute()

def construct_wrapped_graphcast(model_config:graphcast.ModelConfig, task_config:graphcast.TaskConfig):
    predictor = graphcast.GraphCast(model_config, task_config)
    predictor = casting.Bfloat16Cast(predictor)
    predictor = normalization.InputsAndResiduals(predictor, diffs_stddev_by_level = diffs_stddev_by_level, mean_by_level = mean_by_level, stddev_by_level = stddev_by_level)
    predictor = autoregressive.Predictor(predictor, gradient_checkpointing = True)
    return predictor

@hk.transform_with_state
def run_forward(model_config, task_config, inputs, targets_template, forcings):
    predictor = construct_wrapped_graphcast(model_config, task_config)
    return predictor(inputs, targets_template = targets_template, forcings = forcings)

def with_configs(fn):
    return functools.partial(fn, model_config = model_config, task_config = task_config)

def with_params(fn):
    return functools.partial(fn, params = params, state = state)

def drop_state(fn):
    return lambda **kw: fn(**kw)[0]

run_forward_jitted = drop_state(with_params(jax.jit(with_configs(run_forward.apply))))

class Predictor:

    @classmethod
    def predict(cls, inputs, targets, forcings) -> xarray.Dataset:
        predictions = rollout.chunked_prediction(run_forward_jitted, rng = jax.random.PRNGKey(0), inputs = inputs, targets_template = targets, forcings = forcings)
        return predictions

if __name__ == '__main__':

    # The code for creating inputs, targets, forcings & processing will be here.

    predictions = Predictor.predict(values['inputs'], values['targets'], values['forcings'])
    predictions.to_dataframe().to_csv('predictions.csv', sep = ',')
```

# 结论

上面，我提供了每个将要执行的过程的代码：

+   创建输入、目标和强迫项，

+   将上述数据处理成可用格式，最终

+   将它们汇集在一起并进行预测。

在执行过程中，将所有过程整合在一起，以实现无缝的实施是非常重要的。

为了简单起见，我[**上传了代码**](https://github.com/abhinavyesss/graphcast-predict)，以及Docker镜像和容器文件，可以用来创建一个环境来执行预测程序。

在天气预测领域，目前我们有像Accuweather、IBM以及多个meteomatics模型等贡献者。Graphcast证明是这一系列中一个有趣且在许多情况下更高效的补充。然而，它也有一些远未达到最佳状态的属性。在某个难得的思考时刻，我总结出以下几点见解：

+   与其他天气预测服务相比，Graphcast更高效且速度更快，能够在几分钟内为整个世界提供预测。

+   这使得通过API进行成百上千次地理位置调用变得多余。

+   然而，要在几分钟内完成上述操作，必须拥有非常强大的机器，至少是Google TPU v4或更好的机器。这种机器并不容易获取。即便选择使用AWS、Google或Azure的虚拟机，费用也会迅速累积。

+   目前没有办法使用小范围的地理数据或坐标子集并获得相应的预测。始终需要所有坐标的数据。

+   CDS提供的数据有五天的延迟期，这意味着在“x”日期，CDS只能提供到“x-5”日期的数据。这使得未来天气预测变得有些复杂，因为在进行未来预测之前，必须先覆盖延迟期。

需要注意的是，Graphcast 是天气预测领域中相对较新的工具，未来肯定会进行更改和新增功能，以提高易用性和可访问性。考虑到它在效率和性能方面的领先优势，Graphcast 一定会加以利用。

资源：

+   [**Graphcast 演示代码**](https://colab.research.google.com/drive/1X9WcRis_PC_DyuHYIiUwKWCAIr8T-4Pd#scrollTo=Sd99tPA3TBa4)**。**

+   [**模型权重和统计文件**](https://console.cloud.google.com/storage/browser/dm_graphcast)。

+   [**论文**](https://storage.googleapis.com/deepmind-media/DeepMind.com/Blog/graphcast-ai-model-for-faster-and-more-accurate-global-weather-forecasting/Learning_skillful_medium-range_global_weather_forecasting.pdf)。

+   [**文章**](https://deepmind.google/discover/blog/graphcast-ai-model-for-faster-and-more-accurate-global-weather-forecasting/)。

+   [**CDS**](https://cds.climate.copernicus.eu/#!/home)。

祝你在数据科学之旅中好运，感谢阅读 :)
