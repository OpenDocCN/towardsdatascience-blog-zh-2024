# 产品分析师的实用计算机模拟

> 原文：[https://towardsdatascience.com/practical-computer-simulations-for-product-analysts-90b5deb6a54e?source=collection_archive---------5-----------------------#2024-04-19](https://towardsdatascience.com/practical-computer-simulations-for-product-analysts-90b5deb6a54e?source=collection_archive---------5-----------------------#2024-04-19)

## 第一部分：针对场景预测的任务特定方法

[](https://miptgirl.medium.com/?source=post_page---byline--90b5deb6a54e--------------------------------)[![Mariya Mansurova](../Images/b1dd377b0a1887db900cc5108bca8ea8.png)](https://miptgirl.medium.com/?source=post_page---byline--90b5deb6a54e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--90b5deb6a54e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--90b5deb6a54e--------------------------------) [Mariya Mansurova](https://miptgirl.medium.com/?source=post_page---byline--90b5deb6a54e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--90b5deb6a54e--------------------------------) ·20分钟阅读·2024年4月19日

--

![](../Images/6987a0f2acb152826d29e342f49419d6.png)

图片来自DALL-E

在产品分析中，我们经常遇到“如果怎样”的问题。我们的团队不断发明各种方法来改进产品，并希望了解它如何影响我们的KPI或其他指标。

让我们来看一些例子：

+   假设我们在金融科技行业，面对新的规定，要求我们检查来自首次捐赠或向特定国家汇款超过10万美元的客户的更多文件。我们希望了解这一变化对我们的运营需求的影响，以及是否需要雇佣更多的客服人员。

+   让我们切换到另一个行业。我们可能希望通过推出一个新的奖励计划，鼓励出租车司机晚间工作或接受长途订单。在推出这一变化之前，估算奖励的预期规模并进行成本与收益分析对我们来说至关重要。

+   以最后一个例子为例，让我们看看主要的客户支持KPI。通常，公司会跟踪平均等待时间。改善这一指标有很多可能的方式。我们可以增加夜班，雇佣更多的客服人员，或者利用大型语言模型（LLMs）快速回答问题。为了优先考虑这些想法，我们需要估算它们对我们的KPI的影响。

当你第一次看到这样的问题时，它们看起来可能非常令人畏惧。

如果有人要求你计算月活跃用户数或7天留存率，这是直接的。你只需访问你的数据库，写SQL语句并使用现有的数据。

当你需要计算一些不存在的东西时，事情变得更加具有挑战性（也更为激动人心）。计算机模拟通常是此类任务的最佳解决方案。根据[维基百科](https://en.wikipedia.org/wiki/Simulation)，**模拟**是对一个可能存在于现实世界中的过程或系统的模拟性表现。因此，我们将尝试模仿不同的情景，并将其用于决策过程。

模拟是一个强大的工具，可以在各种情况下帮助你。因此，我想通过这系列文章与大家分享计算机模拟的实际案例：

+   在本文中，我们将讨论如何利用模拟来估算不同的情景。你将了解模拟的基本概念，并看到它们如何解决复杂的任务。

+   在第二部分，我们将不再讨论情景分析，而是聚焦于计算机模拟的经典方法——自举法（bootstrap）。自举法可以帮助你为度量指标获取置信区间，并分析A/B测试。

+   我想将第三部分的内容奉献给[基于代理的模型](https://en.wikipedia.org/wiki/Agent-based_model)。我们将模拟客户服务（CS）代理的行为，了解我们的过程变动如何影响客户服务的关键绩效指标（KPI），如队列长度或平均等待时间。

所以，是时候开始并讨论我们将在本文中解决的任务了。

# 我们的项目：为英语课程启动测试

假设我们正在开发一款教育技术产品，帮助人们学习英语。我们正在进行一项测试，这项测试可以从不同角度评估学生的知识水平（阅读、听力、写作和口语）。这项测试将为我们和我们的学生提供清晰的当前水平认知。

我们已经决定为所有新学生启动该测试，以便评估他们的初始水平。同时，我们会建议现有学生在下次回归服务时通过这项测试。

我们的目标是根据提交的测试数量建立时间预测。由于这些测试的某些部分（写作和口语）需要我们的老师进行人工审核，我们希望确保我们有足够的能力按时完成这些测试的审核。

让我们试着对问题进行结构化。我们有两组学生：

+   第一组是**现有学生**。在分析中精准是一个好习惯，因此我们将把他们定义为在本次上线之前已开始使用我们服务的学生。我们需要在他们下次交易时进行一次检查，因此在处理他们时会出现一个显著的需求激增。之后，这一群体的需求将变得微不足道（只有偶尔的重新激活）。

+   **新学生**希望能够继续加入我们的课程。因此，我们应该预期这一群体的需求会保持稳定。

现在，是时候思考我们如何估算这两组客户的需求了。

对于**新学生**的情况就比较直接——我们需要预测每周的新客户数量，并用它来估算需求。因此，这就是经典的时间序列预测任务。

预测**现有客户**需求的任务可能会更具挑战性。直接的方法是构建一个模型，预测学生下次回归服务的周数，并用它来进行估算。这是一个可行的解决方案，但对我来说有点过于复杂。

我更倾向于采用另一种方法。我会模拟我们在一段时间前启动这个测试的情景，并使用之前的数据。这样，我们将获得“此模拟启动”之后的所有数据，并能够计算出所有的指标。所以，这实际上是场景模拟的基本思路。

很好，我们有了计划。让我们继续执行。

# 建模新客户需求

在进行分析之前，让我们先检查一下我们拥有的数据。我们记录了课程完成事件的记录。我们知道每个事件的用户标识符、日期、模块和课程编号。我们将使用每周数据，以避免季节性波动，并捕捉有意义的趋势。

![](../Images/43843a3d3569e67eaf6d34c85251d222.png)

让我分享一些关于教育过程的背景信息。学生们主要来到我们的服务平台，从零开始学习英语，并通过六个模块（从预A1到C1）。每个模块包含100节课程。

> 这些数据是专门为这个用例生成的，因此我们正在使用一个合成数据集。

首先，我们需要计算出我们想要预测的指标。我们将为学生提供在完成第一节演示课后参加初步评估测试的机会。所以，我们可以轻松地计算出通过第一节课的客户数，或者按他们的首次日期来汇总用户数据。

```py
new_users_df = df.groupby('user_id', as_index = False).date.min()\
  .rename(columns = {'date': 'cohort'})

new_users_stats_df = new_users_df.groupby('cohort')[['user_id']].count()\
  .rename(columns = {'user_id': 'new_users'})
```

我们可以查看数据，发现整体呈增长趋势，并伴随有一些季节性影响（例如，夏季或圣诞节期间客户的加入人数较少）。

![](../Images/4f1587dc8cb87734b463b1df1d8ff7a7.png)

在预测中，我们将使用[Prophet](https://facebook.github.io/prophet/)——Meta的一个开源库。它在处理商业数据时效果非常好，因为它可以预测非线性趋势，并自动考虑季节性影响。你可以很容易地从PyPI安装它。

```py
pip install prophet
```

Prophet库期望的数据框架包含两列：`ds`表示时间戳，`y`表示我们想要预测的指标。此外，`ds`必须是日期时间列。因此，我们需要将数据转换为所需的格式。

```py
pred_new_users_df = new_users_df.copy()
pred_new_users_df = pred_new_users_df.rename(
  columns = {'new_users': 'y', 'cohort': 'ds'})
pred_new_users_df.ds = pd.to_datetime(pred_new_users_df.ds)
```

现在，我们准备好进行预测了。像往常一样，在机器学习中，我们需要初始化并拟合一个模型。

```py
from prophet import Prophet

m = Prophet()
m.fit(pred_new_users_df)
```

下一步是预测。首先，我们需要创建一个未来的数据框，指定预测的周期数和它们的频率（在我们的例子中是每周）。然后，我们需要调用`predict`函数。

```py
future = m.make_future_dataframe(periods= 52, freq = 'W')
forecast_df = m.predict(future)
forecast_df.tail()[['ds', 'yhat', 'yhat_lower', 'yhat_upper']]
```

结果，我们得到了预测值（`yhat`）和置信区间（`yhat_lower` 和 `yhat_upper`）。

![](../Images/038a1e1d4a20c36f00e76a9ffacecb3e.png)

如果没有图表，结果很难理解。让我们使用Prophet函数来更好地可视化输出结果。

```py
m.plot(forecast_df) # forecast
m.plot_components(forecast_df) # components
```

预测图表向你展示了带有置信区间的预测结果。

![](../Images/023fd7c58cfd4628e700d281faf1819d.png)

组件视图让你了解趋势和季节性效应之间的分布。例如，第二张图显示了夏季期间的季节性下降，以及九月初的增长（当人们可能更有动力开始学习新事物时）。

![](../Images/c4c514ca943e9bf65483ddfa2e0f0fd4.png)

我们可以将所有这些预测逻辑放入一个函数中。这样以后会对我们很有帮助。

```py
import plotly.express as px
import plotly.io as pio
pio.templates.default = 'simple_white'

def make_prediction(tmp_df, param, param_name = '', periods = 52):
    # pre-processing
    df = tmp_df.copy()
    date_param = df.index.name
    df.index = pd.to_datetime(df.index)

    train_df = df.reset_index().rename(columns = {date_param: 'ds', param: 'y'})

    # model
    m = Prophet()
    m.fit(train_df)

    future = m.make_future_dataframe(periods=periods, freq = 'W')
    forecast = m.predict(future)
    forecast = forecast[['ds', 'yhat']].rename(columns = {'ds': date_param, 'yhat': param + '_model'})

    # join to actual data
    forecast = forecast.set_index(date_param).join(df, how = 'outer')

    # visualisation
    fig = px.line(forecast, 
        title = '<b>Forecast:</b> ' + (param if param_name == '' else param_name),
        labels = {'value': param if param_name == '' else param_name},
        color_discrete_map = {param: 'navy', param + '_model': 'gray'}
    )
    fig.update_traces(mode='lines', line=dict(dash='dot'), 
        selector=dict(name=param + '_model'))
    fig.update_layout(showlegend = False)
    fig.show()

    return forecast

new_forecast_df = make_prediction(new_users_stats_df, 
  'new_users', 'new users', periods = 75)
```

我更倾向于与我的利益相关者分享一个更具样式的可视化版本（尤其是对于公开演讲），所以我也将其添加到了函数中。

![](../Images/06eaa510b37b23b26f271744de6aaaf1.png)

在这个例子中，我们使用了默认的Prophet模型，并得到了一个相当可信的预测结果。然而，在某些情况下，你可能需要调整参数，因此我建议你阅读[Prophet文档](https://facebook.github.io/prophet/docs/quick_start.html#python-api)以了解更多可能的调整选项。

例如，在我们的案例中，我们相信我们的受众将以相同的速度继续增长。然而，这可能并非如此，你可能预计它会在约100个用户时达到上限。让我们更新一下对饱和增长的预测。

```py
# adding cap to the initial data
# it's not required to be constant
pred_new_users_df['cap'] = 100

#specifying logistic growth
m = Prophet(growth='logistic')
m.fit(pred_new_users_df)

# adding cap for the future
future = m.make_future_dataframe(periods= 52, freq = 'W')
future['cap'] = 100
forecast_df = m.predict(future)
```

我们可以看到，预测结果发生了显著变化，并且增长停滞在每周约100个新客户。

![](../Images/c50618f0e4851c98c777991e5dad7c1c.png)

在这种情况下，查看组件图表也很有趣。我们可以看到季节性效应保持不变，而趋势已变为逻辑增长（正如我们指定的那样）。

![](../Images/cd9448cab3099823c8aafa704be0f14f.png)

我们已经了解了一些关于调整预测的能力。然而，针对未来的计算，我们将使用一个基本模型。我们的业务仍然相对较小，且最有可能尚未达到饱和状态。

我们已经得到了所有新客户所需的估算，并准备开始考虑现有客户的需求。

# 建模现有客户的需求

## 第一个版本

我们方法的关键点在于模拟我们在某段时间前进行该测试的情况，并使用这些数据计算需求。我们的解决方案基于一个理念，即我们可以利用过去的数据，而不是预测未来。

由于存在显著的年度季节性，我将使用过去一年的数据来自动考虑这些效应。我们希望在4月初启动这个项目。因此，我将使用2023年4月2日那一周的数据。

首先，我们需要筛选出与现有客户相关的数据，时间是2023年4月初。我们已经预测了新用户的需求，因此在此次估算中不需要考虑新用户。

```py
model_existing_users = df[df.date < '2023-04-02'].user_id.unique()
raw_existing_df = df[df.user_id.isin(model_existing_users)]
```

然后，我们需要对这些用户的需求进行建模。我们将为现有学生提供下次使用我们产品时参加测试的机会。所以，我们需要定义每个客户在推出后何时返回我们的服务，并按周汇总客户数量。这一点其实并不复杂。

```py
existing_model_df = raw_existing_df[raw_existing_df.date >= '2023-04-02']\
  .groupby('user_id', as_index = False).date.min()\
  .groupby('date', as_index = False).user_id.count()\
  .rename(columns = {'user_id': 'existing_users'})
```

我们得到了第一次估算。如果我们在2023年4月推出这个测试，第一周大约会有1.3K个测试，第二周0.3K，第三周80个案例，之后会更少。

![](../Images/fbefdfd378810634f2b65d8598dd309d.png)

我们假设100%的现有客户都会完成测试，我们需要对其进行检查。在实际任务中，值得考虑转化率，并相应调整数字。为了简便起见，这里我们将继续使用100%的转化率。

所以，我们完成了第一次建模。其实一点也不难。但这个估算足够好吗？

## 考虑长期趋势

我们正在使用去年的数据。然而，一切都在变化。让我们来看看活跃客户的数量变化情况。

```py
active_users_df = df.groupby('date')[['user_id']].nunique()\
    .rename(columns = {'user_id': 'active_users'})
```

![](../Images/aa96d886a01bb6c2677baf121cc54707.png)

我们可以看到它在稳步增长。我预计它会继续增长。所以，考虑到这一点，我们应该调整我们的预测以反映这个同比增长（*Year-over-Year*）。我们可以重新使用我们的预测函数，并利用预测值计算同比增长，以使其更加准确。

```py
 active_forecast_df = make_prediction(active_users_df, 
    'active_users', 'active users')
```

![](../Images/ee2f35233a05c10748da4fa37c376da4.png)

让我们根据我们的预测计算同比增长，并调整模型的预测值。

```py
# calculating YoYs
active_forecast_df['active_user_prev_year'] = active_forecast_df.active_users.shift(52)
active_forecast_df['yoy'] = active_forecast_df.active_users_model/\
  active_forecast_df.active_user_prev_year

existing_model_df = existing_model_df.rename(
  columns = {'date': 'model_date', 'existing_users': 'model_existing_users'})

# adjusting dates from 2023 to 2024
existing_model_df['date'] = existing_model_df.model_date.map(
  lambda x: datetime.datetime.strptime(x, '%Y-%m-%d') + datetime.timedelta(364)
)

existing_model_df = existing_model_df.set_index('date')\
   .join(active_forecast_df[['yoy']])

# updating estimations
existing_model_df['existing_users'] = list(map(
    lambda x, y: int(round(x*y)),
    existing_model_df.model_existing_users,
    existing_model_df.yoy
))
```

我们也完成了现有学生的估算。所以，我们准备将两部分合并，得到最终结果。

# 将一切汇总

## 初步结果

现在，我们可以将所有之前的估算结合起来，看看最终的图表。为此，我们需要将数据转换为通用格式，并添加不同的分段，以便我们能够区分新客户和现有客户的需求。

```py
# existing segment
existing_model_df = existing_model_df.reset_index()[['date', 'existing_users']]\
  .rename(columns = {'existing_users': 'users'})
existing_model_df['segment'] = 'existing'

# new segment
new_model_df = new_forecast_df.reset_index()[['cohort', 'new_users_model']]\
  .rename(columns = {'cohort': 'date', 'new_users_model': 'users'})
new_model_df = new_model_df[(new_model_df.date >= '2024-03-31') 
  & (new_model_df.date < '2025-04-07')]
new_model_df['users'] = new_model_df.users.map(lambda x: int(round(x)))
new_model_df['segment'] = 'new'

# combining everything
demand_model_df = pd.concat([existing_model_df, new_model_df])

# visualisation
px.area(demand_model_df.pivot(index = 'date', 
          columns = 'segment', values = 'users').head(15)[['new', 'existing']], 
    title = '<b>Demand</b>: modelling number of tests after launch',
    labels = {'value': 'number of test'})
```

![](../Images/2ed261ca8aa5d7ae80ade41198fecebc.png)

我们应该预期在推出后的第一周大约会有2.5K的测试需求，主要来自现有客户。然后，在接下来的四周内，我们将审查现有用户的测试，新增用户每周大约只有100到130个案例。

太好了。现在，我们可以将我们的估算与同事分享，这样他们也可以规划自己的工作。

## 如果我们遇到需求限制怎么办？

在现实生活中，当无法将新功能推广到100%的客户时，你经常会面临容量限制的问题。所以，是时候学习如何应对这种情况了。

假设我们发现我们的教师每周只能检查1000个测试。那我们就需要分散需求，避免糟糕的客户体验（当学生需要等待好几周才能得到结果时）。

幸运的是，我们可以通过将测试分批推出给现有客户来轻松实现。我们可以在第一周为所有新加入的用户和X%的现有客户开启此功能。然后，我们可以在第二周增加Y%的现有客户，等等。最终，我们将评估所有现有学生，并仅从新用户中产生持续的需求。

让我们制定一个推出计划，确保不会超过1000个容量的阈值。

由于我们肯定要为所有新学生推出，因此让我们从他们开始并将其加入计划中。我们将按分段存储所有的需求估算在`raw_demand_est_model_df`数据框中，并使用我们之前得到的`new_model_df`估算进行初始化。

```py
raw_demand_est_model_df = new_model_df.copy()
```

现在，我们可以汇总这些数据并计算剩余的容量。

```py
capacity = 1000

demand_est_model_df = raw_demand_est_model_df.pivot(index = 'date', 
    columns = 'segment', values = 'users')

demand_est_model_df['total_demand'] = demand_est_model_df.sum(axis = 1)
demand_est_model_df['capacity'] = capacity
demand_est_model_df['remaining_capacity'] = demand_est_model_df.capacity \
    - demand_est_model_df.total_demand

demand_est_model_df.head()
```

![](../Images/259999a7d45437eadf0ac9b1dbabf9ce.png)

让我们将这个逻辑放到一个单独的函数中，因为我们将在每次迭代后需要它来评估我们的估算结果。

```py
import plotly.graph_objects as go

def get_total_demand_model(raw_demand_est_model_df, capacity = 1000):
    demand_est_model_df = raw_demand_est_model_df.pivot(index = 'date', 
        columns = 'segment', values = 'users')
    demand_est_model_df['total_demand'] = demand_est_model_df.sum(axis = 1)
    demand_est_model_df['capacity'] = capacity
    demand_est_model_df['remaining_capacity'] = demand_est_model_df.capacity \
      - demand_est_model_df.total_demand

    tmp_df = demand_est_model_df.drop(['total_demand', 'capacity', 
        'remaining_capacity'], axis = 1)
    fig = px.area(tmp_df,
                 title = '<b>Demand vs Capacity</b>',
                  category_orders={'segment': ['new'] + list(sorted(filter(lambda x: x != 'new', tmp_df.columns)))},
                 labels = {'value': 'tests'})
    fig.add_trace(go.Scatter(
        x=demand_est_model_df.index, y=demand_est_model_df.capacity, 
        name='capacity', line=dict(color='black', dash='dash'))
    )

    fig.show()
    return demand_est_model_df

demand_plan_df = get_total_demand_model(raw_demand_est_model_df)
demand_plan_df.head()
```

我还在此函数的输出中添加了一个图表，帮助我们轻松评估结果。

![](../Images/67a0113dbc61d742f2dbf40c7e31ac44.png)

现在，我们可以按周计划现有客户的推出进程。

首先，让我们转变当前针对现有学生的需求模型。我希望它能够按周序列号进行索引，并显示100%的需求估算。然后，我可以通过将需求乘以权重，并根据启动日期和周数计算日期，顺利得到每批次的估算。

```py
existing_model_df['num_week'] = list(range(existing_model_df.shape[0]))
existing_model_df = existing_model_df.set_index('num_week')\
    .drop(['date', 'segment'], axis = 1)
existing_model_df.head()
```

![](../Images/736f165ce31047c1a725014b012a9c2e.png)

所以，例如，如果我们为10%的随机客户推出评估测试，那么我们预计第一周会进行244个测试，第二周52个测试，第三周14个测试，等等。

我将使用相同的估算来处理所有批次。我假设所有相同大小的批次在接下来的几周内将产生相同数量的测试。所以，我没有考虑与每个批次的启动日期相关的任何季节性影响。

这个假设大大简化了你的过程。在我们的案例中这是相当合理的，因为我们将在4到5周内完成推出，而且在这段时间内没有显著的季节性影响。然而，如果你想要更准确（或有显著季节性波动），你可以通过重复我们之前的过程来为每个批次建立需求估算。

让我们从2024年3月31日那一周开始。如我们之前看到的，我们有888个测试的剩余容量。如果我们为100%的现有客户推出测试，第一周我们将需要检查大约2400个测试。因此，我们只准备向一部分客户推出。让我们来计算一下。

```py
cohort = '2024-03-31'
demand_plan_df.loc[cohort].remaining_capacity/existing_model_df.iloc[0].users
# 0.3638
```

使用更简洁的数字操作会更容易，所以让我们将数字四舍五入到5%的倍数。我已将数字向下舍入，以留出一些缓冲。

```py
full_demand_1st_week = existing_model_df.iloc[0].users
next_group_share = demand_plan_df.loc[cohort].remaining_capacity/full_demand_1st_week
next_group_share = math.floor(20*next_group_share)/20
# 0.35
```

由于我们将进行几次迭代，我们需要追踪已有客户中启用了新功能的百分比。此外，检查我们是否已经处理了所有客户以避免重复计数也是值得的。

```py
enabled_user_share = 0

# if we can process more customers than are left, update the number
if next_group_share > 1 - enabled_user_share:
    print('exceeded')
    next_group_share = round(1 - enabled_user_share, 2)

enabled_user_share += next_group_share
# 0.35
```

此外，将我们的推出计划保存在一个单独的变量中将会很有帮助。

```py
rollout_plan = []
rollout_plan.append(
    {'launch_date': cohort, 'rollout_percent': next_group_share}
)
```

现在，我们需要估算这一批次的预期需求。3月31日对35%的客户进行测试，将不仅会在第一周产生需求，还会在接下来的几周产生需求。因此，我们需要计算这一批次的总需求，并将其纳入我们的计划中。

```py
# copy the model
next_group_demand_df = existing_model_df.copy().reset_index()

# calculate the dates from cohort + week number
next_group_demand_df['date'] = next_group_demand_df.num_week.map(
    lambda x: (datetime.datetime.strptime(cohort, '%Y-%m-%d') \
        + datetime.timedelta(7*x))
)

# adjusting demand by weight
next_group_demand_df['users'] = (next_group_demand_df.users * next_group_share).map(lambda x: int(round(x)))

# labelling the segment
next_group_demand_df['segment'] = 'existing, cohort = %s' % cohort

# updating the plan
raw_demand_est_model_df = pd.concat([raw_demand_est_model_df, 
    next_group_demand_df.drop('num_week', axis = 1)])
```

现在，我们可以重用函数`get_total_demand_mode`，它帮助我们分析当前的需求与容量之间的平衡。

```py
demand_plan_df = get_total_demand_model(raw_demand_est_model_df)
demand_plan_df.head()
```

我们在第一周已经利用了大部分容量。我们仍然有一些空闲资源，但我们故意决定为可持续性留出一些缓冲。我们可以看到，在三周后，这一批次几乎没有需求。

![](../Images/69099cf101463ab326d82459ced45d43.png)

到此为止，我们完成了第一轮迭代，可以进入下一个周期——2024年4月4日。我们可以在这一周再检查706个案例。

![](../Images/3daf0fc5aa2f9e3b3152e00a51861ff1.png)

我们可以重复整个过程直到本周结束，然后继续进入下一个阶段。我们可以进行迭代，直到我们将项目推广到100%的现有客户（`enabled_user_share`等于1）。

我们可以在不违反每周1000次测试容量限制的情况下，在四周内将我们的测试推出到所有客户。最后，我们将得到如下的每周预测。

![](../Images/ba0dd724bd732b52409020fe5c6e2b95.png)

我们还可以查看我们在模拟过程中记录的推出计划。所以，我们需要在3月31日那一周对随机选择的35%的客户进行测试，接着在下周对下一个20%的客户进行测试，再对接下来的两周分别对25%和20%的现有用户进行测试。之后，我们将把我们的项目推广到所有现有学生。

```py
rollout_plan
# [{'launch_date': '2024-03-31', 'rollout_percent': 0.35},
# {'launch_date': '2024-04-07', 'rollout_percent': 0.2},
# {'launch_date': '2024-04-14', 'rollout_percent': 0.25},
# {'launch_date': '2024-04-21', 'rollout_percent': 0.2}]
```

所以，恭喜你。我们现在有了一个可持续推出新功能的计划。

# 跟踪学生的表现变化

我们已经做了很多工作来估算需求。我们通过模拟一年前项目的推出过程，进行扩展并评估后果，从而运用了模拟的思路。所以，这绝对是一个模拟的例子。

然而，我们主要使用的是你每天都会用到的基本工具——一些Pandas数据整理和算术运算。在文章的最后部分，我想向你展示一个稍微复杂的案例，我们将需要为每个客户独立模拟该过程。

产品需求随着时间的推移通常会发生变化，我们的项目也经历了这种情况。你和团队决定，如果能够让学生在学习过程中跟踪进展（不仅仅是在一开始），那会更好。因此，我们希望能够让学生在每个模块结束后进行一次表现测试（如果距离上次测试已经超过一个月），或者如果学生在三个月的缺席后重新返回服务。

现在，测试分配的标准相当复杂。然而，我们仍然可以使用相同的方法，通过查看前一年的数据来进行判断。但是，这一次，我们需要查看每个客户的行为，并确定他们在什么情况下会得到一个测试。

我们将同时考虑新客户和现有客户，因为我们希望评估跟踪测试对所有客户的影响。我们不需要发布前的数据，因为第一个测试将在下一次有效交易时分配，之前的历史数据不再重要。因此，我们可以将其过滤掉。

```py
sim_df = df[df.date >= '2023-03-31']
```

让我们还定义一个函数，用于计算两个日期字符串之间的天数。这对我们在实现时会非常有帮助。

```py
def days_diff(date1, date2):
    return (datetime.datetime.strptime(date2, '%Y-%m-%d')\
        - datetime.datetime.strptime(date1, '%Y-%m-%d')).days
```

让我们从一个用户开始，详细讨论一下逻辑。首先，我们将筛选与该用户相关的事件，并将其转换为字典列表。这样处理数据对我们来说会更加方便。

```py
user_id = 4861
user_events = sim_df[sim_df.user_id == user_id]\
    .sort_values('date')\
    .to_dict('records')

# [{'user_id': 4861, 'date': '2023-04-09', 'module': 'pre-A1', 'lesson_num': 8},
# {'user_id': 4861, 'date': '2023-04-16', 'module': 'pre-A1', 'lesson_num': 9},
# {'user_id': 4861, 'date': '2023-04-23', 'module': 'pre-A1', 'lesson_num': 10},
# {'user_id': 4861, 'date': '2023-04-23', 'module': 'pre-A1', 'lesson_num': 11},
# {'user_id': 4861, 'date': '2023-04-30', 'module': 'pre-A1', 'lesson_num': 12},
# {'user_id': 4861, 'date': '2023-05-07', 'module': 'pre-A1', 'lesson_num': 13}]
```

为了模拟我们的产品逻辑，我们将逐一处理用户事件，并在每个阶段检查客户是否符合评估的条件。

让我们讨论一下我们需要维护哪些变量，以便能够判断客户是否符合测试条件。为此，让我们回顾一下客户可能会进行测试的所有情况：

+   如果之前没有测试记录 -> 我们需要知道他们是否曾经通过过测试。

+   如果客户完成了模块并且距离上次测试已经超过一个月 **->** 我们需要知道最后一次测试的日期。

+   如果客户在三个月后返回 **->** 我们需要存储最后一课的日期。

为了能够检查所有这些标准，我们只需要使用两个变量：最后的测试日期（如果之前没有测试，则为`None`）和上次课程日期。此外，我们还需要存储所有生成的测试，以便稍后进行计算。让我们初始化所有变量。

```py
tmp_gen_tests = []
last_test_date = None
last_lesson_date = None
```

现在，我们需要按事件进行迭代并检查标准。

```py
for rec in user_events:
  pass
```

让我们从最初的测试开始，逐一检查我们的标准。在这种情况下，`last_test_date`将等于`None`。在"分配"测试之后，更新`last_test_date`变量是非常重要的。

```py
if last_test_date is None: # initial test
    last_test_date = rec['date']
    # TBD saving the test info
```

在已完成的模块的情况下，我们需要检查它是否是该模块的最后一课，并且是否已经过去了30天以上。

```py
if (rec['lesson_num'] == 100) and (days_diff(last_test_date, rec['date']) >= 30): 
    last_test_date = rec['date']
    # TBD saving the test info
```

最后一种情况是客户已经三个月没有使用我们的服务。

```py
if (days_diff(last_lesson_date, rec['date']) >= 30): 
    last_test_date = rec['date']
    # TBD saving the test info
```

此外，我们需要在每次迭代时更新`last_lesson_date`以保持准确性。

我们已经讨论了所有的构建块，现在可以将它们结合起来，进行所有客户的模拟。

```py
import tqdm
tmp_gen_tests = []

for user_id in tqdm.tqdm(sim_raw_df.user_id.unique()):
    # initialising variables
    last_test_date = None
    last_lesson_date = None

    for rec in sim_raw_df[sim_raw_df.user_id == user_id].to_dict('records'):
        # initial test
        if last_test_date is None: 
            last_test_date = rec['date']
            tmp_gen_tests.append(
                {
                    'user_id': rec['user_id'],
                    'date': rec['date'],
                    'trigger': 'initial test'
                }
            )
        # finish module
        elif (rec['lesson_num'] == 100) and (days_diff(last_test_date, rec['date']) >= 30): 
            last_test_date = rec['date']
            tmp_gen_tests.append(
                {
                    'user_id': rec['user_id'],
                    'date': rec['date'],
                    'trigger': 'finished module'
                })
        # reactivation
        elif (days_diff(last_lesson_date, rec['date']) >= 92):
            last_test_date = rec['date']
            tmp_gen_tests.append(
                {
                    'user_id': rec['user_id'],
                    'date': rec['date'],
                    'trigger': 'reactivation'
                })
        last_lesson_date = rec['date']
```

现在，我们可以汇总这些数据。由于我们再次使用的是去年的数据，我将按大约80%的年增长率调整这个数字，正如我们之前估算的那样。

```py
exist_model_upd_stats_df = exist_model_upd.pivot_table(
    index = 'date', columns = 'trigger', values = 'user_id', 
    aggfunc = 'nunique'
).fillna(0)

exist_model_upd_stats_df = exist_model_upd_stats_df\
    .map(lambda x: int(round(x * 1.8)))
```

我们为初始测试得到了相似的估算。在这个案例中，“初始测试”部分等于我们之前估算中新增需求和现有需求的总和。

![](../Images/bc6a32d76bbf1b88a6a9de80fac98f1d.png)

所以，观察其他部分要更有趣，因为它们会是我们之前计算的增量。我们可以看到每周大约有30到60个案例来自于5月开始的模块完成的客户。

几乎不会出现重新激活的情况。在我们的模拟中，我们每年总共得到了4个案例。

![](../Images/9c2a0f348fed3e36d1efb42886f7b790.png)

恭喜！现在问题已经解决，我们找到了一个很好的方法，可以让我们在没有高级数学的情况下，仅通过模拟就能做出精确的估算。你可以使用类似的方法。

> 你可以在[GitHub](https://github.com/miptgirl/miptgirl_medium/blob/main/simulations/student_activities_model.ipynb)上找到这个示例的完整代码。

# 摘要

让我快速回顾一下今天讨论的内容：

+   计算机模拟的主要思想是基于你的数据进行模仿。

+   在许多情况下，你可以将问题的框架从预测未来转变为使用你已经拥有的数据，并模拟你感兴趣的过程。因此，这种方法非常强大。

+   在本文中，我们通过一个端到端的示例介绍了情景估算。我们展示了如何构建复杂问题，并将其拆分成更多定义明确的问题。我们还学会了如何处理约束并计划逐步推出。

> 非常感谢你阅读本文。如果你有任何后续问题或评论，请在评论区留言。

# 参考文献

*除非另有说明，所有图像均由作者制作。*
