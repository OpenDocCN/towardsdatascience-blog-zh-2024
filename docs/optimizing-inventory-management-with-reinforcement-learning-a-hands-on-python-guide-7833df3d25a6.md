# 使用强化学习优化库存管理：一个实用的Python指南

> 原文：[https://towardsdatascience.com/optimizing-inventory-management-with-reinforcement-learning-a-hands-on-python-guide-7833df3d25a6?source=collection_archive---------3-----------------------#2024-10-03](https://towardsdatascience.com/optimizing-inventory-management-with-reinforcement-learning-a-hands-on-python-guide-7833df3d25a6?source=collection_archive---------3-----------------------#2024-10-03)

## 一份关于如何在Python中应用Q学习方法以优化库存管理和降低成本的完整指南

[](https://medium.com/@peymankor?source=post_page---byline--7833df3d25a6--------------------------------)[![Peyman Kor](../Images/33f92f508120a56ebcc05c2aca7be3c4.png)](https://medium.com/@peymankor?source=post_page---byline--7833df3d25a6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7833df3d25a6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7833df3d25a6--------------------------------) [Peyman Kor](https://medium.com/@peymankor?source=post_page---byline--7833df3d25a6--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7833df3d25a6--------------------------------) ·13分钟阅读·2024年10月3日

--

![](../Images/81c435aa8f9c4f70d2abfcefea46f469.png)

图片由 [Petrebels](https://unsplash.com/@petrebels?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 库存管理 — 我们解决的是什么问题？

假设你在管理一家自行车店。每天，你需要决定从供应商那里订购多少辆自行车。如果你订购太多，就会产生高额的库存持有成本（存储自行车的费用）；如果订购太少，你可能会错失潜在的销售机会。在这里，挑战在于制定一个（订购）策略，能够最佳地平衡这些权衡。库存管理在多个行业中至关重要，其目标是确定最佳的定期订货数量，以最大化盈利。

**为什么选择强化学习来进行库存管理？**

之前，我们讨论过使用动态规划（DP）和马尔可夫决策过程（MDP）来解决这个问题 [这里](https://medium.com/towards-artificial-intelligence/inventory-optimization-with-dynamic-programming-in-less-than-100-lines-of-python-code-ab1cc58ef34c)。然而，DP方法需要一个完整的环境模型（在这种情况下，我们需要知道需求的概率分布），而这一点可能并不总是可用或实际可行的。

> 这里介绍了强化学习（RL）方法，它通过遵循“数据驱动”的方法克服了这个挑战。

目标是构建一个“数据驱动”的代理，通过与环境（不确定性）互动来学习最佳策略（订购多少）。RL方法去除了对环境模型先验知识的需求。本文探索了RL方法，特别是Q学习，来寻找最优的库存策略。

# 如何框定库存管理问题？

在深入了解Q学习方法之前，了解库存管理问题的基础非常重要。从本质上讲，库存管理是一个顺序决策问题，今天做出的决策会影响明天的结果和可用的选择。让我们分解这个问题的关键要素：*状态*，*不确定性*和*重复决策*。

**状态**：当前的情况是什么？

在自行车店的背景下，状态代表当前关于库存的情况。它由两个关键组成部分定义：

α（Alpha）：你目前店内的自行车数量。（称为现有库存）

β（Beta）：你昨天订购的自行车，预计明天早上到达（*36小时交货时间*）。这些自行车仍在运输途中。（称为在途库存）

一起，(α,β) 形成了状态，提供了在任何给定时刻你库存状态的快照。

**不确定性**：可能会发生什么？

该问题中的不确定性来源于每天自行车需求的随机性。你无法确切知道有多少客户会进店并要求自行车，这使得预测确切需求变得具有挑战性。

**决策**：你每天应该订购多少辆自行车？

作为自行车店的老板，你每天都面临一个重复的决策：你应该从供应商那里订购多少辆自行车？你的决策需要考虑到当前库存的状态（α,β），以及第二天客户需求的不确定性。

管理自行车店库存的典型24小时周期如下所示：

6 PM: 观察当前库存的状态 St:(α,β)。(**状态**)

6 PM: 做出决定，确定要订购多少辆新自行车。(**决策**)

6 AM: 收到36小时前订购的自行车。

8 AM: 开店迎接顾客。

8 AM — 6 PM: 一整天感受客户需求。(**不确定性**)

6 PM: 关店并准备进入下一个周期。

下图展示了库存管理过程的图示：

![](../Images/0c833c676bd2f78f9574bc6accf7850c.png)

一个典型的24小时库存管理周期 — 图片来源：作者

# 什么是强化学习？

强化学习（RL）是一种数据驱动的方法，侧重于学习如何通过一系列决策（遵循策略）来最大化累积奖励。它类似于人类和动物通过试错来学习采取什么行动。在库存管理的背景下，RL可用于学习优化的订货策略，从而最小化库存管理的总成本。

强化学习方法的关键组件是：

**代理商**：与环境交互的决策者。

**环境**：代理商与之交互的外部系统。在此案例中，环境是随机的客户需求。

**状态**：环境的当前情况或快照。

**动作**：代理商做出的决策或选择。

**奖励**：反馈信号，告诉代理商其表现如何。

代理商（决策者）的目标是学习最优策略，该策略是从状态到动作的映射，可以最大化累计奖励。

> 在库存管理的背景下，策略告诉代理商根据当前库存状态和客户需求的不确定性每天应该订购多少辆自行车。

# 实现库存优化问题的强化学习

**Q学习**是一种无模型的强化学习算法，它学习在任何给定状态下选择最优动作的策略。与需要完整环境模型的动态规划方法不同，Q学习通过与环境的交互（这里是指不确定性和它获得的奖励）直接学习，更新Q表。

**Q学习的关键组件**

在我们的案例中，代理商是决策者（自行车店老板），而环境是客户需求。状态由当前库存水平(alpha, beta)表示，动作是订购多少辆自行车。**奖励是与持有库存和错失销售相关的成本**。Q表是一个存储每个状态-动作对的预期未来奖励的表格。

**Q表的初始化**

在本工作中，Q表被初始化为名为Q的字典。状态通过元组(alpha, beta)表示，其中：alpha是库存中的物品数量（现有库存）。beta是已订购物品的数量（待订购库存）。

动作是每个状态下可以采取的*可能库存订购数量*。对于每个状态(alpha, beta)，可能的动作取决于库存中剩余空间的大小（剩余容量 = 库存容量 — (alpha + beta)）。限制条件是订购的物品数量不能超过库存的剩余容量。

Q值的示意设计如下图所示：

![](../Images/06e67d083e0092ae4d959b46d288342d.png)

Q字典的示意设计如下所示 — 图片来源：作者

Q字典可以初始化为：

```py
def initialize_Q(self):
    # Initialize the Q-table as a dictionary
    Q = {}
    for alpha in range(self.user_capacity + 1):
        for beta in range(self.user_capacity + 1 - alpha):
        state = (alpha, beta)
        Q[state] = {}
        max_action = self.user_capacity - (alpha + beta)
        for action in range(max_action + 1):
            Q[state][action] = np.random.uniform(0, 1)  # Small random values
    return Q
```

正如上述代码所示，Q值（Q[state][action]）被初始化为较小的随机值，以鼓励探索。

# Q学习算法

Q学习方法根据来自环境的奖励（在此是与环境的交互）更新状态-动作对的表格。以下是该算法的三个步骤：

![](../Images/b271d953abb3487d58b4688cb271b868.png)

Q学习方程 — 图片来源：作者

其中，s 是当前状态，a 是所采取的行动，s' 是下一个状态，( α ) 是学习率，( γ ) 是折扣因子。

我们将公式拆解，并在下面分成三部分重新写出：

![](../Images/9b31dd86a62fd52609a518a0ee87276d.png)

Q 学习方程 — 图片来源：作者

上述公式的 Python 代码转换如下：

```py
def update_Q(self, state, action, reward, next_state):
        # Update the Q-table based on the state, action, reward, and next state
        best_next_action = max(self.Q[next_state], key=self.Q[next_state].get)

        # reward + gamma * Q[next_state][best_next_action]
        td_target = reward + self.gamma * self.Q[next_state][best_next_action]

        # td_target - Q[state][action]
        td_error = td_target - self.Q[state][action]

        # Q[state][action] = Q[state][action] + alpha * td_error
        self.Q[state][action] += self.alpha * td_error
```

在上述函数中，每一行的等效方程已经作为注释显示在每一行上方。

# 在 Q 学习中模拟过渡和奖励以进行库存优化

当前状态由一个元组 (alpha, beta) 表示，其中：alpha 是当前的现有库存（库存中的物品数量），beta 是当前的在订库存（已订购但尚未收到的物品数量），init_inv 通过将 alpha 和 beta 相加来计算总的初始库存。

接下来，我们需要使用泊松分布模拟顾客需求，lambda 值为“self.poisson_lambda”。这里，需求表现出顾客需求的随机性：

```py
alpha, beta = state
init_inv = alpha + beta
demand = np.random.poisson(self.poisson_lambda)
```

**注意**：泊松分布被用来模拟需求，这是建模随机事件（如顾客到达）时的常见选择。然而，我们可以使用历史需求数据或与环境的实时交互来训练模型。从本质上讲，强化学习就是从数据中学习，它不需要先验的模型知识。

现在，"下一个 alpha"（现有库存）可以表示为 max(0, init_inv - demand)。这意味着如果需求大于初始库存，那么新的 alpha 将为零；如果不大于，则为 init_inv - demand。

**成本**分为两部分。**持有成本**：通过将商店中的自行车数量与单位持有成本相乘来计算。然后，我们有另一个成本，即**缺货成本**。这是我们需要支付的错失需求的成本。这两部分构成了我们尝试通过强化学习方法最大化的“奖励”。（更好的表述是我们希望最小化成本，因此我们最大化奖励）。

```py
new_alpha = max(0, init_inv - demand)
holding_cost = -new_alpha * self.holding_cost
stockout_cost = 0

if demand > init_inv:

    stockout_cost = -(demand - init_inv) * self.stockout_cost

reward = holding_cost + stockout_cost
next_state = (new_alpha, action)
```

## Q 学习中的探索 — 利用

在 Q 学习方法中选择行动涉及一定程度的探索，以便全面了解 Q 表中所有状态的 Q 值。为此，在每次选择行动时，有一个 epsilon 的概率我们采取探索方法，并“随机”选择一个行动，而在 1-ϵ 的概率下，我们从 Q 表中选择最优的行动。

```py
def choose_action(self, state):

        # Epsilon-greedy action selection
    if np.random.rand() < self.epsilon:

          return np.random.choice(self.user_capacity - (state[0] + state[1]) + 1)

    else:

        return max(self.Q[state], key=self.Q[state].get)
```

## 训练 RL 代理

RL 代理的训练通过“train”函数完成，具体步骤如下：首先，我们需要初始化 Q（空字典结构）。然后，在每一批次中收集经验（self.batch.append((state, action, reward, next_state))），并在每批次结束时更新 Q 表（self.update_Q(self.batch)）。每批次的最大回合数被限制为“max_actions_per_episode”。回合数是指代理与环境互动以学习最优策略的次数。

每个回合从随机分配的状态开始，当动作数量低于max_actions_per_episode时，批次的数据收集继续进行。

```py
def train(self):

        self.Q = self.initialize_Q()  # Reinitialize Q-table for each training run

        for episode in range(self.episodes):
            alpha_0 = random.randint(0, self.user_capacity)
            beta_0 = random.randint(0, self.user_capacity - alpha_0)
            state = (alpha_0, beta_0)
            #total_reward = 0
            self.batch = []  # Reset the batch at the start of each episode
            action_taken = 0
            while action_taken < self.max_actions_per_episode:
                action = self.choose_action(state)
                next_state, reward = self.simulate_transition_and_reward(state, action)
                self.batch.append((state, action, reward, next_state))  # Collect experience
                state = next_state
                action_taken += 1

            self.update_Q(self.batch)  # Update Q-table using the batch
```

# 示例案例和结果

这个示例案例展示了如何将上述所有代码组合在一起，并查看Q学习智能体如何学习库存管理的最优策略。在这里，*user_capicty*（存储容量）为10，表示库存可以容纳的物品总数（容量）。然后，*poisson_lambda*是需求分布中的λ值，其值为4。持有成本为8，表示每晚将一个物品保留在库存中的成本，而缺货成本则是失去需求的成本（假设当天有顾客需要该物品，而你却没有该物品在库存中）为10。*gamma*值小于1，用于在方程中折扣未来奖励（0.9），其中*alpha*（学习率）为0.1。*epsilon*项用于控制探索-开发的困境。回合数为1000，每个批次包含1000个（每回合的最大动作数）。

```py
# Example usage:
user_capacity = 10
poisson_lambda = 4
holding_cost = 8
stockout_cost = 10
gamma = 0.9
alpha = 0.1
epsilon = 0.1
episodes = 1000
max_actions_per_episode = 1000
```

定义了这些初始参数后，我们可以定义ql Python类，然后使用该类进行训练，再通过模块“get_optimal_policy()”获取最优策略。

```py
# Define the Class
ql = QLearningInventory(user_capacity, poisson_lambda, holding_cost, stockout_cost, gamma, 
                        alpha, epsilon, episodes, max_actions_per_episode)

# Train Agent
ql.train()

# Get the Optimal Policy
optimal_policy = ql.get_optimal_policy()
```

**结果**

现在我们已经得到了通过Q学习方法找到的策略，我们可以将结果可视化并查看其表现。x轴是状态，是（alpha，beta）的元组，y轴是Q学习在每个状态下找到的“订单数量”。

![](../Images/6b23e9e1d2d2dd54cf9f112b6b4ab5be.png)

*Q学习策略下每个状态（x轴）对应的订单数量（y轴） — 图片来源：作者*

通过查看图表，可以得到几个启示。首先，当我们向右移动时，可以看到订单数量减少。当我们向右移动时，alpha值增加（现有库存），这意味着我们需要“订购”更少，因为现有的库存可以满足需求。其次，当alpha保持不变时，随着beta的增加，我们减少了新地点的订单数量。这可以理解为，当“我们有更多的物品在‘订购中’时”，我们不需要增加订单。

**将Q学习策略与基准策略进行比较**

现在我们使用Q学习来找到策略（在给定状态下订购多少物品），我们可以将其与基准策略（一个简单的策略）进行比较。基准策略就是“按政策订购”，这意味着你查看现有库存和正在订购的库存，然后订购到“满足目标水平”。我们可以在这里编写简单的Python代码来实现这个策略：

```py
# Create a simple policy
def order_up_to_policy(state, user_capacity, target_level):
    alpha, beta = state
    max_possible_order = user_capacity - (alpha + beta)
    desired_order = max(0, target_level - (alpha + beta))
    return min(max_possible_order, desired_order)
```

在代码中，**target_level**是我们希望订购的库存目标值。如果target_level = user_capacity，那么我们只是为了填满库存。首先，我们可以比较这些不同方法的策略。对于每个状态，按照简单策略和Q-learning策略，订购的“数量”会是多少？在下图中，我们绘制了两种策略的比较。

![](../Images/f90efc18304e79a30955c981f2b915f0.png)

比较Q-Learning和简单策略之间的订购策略，针对每个状态 — 图片来源：作者

简单策略只是按照一定的顺序进行订购，以满足库存需求，而Q-learning策略的订购量通常低于简单策略的订购量。

> **这可以归因于“poisson_lambda”在这里是4，意味着需求远低于库存容量=10，因此订购“高数量的自行车”并不是最优选择，因为它有很高的持有成本。**

我们还可以比较在应用这两种策略时，你能够获得的总累计奖励。为此，我们可以使用*test_policy*函数，该函数是“QLearningInventory”中特别设计用来评估策略的：

```py
def test_policy(self, policy, episodes):
        """
        Test a given policy on the environment and calculate the total reward.

        Args:
            policy (dict): A dictionary mapping states to actions.
            episodes (int): The number of episodes to simulate.

        Returns:
            float: The total reward accumulated over all episodes.
        """
        total_reward = 0
        alpha_0 = random.randint(0, self.user_capacity)
        beta_0 = random.randint(0, self.user_capacity - alpha_0)
        state = (alpha_0, beta_0)  # Initialize the state

        for _ in range(episodes):

            action = policy.get(state, 0)
            next_state, reward = self.simulate_transition_and_reward(state, action)
            total_reward += reward
            state = next_state

        return total_reward
```

该函数的工作方式是，它从一个新的状态（state = (alpha_0, beta_0)）开始，然后根据该状态从策略中获得动作（订购数量），执行操作并查看奖励和下一个状态，过程继续进行，直到达到总的回合数，同时收集总奖励。

![](../Images/c3d1c92a43d76dc68bb3566c90a66491.png)

管理库存的总成本，遵循Q-Learning策略和简单策略 — 图片来源：作者

上面的图表比较了遵循“Q-Learning”和“简单策略”时，管理库存的总成本。目标是最小化运行库存的成本。由于我们模型中的“奖励”代表了这个成本，因此我们将总成本设置为-总奖励。

> 使用Q-Learning策略运行库存将导致比简单策略更低的成本。

# GitHub中的代码

本博客的完整代码可以在GitHub仓库中找到，链接：[这里](https://github.com/Peymankor/medium_blogs/blob/main/2024/08-Aug/RL-Inventory/main.py)。

# 总结和主要收获

在这篇文章中，我们讨论了如何使用强化学习（特别是Q-Learning）来优化库存管理。我们开发了一个Q-learning算法，通过与环境（不确定性）的互动来学习最优的订购策略。在这里，环境是客户的“随机”需求（自行车买家），状态是当前的库存状态（alpha, beta）。Q-learning算法能够学习到最优策略，从而最小化库存管理的总成本。

**主要收获**

1.  **Q-Learning**：Q-Learning是一种无模型的强化学习算法，可以在不需要完整环境模型的情况下找到最优库存策略。

1.  **状态表示**：库存管理中的状态由当前手头库存和订购库存表示，状态 = (α, β)。

1.  **成本降低**：我们可以看到，相比简单的按容量订购的策略，Q-learning策略能够带来更低的成本。

1.  **灵活性**：Q-learning方法非常灵活，可以应用于我们有历史需求数据的情况，或者我们可以与环境互动，学习最优策略。

1.  **数据驱动决策**：正如我们所展示的，强化学习（RL）方法不需要任何关于环境模型的先验知识，因为它是从数据中学习的。

# 参考文献

[1] A. Rao, T. Jelvis，《强化学习基础与金融应用》（2022）。

[2] S. Sutton, A. Barto，《强化学习：导论》（2018）。

[3] W. B. Powell，《顺序决策分析与建模：用Python建模》（2022）。

[4] R. B. Bratvold，《做出正确决策》（2010）。

# 在你离开之前！🦸🏻‍♀️

1.  如果你觉得这篇文章有价值，请在[Medium](https://medium.com/@peymankor)和[Linkedin](https://www.linkedin.com/in/peyman-kor/)上关注我，及时获取我的最新文章/写作。

1.  给我的文章点个赞，50次，那真的能帮我很多，并将这篇文章推送给更多人。👏
