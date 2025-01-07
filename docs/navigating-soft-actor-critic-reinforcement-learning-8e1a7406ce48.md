# 探索软演员评论家强化学习

> 原文：[https://towardsdatascience.com/navigating-soft-actor-critic-reinforcement-learning-8e1a7406ce48?source=collection_archive---------11-----------------------#2024-12-12](https://towardsdatascience.com/navigating-soft-actor-critic-reinforcement-learning-8e1a7406ce48?source=collection_archive---------11-----------------------#2024-12-12)

## 在生物工程背景下理解SAC强化学习的理论与实现

[](https://medium.com/@mo.abusadeh?source=post_page---byline--8e1a7406ce48--------------------------------)[![Mohammed AbuSadeh](../Images/2a7d5ce6964cdc76d9640b1a17ac707b.png)](https://medium.com/@mo.abusadeh?source=post_page---byline--8e1a7406ce48--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8e1a7406ce48--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8e1a7406ce48--------------------------------) [Mohammed AbuSadeh](https://medium.com/@mo.abusadeh?source=post_page---byline--8e1a7406ce48--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8e1a7406ce48--------------------------------) ·阅读时长10分钟·2024年12月12日

--

![](../Images/117e5ac1c7be96924a7201b5185fd59e.png)

由作者使用ChatGPT-4o生成的图像

# 介绍

强化学习（RL）研究领域在过去几年里取得了巨大进展。深度强化学习方法的使用，如近端策略优化（PPO）（Schulman，2017）和深度确定性策略梯度（DDPG）（Lillicrap，2015），使得智能体能够在高维环境中解决任务。然而，许多这些无模型的强化学习算法在训练过程中存在稳定性问题。这些挑战源于脆弱的收敛性、高方差的梯度估计、非常高的样本复杂度以及在连续动作空间中对超参数的敏感性。鉴于这些问题，有必要考虑一种新开发的强化学习算法，避免这些问题并扩展其在复杂现实世界问题中的适用性。这个新算法就是软演员评论家（SAC）深度强化学习网络。（Haarnoja，2018）

![](../Images/719d9f7573dce860add1e140432f1fdb.png)

软演员评论家网络的模型架构。图像来源于[(Du, 2023)](https://arxiv.org/abs/2301.03220)

SAC是一种离线策略（off-policy）Actor-Critic深度强化学习算法，旨在解决其前辈算法的稳定性和效率问题。SAC算法基于最大熵强化学习框架，目标是让网络的演员部分最大化预期奖励，同时最大化熵。它结合了离线策略更新和更稳定的随机Actor-Critic方法的表述。与PPO等在线策略方法不同，离线策略算法通过经验回放实现更快的学习和更好的样本效率，后者需要为每一步梯度更新收集新的样本。对于像PPO这样的在线策略方法，在学习过程中的每一步梯度更新都需要收集新的样本。使用随机策略和最大化熵的目的是通过鼓励行为中的更多随机性，促进算法的鲁棒性和探索性。此外，与PPO和DDPG不同，SAC使用了双Q网络、独立的演员网络和熵调整，以提高将离线学习与高维非线性函数逼近相结合时的稳定性和收敛性。

离线策略强化学习方法对改善患者生活的生物工程系统产生了广泛的影响。更具体地说，强化学习已被应用于诸如机器人手臂控制、药物递送方法以及最著名的去 novo药物设计等领域。（Svensson, 2024）Svensson等人使用了多种在线和离线框架及不同类型的回放缓冲区，以学习基于RNN的分子生成策略，该策略能有效作用于DRD2（一种多巴胺受体）。该论文指出，使用经验回放处理高、中、低评分分子在提高结构多样性和生成活性分子的数量方面产生了效果。回放缓冲区提高了训练代理的样本效率。他们还报告指出，使用离线策略方法，特别是SAC，有助于通过防止模式坍塌来促进结构多样性。

# 理论解释

SAC通过引入带有熵项的目标函数，使用“软”价值函数，**Η(π(a|s))**。因此，网络试图最大化终身奖励的预期回报和策略的熵。策略的熵被定义为一个随机变量的不可预测性，随着可能值范围的增大而增加。因此，新的熵正则化目标变为：

![](../Images/57384bbf7ccc74498e77313443dd16e2.png)

熵正则化目标

**α** 是一个温度参数，用于平衡探索与利用之间的关系。

在软值函数的实现中，我们的目标是最大化熵，因为算法会将相似 Q 值的动作分配相等的概率。最大化熵还有助于防止智能体选择利用近似 Q 值中的不一致性的动作。最终我们可以理解 SAC 如何通过允许网络进行更多探索，而不是对一类动作赋予过高的概率，从而改善脆弱性。这部分内容灵感来自 [Vaishak V.Kumar](https://medium.com/u/61d2676ad14?source=post_page---user_mention--8e1a7406ce48--------------------------------) 在《软演员-评论家解密》一文中对熵最大化的解释。

SAC 论文的作者讨论了，由于状态值函数逼近软值，因此实际上并不需要为策略训练单独的函数逼近器，因为它们根据以下公式与状态值相关联。然而，训练三个独立的逼近器能提供更好的收敛性。

![](../Images/8bb99c228fae7e880d122184030b8231.png)

软状态值函数

三个函数逼近器网络的特征如下：

+   **策略网络（Actor）：** 随机策略输出一组从高斯分布中采样的动作。策略参数通过最小化 Kullback-Leibler 散度来学习，公式如下：

![](../Images/79a5cd826f332f6b5fec21de2400c762.png)

最小化 KL 散度

KL 散度比较的是两个概率分布之间的相对熵或差异。因此，在该公式中，我们试图最小化策略函数与经过 Z 函数规范化的指数化 Q 函数之间分布的差异。由于目标密度函数是可微分的 Q 函数，因此我们对策略应用了重新参数化技巧，以减少方差的估计。

![](../Images/9b4eb81490bd4799501b1cea4cd7cba4.png)

重新参数化策略

ϵₜ 是一个从高斯分布中采样的向量，描述了噪声。

然后，策略目标被更新为以下表达式：

![](../Images/61de771ed85419f7cf722c7582dcd7c9.png)

策略目标

策略目标通过以下梯度估计来优化：

![](../Images/3247348d35c518e313d104bcdaef9c3d.png)

策略梯度估计器

+   **Q 网络（Critic）：** 包含两个 Q 值网络，用于估计状态-动作对的期望回报。我们通过使用以下软贝尔曼残差来最小化软 Q 函数的参数：

![](../Images/326b8933dff984f1c5106331d3507e28.png)

软 Q 函数目标

其中：

![](../Images/4333f2dc897ce235348f2991d1cb661a.png)

即时 Q 值

Soft Q-函数目标最小化网络Q值估计与即时Q值之间的平方差。即时Q值（Q hat）是通过当前状态-动作对的奖励与下一时间戳中目标值函数的折扣期望之和得到的。最终，目标通过以下给定的随机梯度估计进行优化：

![](../Images/8739f195eadfd70126df462e16384612.png)

随机梯度估计器

**目标值网络（Critic）：** 一个独立的Soft值函数，有助于稳定训练过程。Soft值函数逼近器通过以下方式最小化平方残差误差：

![](../Images/084c54806c06bf551bded6f9f54b245a.png)

Soft值函数目标

这个Soft值函数目标最小化值函数与Q值的期望加上策略函数**π**的熵之间的平方差。此目标的负对数部分描述了策略函数的熵。我们还知道，信息熵是通过负号来计算，从而输出正的熵值，因为概率值（介于0和1之间）的对数总是负数。类似地，目标通过以下表达式进行优化，使用无偏的梯度估计器：

![](../Images/94ecdcd4eacc2213b4ba1a9c2dcc2ef8.png)

无偏梯度估计器

# 代码实现

本文中实现的代码来自以下GitHub仓库（quantumiracle，2023年）：

[](https://github.com/quantumiracle/Popular-RL-Algorithms?source=post_page-----8e1a7406ce48--------------------------------) [## GitHub - quantumiracle/Popular-RL-Algorithms: PyTorch实现Soft Actor-Critic (SAC)…

### Soft Actor-Critic (SAC)、Twin Delayed DDPG (TD3)、Actor-Critic (AC/A2C)、Proximal Policy的PyTorch实现…

github.com](https://github.com/quantumiracle/Popular-RL-Algorithms?source=post_page-----8e1a7406ce48--------------------------------)

```py
pip install gymnasium torch
```

SAC依赖于使用连续动作空间的环境，因此所提供的仿真大多使用了机器人臂‘Reacher’环境，以及Gymnasium包中的Pendulum-v1环境。

Pendulum环境在另一个仓库中运行，该仓库实现了相同的算法，但使用了较少过时的库（MrSyee，2020年）：

[](https://github.com/MrSyee/pg-is-all-you-need?tab=readme-ov-file&source=post_page-----8e1a7406ce48--------------------------------) [## GitHub - MrSyee/pg-is-all-you-need: Policy Gradient is all you need! 一个详细的PG方法教程…

### Policy Gradient就是你所需要的！一个详细的PG方法教程。- MrSyee/pg-is-all-you-need

github.com](https://github.com/MrSyee/pg-is-all-you-need?tab=readme-ov-file&source=post_page-----8e1a7406ce48--------------------------------)

在网络架构方面，如*理论解释*中所提到，有三个主要组件：

**策略网络：** 实现一个高斯演员网络，计算动作分布的均值和对数标准差。

```py
class PolicyNetwork(nn.Module):
    def __init__(self, state_dim, action_dim, hidden_dim):
        super(PolicyNetwork, self).__init__()
        self.fc1 = nn.Linear(state_dim, hidden_dim)
        self.fc2 = nn.Linear(hidden_dim, hidden_dim)
        self.mean = nn.Linear(hidden_dim, action_dim)
        self.log_std = nn.Linear(hidden_dim, action_dim)

    def forward(self, state):
        x = F.relu(self.fc1(state))
        x = F.relu(self.fc2(x))
        mean = self.mean(x)
        log_std = torch.clamp(self.log_std(x), -20, 2)  # Limit log_std to prevent instability
        return mean, log_std
```

**软Q网络：** 估计给定状态-动作对下的期望未来回报，对于定义的最优策略而言。

```py
class SoftQNetwork(nn.Module):
    def __init__(self, state_dim, action_dim, hidden_dim):
        super(SoftQNetwork, self).__init__()
        self.fc1 = nn.Linear(state_dim + action_dim, hidden_dim)
        self.fc2 = nn.Linear(hidden_dim, hidden_dim)
        self.out = nn.Linear(hidden_dim, 1)

    def forward(self, state, action):
        x = torch.cat([state, action], dim=-1)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        return self.out(x)
```

**价值网络：** 估计状态值。

```py
class ValueNetwork(nn.Module):
    def __init__(self, state_dim, hidden_dim):
        super(ValueNetwork, self).__init__()
        self.fc1 = nn.Linear(state_dim, hidden_dim)
        self.fc2 = nn.Linear(hidden_dim, hidden_dim)
        self.out = nn.Linear(hidden_dim, 1)

    def forward(self, state):
        x = F.relu(self.fc1(state))
        x = F.relu(self.fc2(x))
        return self.out(x)
```

以下代码片段展示了更新与SAC算法相关的不同变量的关键步骤。首先，它从重放缓冲区采样一个批次进行经验回放。然后，在计算梯度之前，先将梯度初始化为零，以确保不会累积来自前一批次的梯度。接着执行反向传播，并在训练过程中更新网络的权重。最后，更新Q网络的目标值和损失值。这些步骤适用于所有三种方法。

```py
def update(batch_size, reward_scale, gamma=0.99, soft_tau=1e-2):
    # Sample a batch
    state, action, reward, next_state, done = replay_buffer.sample(batch_size)
    state, next_state, action, reward, done = map(lambda x: torch.FloatTensor(x).to(device), 
                                                  [state, next_state, action, reward, done])

    # Update Q-networks
    target_value = target_value_net(next_state)
    target_q = reward + (1 - done) * gamma * target_value
    q1_loss = F.mse_loss(soft_q_net1(state, action), target_q.detach())
    q2_loss = F.mse_loss(soft_q_net2(state, action), target_q.detach())

    soft_q_optimizer1.zero_grad()
    q1_loss.backward()
    soft_q_optimizer1.step()

    soft_q_optimizer2.zero_grad()
    q2_loss.backward()
    soft_q_optimizer2.step()

    # Update Value Network
    predicted_q = torch.min(soft_q_net1(state, action), soft_q_net2(state, action))
    value_loss = F.mse_loss(value_net(state), predicted_q - alpha * log_prob)
    value_optimizer.zero_grad()
    value_loss.backward()
    value_optimizer.step()

    # Update Policy Network
    new_action, log_prob, _, _, _ = policy_net.evaluate(state)
    policy_loss = (alpha * log_prob - predicted_q).mean()
    policy_optimizer.zero_grad()
    policy_loss.backward()
    policy_optimizer.step()

    # Soft Update Target Network
    for target_param, param in zip(target_value_net.parameters(), value_net.parameters()):
        target_param.data.copy_(soft_tau * param.data + (1 - soft_tau) * target_param.data)
```

最后，要运行sac.py文件中的代码，只需运行以下命令：

```py
python sac.py --train
python sac.py --test
```

# 结果与可视化

![](../Images/77207902ecc4b682d7a475496bd4b932.png)

训练“Reacher”机器人手臂，（由作者生成）

在两个环境中训练SAC智能体时，我注意到问题的行动空间会影响训练的效率和表现。事实上，当我在简单的摆动环境中训练智能体时，学习收敛得更快，且波动较小。然而，由于Reacher环境包含了更复杂的连续动作空间，算法虽然训练得相对不错，但奖励的巨大跳跃并没有像预期那样显著。Reacher环境的训练回合数是摆动环境的4倍。

![](../Images/6196e06d1570d595e76a5cff6916d1bf.png)

通过最大化奖励的学习表现（由作者生成）

下面的动作分布图显示了策略在训练过程中探索的多样化动作，直到它收敛到一个最优策略。像SAC这样的熵正则化算法的特点来自于探索性的增加。我们还可以注意到，峰值对应于具有高预期回报的动作值，这推动策略收敛到更具确定性的行为。

![](../Images/1231b79d7ed7b5a2561e80ac55eea9aa.png)

行动空间使用分布（由作者生成）

说到更具确定性的行为，我们观察到，熵在训练回合数上平均有所减少。然而，这种行为是可以预期的，因为我们希望最大化熵的唯一原因是鼓励更多的探索。更高的探索主要发生在训练过程的早期，目的是耗尽大多数可能的状态-动作对，从而获得更高的回报。

![](../Images/206dded1ba5479ad51d65ff6055f4164.png)

训练回合的熵评估（由作者生成）

# 结论

SAC算法是一种离策略强化学习框架，通过引入新的熵项，平衡了开发和探索。SAC算法的主要目标函数包括最大化期望回报和训练过程中的熵，这解决了传统框架中存在的许多问题。使用双Q网络和自动温度调节来应对高样本复杂性、脆弱的收敛性和复杂的超参数调整问题。SAC在连续控制任务领域中已经证明非常有效。关于动作分布和熵的结果表明，算法在早期训练阶段偏向于探索和多样化的动作采样。随着智能体的训练，它趋向于一个更具体的策略，减少了熵并达到了最优动作。因此，它已被有效地应用于生物工程中的各种领域，如机器人控制、药物发现和药物传递。未来的实现应着重于将该框架扩展到更复杂的任务并降低其计算复杂性。

# 参考文献

Lillicrap, T.P., Hunt, J.J., Pritzel, A., Heess, N., Erez, T., Tassa, Y., Silver, D. 和 Wierstra, D. (2015)。使用深度强化学习进行连续控制。[在线] arXiv.org。可用链接：[https://arxiv.org/abs/1509.02971.](https://arxiv.org/abs/1509.02971.)

Schulman, J., Wolski, F., Dhariwal, P., Radford, A. 和 Klimov, O. (2017)。邻近策略优化算法。[在线] arXiv.org。可用链接：[https://arxiv.org/abs/1707.06347.](https://arxiv.org/abs/1707.06347.)

Haarnoja, T., Zhou, A., Abbeel, P. 和 Levine, S. (2018)。软演员-评论家：带有随机演员的离策略最大熵深度强化学习。arXiv:1801.01290 [cs, stat]。[在线] 可用链接：[https://arxiv.org/abs/1801.01290.](https://arxiv.org/abs/1801.01290.)

Du, H., Li, Z., Niyato, D., Yu, R., Xiong, Z., Xuemin, Shen 和 Dong In Kim (2023)。在无线边缘网络中启用AI生成内容（AIGC）服务。doi: https://doi.org/10.48550/arxiv.2301.03220.

Svensson, H.G., Tyrchan, C., Engkvist, O. 和 Morteza Haghir Chehreghani (2024)。利用强化学习进行新药设计。机器学习, 113(7), 第4811–4843页。doi: https://doi.org/10.1007/s10994-024-06519-w.

quantumiracle (2019)。*GitHub — quantumiracle/Popular-RL-Algorithms: PyTorch实现的软演员-评论家（SAC），双延迟DDPG（TD3），演员-评论家（AC/A2C），邻近策略优化（PPO），QT-Opt，PointNet..* [在线] GitHub。可用链接：[https://github.com/quantumiracle/Popular-RL-Algorithms](https://github.com/quantumiracle/Popular-RL-Algorithms) [访问时间：2024年12月12日]。

MrSyee (2019). *GitHub — MrSyee/pg-is-all-you-need: 策略梯度就是你所需要的一切！一步一步教你掌握著名的PG方法。* [在线] GitHub. 可用链接：[https://github.com/MrSyee/pg-is-all-you-need?tab=readme-ov-file.](https://github.com/MrSyee/pg-is-all-you-need?tab=readme-ov-file.)

![](../Images/b852f22c4c64850f1155b42ffaf02eb7.png)
