# 理解 DDPG：解决连续动作控制挑战的算法

> 原文：[https://towardsdatascience.com/understanding-ddpg-the-algorithm-that-solves-continuous-action-control-challenges-742c67e0783a?source=collection_archive---------2-----------------------#2024-12-11](https://towardsdatascience.com/understanding-ddpg-the-algorithm-that-solves-continuous-action-control-challenges-742c67e0783a?source=collection_archive---------2-----------------------#2024-12-11)

## 发现 DDPG 如何解决连续动作控制难题，释放人工智能驱动的医疗机器人的可能性。

[](https://medium.com/@sirinebhouri?source=post_page---byline--742c67e0783a--------------------------------)[![Sirine Bhouri](../Images/ae904be69cb3ca9bb39185aaa7be4233.png)](https://medium.com/@sirinebhouri?source=post_page---byline--742c67e0783a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--742c67e0783a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--742c67e0783a--------------------------------) [Sirine Bhouri](https://medium.com/@sirinebhouri?source=post_page---byline--742c67e0783a--------------------------------)

·发表在 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--742c67e0783a--------------------------------) ·阅读 10 分钟·2024 年 12 月 11 日

--

想象一下你在外科手术中控制机械手臂。离散动作可能包括：

+   **向上移动,**

+   **向下移动**,

+   **抓取**，或

+   **发布**

这些是清晰、直接的命令，在简单场景中易于执行。

但是如何执行精细的动作，比如：

+   **将手臂移动 0.5 毫米以避免损伤组织**,

+   **施加 3N 的力以压缩组织**，或

+   **将手腕旋转 15° 调整切口角度**？

在这些情况下，你需要的不仅仅是选择一个动作 —— 你必须决定*需要多少*的那个动作。这就是**连续动作空间**的世界，也是深度确定性策略梯度 **(DDPG)** 的闪亮之处！

传统方法如深度 Q 网络（**DQN**）在离散动作方面表现良好，但在连续动作方面表现不佳。而确定性策略梯度 **(DPG)** 则解决了这个问题，但面临着探索不足和不稳定性的挑战。**DDPG** 首次在 T P. Lillicrap 等人的[论文](https://arxiv.org/pdf/1509.02971v6)中**结合了** **DPG** 和 **DQN** 的**优势**，以**提高**在**连续动作空间**环境中的**稳定性**和**性能**。

在这篇文章中，我们将讨论 DDPG 背后的理论和架构，看看在 Python 上的实现，评估其性能（通过在 MountainCarContinuous 游戏上测试），并简要讨论 DDPG 如何在生物工程领域中使用。

# **DDPG 架构**

与**DQN**不同，后者评估每一个可能的状态-动作对来寻找最佳动作（由于连续空间中组合的无限性，这在连续空间中不可能实现），**DPG**使用**行动者-评论家架构**。行动者学习一种策略，将状态直接映射到动作，避免了穷举搜索，专注于学习每个状态下的最佳动作。

然而，DPG面临两个主要挑战：

1.  它是一个**确定性**算法，**限制了对动作空间的探索**。

1.  由于学习过程中的**不稳定性**，它无法有效使用神经网络。

**DDPG**通过引入**探索噪声**（通过奥恩斯坦-乌伦贝克过程）并通过**批量归一化**和DQN技术（如**重放缓冲区**和**目标网络**）来改进DPG，从而稳定训练过程。

通过这些改进，DDPG非常适合训练连续动作空间中的智能体，例如在生物工程应用中控制机器人系统。

现在，让我们来探讨一下DDPG模型的关键组件吧！

**行动者-评论家框架**

+   **行动者（策略网络）**：告诉智能体在给定的状态下应该采取什么动作。网络的参数（即权重）由θμ表示。

![](../Images/6ae607f0b6394ea2af1bc4e008128a96.png)

**提示！** 把行动者网络想象成决策者：它将当前状态映射到一个单一的动作。

+   **评论家（Q值网络）**：通过估计该状态-动作对的Q值来评估行动者所采取的动作的优劣。

![](../Images/095e9c51285694906190157f570dd843.png)

**提示！** 把评论家网络想象成评估者，它为每个动作分配一个质量分数，并帮助改进行动者的策略，确保其在每个给定的状态下确实生成最佳动作。

**注意**！评论家将使用估计的Q值来做两件事：

1.  改进行动者的策略（行动者策略更新）。

行动者的目标是调整其参数（θμ），使其输出的动作最大化评论家的Q值。

为了做到这一点，行动者需要理解选定的动作a如何影响评论家的Q值，以及其内部参数如何影响其策略，这通过这个策略梯度方程来完成（它是从小批量中计算出的所有梯度的均值）：

![](../Images/1ef06bfb2a896ecd595c0da22b4ff7e1.png)

**2.** 通过最小化下面的损失函数来改进其自身的网络（评论家Q值网络更新）。

![](../Images/eb44da31f91612d3172e85cec236c8bb.png)

其中N是小批量中采样的经验数量，y_i是目标Q值，计算方法如下。

![](../Images/c534b1294b3cfbefd8bf06b9da2bb423.png)

## **重放缓冲区**

当智能体探索环境时，过去的经验（状态、动作、奖励、下一个状态）作为元组（s，a，r，s′）存储在重放缓冲区中。在训练过程中，这些经验中的一些会随机组成小批量进行训练。

**问题！** 重放缓冲区*如何*减少不稳定性？

通过随机抽样经验，回放缓冲区打破了连续样本之间的相关性，减少了偏差，并导致更稳定的训练。

## **目标网络**

目标网络是演员和评论家的缓慢更新副本。它们提供稳定的Q值目标，防止快速变化，并确保平稳、一致的更新。

![](../Images/ad2698b89ffe352ffc56ed2573b32f88.png)

**问题！** 目标网络是如何*实际*减少不稳定性的？

如果没有**评论家目标网络**，目标Q值将直接从评论家Q值网络计算，而评论家Q值网络是持续更新的。这会导致目标Q值在每一步发生变化，造成“移动目标”问题。结果，评论家不断追逐一个不断变化的目标，导致训练不稳定。

此外，由于**演员**依赖于评论家的反馈，一个网络中的错误可能会放大另一个网络中的错误，形成一个相互依赖的稳定性问题。

通过引入**目标网络**，并使用软更新规则逐步更新它们，我们确保目标Q值保持更一致，减少突变变化，从而提高学习的稳定性。

## **批量归一化**

批量归一化对神经网络每一层的输入进行标准化，确保均值为零，方差为单位。

**问题！** 批量归一化是如何*实际*减少不稳定性的？

从回放缓冲区抽取的样本可能与实时数据的分布不同，从而导致网络更新时的不稳定性。

批量归一化确保输入的尺度一致，以防止由输入分布变化引起的更新不稳定。

## **探索噪声**

由于演员的策略是确定性的，训练过程中会向动作添加探索噪声，鼓励智能体尽可能多地探索动作空间。

![](../Images/f8d1d609db7533482e5a40ac6532ab59.png)

在DDPG论文中，作者使用了**奥恩斯坦-乌伦贝克过程**来生成时间相关噪声，以模拟现实世界的系统动态。

# DDPG伪代码：逐步拆解

![](../Images/67ae26120aeeafaba2fbe7b7b9d7ef7d.png)

伪代码摘自[http://arxiv.org/abs/1509.02971](http://arxiv.org/abs/1509.02971)（见‘参考文献’部分的参考文献1）

![](../Images/5490d989d60df6c77b8e3d32123600df.png)

图示由作者绘制

+   **定义演员和评论家网络**

```py
class Actor(nn.Module):
    """
    Actor network for the DDPG algorithm.
    """
    def __init__(self, state_dim, action_dim, max_action,use_batch_norm):
        """
        Initialise the Actor's Policy network.

        :param state_dim: Dimension of the state space
        :param action_dim: Dimension of the action space
        :param max_action: Maximum value of the action
        """
        super(Actor, self).__init__()
        self.bn1 = nn.LayerNorm(HIDDEN_LAYERS_ACTOR) if use_batch_norm else nn.Identity()
        self.bn2 = nn.LayerNorm(HIDDEN_LAYERS_ACTOR) if use_batch_norm else nn.Identity()

        self.l1 = nn.Linear(state_dim, HIDDEN_LAYERS_ACTOR)
        self.l2 = nn.Linear(HIDDEN_LAYERS_ACTOR, HIDDEN_LAYERS_ACTOR)
        self.l3 = nn.Linear(HIDDEN_LAYERS_ACTOR, action_dim)
        self.max_action = max_action

    def forward(self, state):
        """
        Forward propagation through the network.

        :param state: Input state
        :return: Action
        """

        a = torch.relu(self.bn1(self.l1(state)))
        a = torch.relu(self.bn2(self.l2(a)))
        return self.max_action * torch.tanh(self.l3(a))

class Critic(nn.Module):
    """
    Critic network for the DDPG algorithm.
    """
    def __init__(self, state_dim, action_dim,use_batch_norm):
        """
        Initialise the Critic's Value network.

        :param state_dim: Dimension of the state space
        :param action_dim: Dimension of the action space
        """
        super(Critic, self).__init__()
        self.bn1 = nn.BatchNorm1d(HIDDEN_LAYERS_CRITIC) if use_batch_norm else nn.Identity()
        self.bn2 = nn.BatchNorm1d(HIDDEN_LAYERS_CRITIC) if use_batch_norm else nn.Identity()
        self.l1 = nn.Linear(state_dim + action_dim, HIDDEN_LAYERS_CRITIC)

        self.l2 = nn.Linear(HIDDEN_LAYERS_CRITIC, HIDDEN_LAYERS_CRITIC)
        self.l3 = nn.Linear(HIDDEN_LAYERS_CRITIC, 1)

    def forward(self, state, action):
        """
        Forward propagation through the network.

        :param state: Input state
        :param action: Input action
        :return: Q-value of state-action pair
        """
        q = torch.relu(self.bn1(self.l1(torch.cat([state, action], 1))))
        q = torch.relu(self.bn2(self.l2(q)))
        return self.l3(q)
```

+   **定义回放缓冲区**

实现了一个ReplayBuffer类，用于存储和抽样前一节中讨论的过渡元组(s, a, r, s')，以支持小批量的离策略学习。

```py
class ReplayBuffer:
    def __init__(self, capacity):
        self.buffer = deque(maxlen=capacity)

    def push(self, state, action, reward, next_state, done):
        self.buffer.append((state, action, reward, next_state, done))

    def sample(self, batch_size):
        return random.sample(self.buffer, batch_size)

    def __len__(self):
        return len(self.buffer)
```

+   **定义OU噪声类**

添加了一个OUNoise类来生成探索噪声，帮助智能体更有效地探索动作空间。

```py
"""
Taken from https://github.com/vitchyr/rlkit/blob/master/rlkit/exploration_strategies/ou_strategy.py
"""
class OUNoise(object):
    def __init__(self, action_space, mu=0.0, theta=0.15, max_sigma=0.3, min_sigma=0.3, decay_period=100000):
        self.mu           = mu
        self.theta        = theta
        self.sigma        = max_sigma
        self.max_sigma    = max_sigma
        self.min_sigma    = min_sigma
        self.decay_period = decay_period
        self.action_dim   = action_space.shape[0]
        self.low          = action_space.low
        self.high         = action_space.high
        self.reset()

    def reset(self):
        self.state = np.ones(self.action_dim) * self.mu

    def evolve_state(self):
        x  = self.state
        dx = self.theta * (self.mu - x) + self.sigma * np.random.randn(self.action_dim)
        self.state = x + dx
        return self.state

    def get_action(self, action, t=0): 
        ou_state = self.evolve_state()
        self.sigma = self.max_sigma - (self.max_sigma - self.min_sigma) * min(1.0, t / self.decay_period)
        return np.clip(action + ou_state, self.low, self.high)
```

+   **定义DDPG智能体**

定义了一个DDPG类，它封装了智能体的行为：

1.  初始化：创建演员和评论家网络，以及它们的目标副本和回放缓冲区。

```py
class DDPG():
    """
    Deep Deterministic Policy Gradient (DDPG) agent.
    """
    def __init__(self, state_dim, action_dim, max_action,use_batch_norm):
        """
        Initialise the DDPG agent.

        :param state_dim: Dimension of the state space
        :param action_dim: Dimension of the action space
        :param max_action: Maximum value of the action
        """
        # [STEP 0]
        # Initialise Actor's Policy network
        self.actor = Actor(state_dim, action_dim, max_action,use_batch_norm)
        # Initialise Actor target network with same weights as Actor's Policy network
        self.actor_target = Actor(state_dim, action_dim, max_action,use_batch_norm)
        self.actor_target.load_state_dict(self.actor.state_dict())
        self.actor_optimizer = optim.Adam(self.actor.parameters(), lr=ACTOR_LR)

        # Initialise Critic's Value network
        self.critic = Critic(state_dim, action_dim,use_batch_norm)
        # Initialise Crtic's target network with same weights as Critic's Value network
        self.critic_target = Critic(state_dim, action_dim,use_batch_norm)
        self.critic_target.load_state_dict(self.critic.state_dict())
        self.critic_optimizer = optim.Adam(self.critic.parameters(), lr=CRITIC_LR)

        # Initialise the Replay Buffer
        self.replay_buffer = ReplayBuffer(BUFFER_SIZE)
```

2\. 动作选择：`select_action`方法根据当前策略选择动作。

```py
 def select_action(self, state):
        """
        Select an action given the current state.

        :param state: Current state
        :return: Selected action
        """
        state = torch.FloatTensor(state.reshape(1, -1))
        action = self.actor(state).cpu().data.numpy().flatten()
        return action
```

+   3\. 训练：`train`方法定义了如何使用来自重放缓冲区的经验更新网络。

**注意！** 由于论文介绍了使用目标网络和批量归一化来提高稳定性，我设计了`train`方法，允许我们切换这些方法的开关。这让我们可以比较智能体在有这些方法和没有这些方法时的表现。具体实现请见下面的代码。

```py
 def train(self, use_target_network,use_batch_norm):
        """
        Train the DDPG agent.

        :param use_target_network: Whether to use target networks or not
        :param use_batch_norm: Whether to use batch normalisation or not
        """
        if len(self.replay_buffer) < BATCH_SIZE:
            return

        # [STEP 4]. Sample a batch from the replay buffer
        batch = self.replay_buffer.sample(BATCH_SIZE)
        state, action, reward, next_state, done = map(np.stack, zip(*batch))

        state = torch.FloatTensor(state)
        action = torch.FloatTensor(action)
        next_state = torch.FloatTensor(next_state)
        reward = torch.FloatTensor(reward.reshape(-1, 1))
        done = torch.FloatTensor(done.reshape(-1, 1))

        # Critic Network update #
        if use_target_network:
            target_Q = self.critic_target(next_state, self.actor_target(next_state))
        else:
            target_Q = self.critic(next_state, self.actor(next_state))

        # [STEP 5]. Calculate target Q-value (y_i)
        target_Q = reward + (1 - done) * GAMMA * target_Q
        current_Q = self.critic(state, action)
        critic_loss = nn.MSELoss()(current_Q, target_Q.detach())

        # [STEP 6]. Use gradient descent to update weights of the Critic network 
        # to minimise loss function
        self.critic_optimizer.zero_grad()
        critic_loss.backward()
        self.critic_optimizer.step()

        # Actor Network update #
        actor_loss = -self.critic(state, self.actor(state)).mean()

        # [STEP 7]. Use gradient descent to update weights of the Actor network
        # to minimise loss function and maximise the Q-value => choose the action that yields the highest cumulative reward
        self.actor_optimizer.zero_grad()
        actor_loss.backward()
        self.actor_optimizer.step()

        # [STEP 8]. Update target networks
        if use_target_network:
            for param, target_param in zip(self.critic.parameters(), self.critic_target.parameters()):
                target_param.data.copy_(TAU * param.data + (1 - TAU) * target_param.data)

            for param, target_param in zip(self.actor.parameters(), self.actor_target.parameters()):
                target_param.data.copy_(TAU * param.data + (1 - TAU) * target_param.data)
```

+   **训练DDPG智能体**

将所有定义的类和方法整合在一起，我们可以训练DDPG智能体。我的`train_dppg`函数遵循伪代码和DDPG模型图的结构。

**提示：** 为了便于理解，我已经在每个代码部分标注了与伪代码和图表中对应步骤的编号。希望这能有所帮助！ :)

```py
def train_ddpg(use_target_network, use_batch_norm, num_episodes=NUM_EPISODES):
    """
    Train the DDPG agent.

    :param use_target_network: Whether to use target networks
    :param use_batch_norm: Whether to use batch normalization
    :param num_episodes: Number of episodes to train
    :return: List of episode rewards
    """
    agent = DDPG(state_dim, action_dim, 1,use_batch_norm)

    episode_rewards = []
    noise = OUNoise(env.action_space)

    for episode in range(num_episodes):
        state= env.reset()
        noise.reset()
        episode_reward = 0
        done = False
        step=0
        while not done:
            action_actor = agent.select_action(state)
            action = noise.get_action(action_actor,step) # Add noise for exploration
            next_state, reward, done,_= env.step(action)
            done = float(done) if isinstance(done, (bool, int)) else float(done[0])
            agent.replay_buffer.push(state, action, reward, next_state, done)

            if len(agent.replay_buffer) > BATCH_SIZE:
                agent.train(use_target_network,use_batch_norm)

            state = next_state
            episode_reward += reward
            step+=1

        episode_rewards.append(episode_reward)

        if (episode + 1) % 10 == 0:
            print(f"Episode {episode + 1}: Reward = {episode_reward}")

    return agent, episode_rewards
```

# 性能与结果：评估DDPG的有效性

DDPG在连续动作空间中的有效性是在`MountainCarContinuous-v0`环境中进行测试的，其中智能体学习如何获得动量，将汽车驶上陡坡。结果表明，使用**目标网络**和**批量归一化**比其他配置能更快地收敛，获得更高的奖励，并且学习更加稳定。

![](../Images/3126a241c28e6b5fd6c59ec4cf1380f1.png)

图表由作者生成

![](../Images/5dafde30d56caffaaeb03e189a322450.png)

GIF由作者生成

**注意！** 你可以通过在我的[GitHub](https://github.com/sirine-b/DDPG)上运行代码来自己实现这一点，只需根据需要更改环境名称即可！

# DDPG在生物工程中的应用：解锁精确性与适应性

通过这篇博客文章，我们看到了DDPG是一个强大的算法，可以在具有连续动作空间的环境中训练智能体。通过结合DPG和DQN的技术，DDPG提高了探索性、稳定性和性能——这些都是机器人手术和生物工程应用中的关键因素。

想象一下，一位机器人外科医生，像**达芬奇系统**那样，使用DDPG来实时控制精细的运动，确保**精确调整**而不出错。通过DDPG，机器人可以将手臂的位置调整到毫米级别，在缝合时施加精确的力量，甚至在做出最佳切口时进行微小的手腕旋转。这样的**实时精确性**可以改变手术结果，减少恢复时间，并最小化人为错误。

但DDPG的潜力不仅仅局限于手术。它已经在推进生物工程，能够使机器人假肢和辅助设备复制人体肢体的自然运动（查看这篇超级有趣的[文章](https://www.tandfonline.com/doi/abs/10.1080/00207179.2023.2201644)!）。

现在我们已经介绍了DDPG背后的理论，是时候让你探索它的实现了。从简单的例子开始，逐步深入到更复杂的场景中吧！

# **参考文献**

1.  Lillicrap TP, Hunt JJ, Pritzel A, Heess N, Erez T, Tassa Y, 等人. 基于深度强化学习的连续控制 [Internet]. arXiv; 2019\. 可从以下网址获取: [http://arxiv.org/abs/1509.02971](http://arxiv.org/abs/1509.02971)
