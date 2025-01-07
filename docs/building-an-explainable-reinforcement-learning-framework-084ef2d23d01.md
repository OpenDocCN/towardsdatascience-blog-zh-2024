# 构建一个可解释的强化学习框架

> 原文：[https://towardsdatascience.com/building-an-explainable-reinforcement-learning-framework-084ef2d23d01?source=collection_archive---------9-----------------------#2024-03-13](https://towardsdatascience.com/building-an-explainable-reinforcement-learning-framework-084ef2d23d01?source=collection_archive---------9-----------------------#2024-03-13)

![](../Images/7b8e8fae19e2beff62bb005375135544.png)

## 通过符号化策略发现可解释的结果

## 符号化遗传算法、动作电位和方程树

[](https://medium.com/@Dani_Lisle?source=post_page---byline--084ef2d23d01--------------------------------)[![Dani Lisle](../Images/2933bbbca26cf198e7964547a91b2751.png)](https://medium.com/@Dani_Lisle?source=post_page---byline--084ef2d23d01--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--084ef2d23d01--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--084ef2d23d01--------------------------------) [Dani Lisle](https://medium.com/@Dani_Lisle?source=post_page---byline--084ef2d23d01--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--084ef2d23d01--------------------------------) ·阅读时长 9 分钟 ·2024年3月13日

--

我们已经学会了训练能够击败国际象棋和围棋等游戏世界冠军的模型，但有一个主要的局限性：可解释性。存在许多方法可以创建一个黑箱模型，使其能够比任何人类更好地玩游戏或操作系统，但创建一个具有可读闭式策略的模型是完全不同的问题。

在解决这个问题方面，提升的潜力是丰富的。人类能够快速理解的策略不会停留在代码库中——它们进入了科学文献，甚至可能被大众所知。它们可能促进人类和计算机之间的增强认知现实，并减少我们物种的知识与深藏在庞大高维张量中、有效加密的知识之间的隔阂。

但如果我们有更多的算法，能从训练中提供这样可解释的结果，我们该如何以人类可读的方式编码它们呢？

最可行的选择之一是使用微分方程（在离散情况下为差分方程）。这些方程的特点是它们定义了导数，或者量的变化率，提供了一种有效的方式来传达并直观地理解几乎任何系统的动态。这里有一个著名的例子，它描述了系统中热量在时间和空间上的导数：

![](../Images/88ee96e7f8c15f63480837e562b8be1d.png)

“n”维热方程（[维基百科：“热方程”](https://en.wikipedia.org/wiki/Heat_equation)）

事实上，已有研究通过算法的方式直接演化这些方程，而不是试图从张量中提取它们（作为知识）。去年我撰写了一篇论文，详细阐述了一个使用动力学方程的博弈论模拟框架，该方程通过遗传算法按符号逐步演化。陈等人发表的另一篇论文展示了一种符号遗传算法，用于发现偏微分方程，这些方程像热方程一样，描述了物理系统的动态。该小组能够从生成的数据集中挖掘出这些方程。

但再考虑一下国际象棋这款游戏。如果我们在计算学习这些方程的能力不限于单纯的预测应用，那么会怎样？如果我们能利用这些进化技术来学习现实世界中社会经济博弈的最佳策略呢？

在一个新的人类与人机关系，以及复杂策略进入应用的时代，计算方法在发现直观且可转移的战略洞察方面从未如此重要。机遇和潜在威胁既吸引人又压倒性强。

# 让我们开始

*本文讨论的所有Python代码都可以在我的项目的GitHub仓库中访问：* [*https://github.com/dreamchef/abm-dynamics-viz*](https://github.com/dreamchef/abm-dynamics-viz)*。*

在我最近撰写的一篇文章中，我讨论了在一个理论游戏中模拟动态行为的智能体。尽管我非常希望通过符号进化来处理这样的多智能体游戏，但从原子化的角度出发，拓展我们的视野，并利用一些前人的工作是明智之举。像DeepMind这样的团队在创建具有世界级技能的竞争性棋盘游戏模型时，背后有一个机器学习的子学科：强化学习。在这一范式中，智能体有一个观察空间（可以测量并作为值使用的环境变量），一个动作空间（与环境互动或在环境中移动/变化的方式），以及一个奖励系统。通过反复实验，奖励动态使智能体能够构建出一个策略或政策，从而最大化奖励。

我们可以将符号遗传算法应用于一些经典的强化学习问题，以便探索和微调它们。Gymnasium库提供了一系列适合强化学习实验的游戏和任务。我确定的一个非常适合我们目标的游戏是“月球着陆器”。

![](../Images/1c4f6127527a81d09e1da5ca69df3b78.png)

月球着陆器（来源：Gymnasium）

游戏的定义如下：

+   观察空间（8）：x，y位置，x，y速度，角度，角速度，左脚，右脚接触地面。连续。

+   动作空间（4）：无引擎，底部，左侧，右侧引擎点火。离散。

# 学习“登月者”任务的符号策略

你可能已经注意到，尽管像速度和角度这样的变量是连续的，但动作空间是离散的。那么我们如何定义一个接受连续输入和输出（有效地说，是一个分类）的函数呢？实际上，这是一个众所周知的问题，常见的方法是使用动作势能函数。

## 动作势能方程

以神经机制命名，动作势能函数像一个阈值一样工作，计算输入的连续值并输出：

+   如果连续值处于阈值之上，则输出为 True。

+   输出为 False 如下所示。

在我们的问题中，我们实际上需要获得一个4个可能值的离散输出。我们可以在设计这个系统时仔细考虑任务的动态，但我选择了一种简单的方式，作为一种半对抗性的努力，给我们的SGA算法施加更多的压力，从而最终展现其优势。它使用了一个普遍的直觉：接近目标时，我们可能不应该过多使用侧推力：

```py
 def potential_to_action(potential):

    if abs(potential-0) < 0.5:
        return 0

    elif abs(potential-0) < 1:
        return 2

    elif potential < 0:
        return 1

    else:
        return 3
```

确定了这一点后，让我们为接下来的旅程制定一个路线图。我们的主要任务将是：

1.  一种进化结构，其中方程的家族和代数可以存在并竞争。

1.  用于存储方程的数据库结构（方便它们的遗传改造）。

1.  符号变异算法——我们将如何变异？变异什么？

1.  选择方法——我们将选择哪些候选者，并带入下一轮？

1.  评估方法——我们将如何衡量方程的适应度？

## 进化结构

我们首先编写出一个高层次的代码，并将一些算法实现留到后续步骤。这通常以数组的形式呈现，我们可以在其中存储方程群体，并且有一个主循环在指定的代数中进化它们，同时调用变异、选择/淘汰和测试算法。

我们还可以为进化模型定义一组参数，包括代数数量，并指定每个父策略创建和选择多少变异。

以下代码

```py
last_gen = [F]

for i in range(GENS):
    next_gen = []

    for policy in last_gen:
        batch = cull(mutants(policy))

        for policy in batch:
            next_gen.append(policy) 

    last_gen = next_gen
```

最终，它选择表现最好的策略，并通过另一轮测试（与Lunar Lander仿真回合进行对比）来验证它们：

```py
last_gen.sort(key=lambda x: x['score'])

final_cull = last_gen [-30:]

for policy in final_cull:

    policy['score'] = score_policy(policy,ep=7)

final_cull.sort(key=lambda x: x['score'])

print('Final Popluation #:',len(last_gen))

for policy in final_cull:
    print(policy['AP'])
    print(policy['score'])
    print('-'*20)

env.close()
```

## 用于存储方程的数据库结构

我们首先选择一组二元和一元操作符及操作数（来自观察空间），并表示和变异它们：

```py
BIN_OPS = ['mult','add','sub', 'div']
UN_OPS = ['abs','exp','log','sqrt','sin','cos']
OPNDS = ['x','y','dx','dy','angle','dangle','L','R']
```

然后，我们借鉴了Chen等人的思想，将方程编码为树的形式。这将允许我们遍历方程并将符号作为独立对象进行变异。具体来说，我选择使用嵌套数组来完成这个任务。这段代码编码了 *x*y + dx*dy*：

```py
F = {'AP': ['add', 
                ['mult','x','y'],
                ['mult','dx','dy']],
        'score': 0
        }
```

每个方程包括定义其形式的树结构，以及一个得分对象，用来存储它在Lander任务中的评估得分。

## 符号变异算法

我们可以通过多种方式接近算法的变异，具体取决于我们想要修改方程式中不同符号的概率分布。我使用了一种递归方法，在树的每一层，算法随机选择一个符号，如果是二元操作符，则继续向下进入下一层进行选择。

以下主要的变异函数接受一个源策略并输出一个数组，其中包括未更改的源策略和变异后的策略。

```py
def mutants(policy, sample=1):
    children = [policy]
    mutation_target = policy

    for i in range(REPL):
        new_policy = copy.deepcopy(policy)
        new_policy['AP'] = mutate_recursive(new_policy['AP'])
        children.append(new_policy)

    return children
```

这个辅助函数包含递归算法：

```py
def mutate_recursive(target, probability=MUTATE_P):

    # Recursive case
    if isinstance(target, list):
      random_element = random.choice(range(len(target)))
      target[random_element] = mutate_recursive(target[random_element])
      return target

    # Base cases
    elif(target in BIN_OPS):
      new = random.choice(BIN_OPS)
      return new

    elif(target in UN_OPS):
      new = random.choice(UN_OPS)
      return new

    elif(target in OPNDS):
      new = random.choice(OPNDS)
      return new
```

## 选择方法

选择最佳策略将涉及测试它们以获得分数，然后决定一种方式让它们竞争并进化到更高级的阶段。在这里，我使用了一个进化家族树结构，其中家族或批次中的每一代（例如左下角的两个）都包含一个突变，使其与父代有所不同。

```py
 +----------+
                | x + dy^2 |
                +----------+
                     |
          +----------+----------+
          |                     |
     +----v----+           +----v----+
     | y + dy^2|           | x / dy^2|
     +---------+           +---------+
          |                      |
     +----+----+            +----+-----+
     |         |            |          |
 +---v--+-+ +--v---+-+   +--v-----+ +--v-----+
 |y - dy^2| |y - dy^2|   |x / dx^2| |y - dy^3|
 +--------+ +--------+   +--------+ +--------+
```

在对方程式进行评分后，每批方程式将被排名，最佳的N个会继续参与，其他的则被丢弃：

```py
def cull(batch):

    for policy in batch[1:]:
        policy['score'] = score_policy(policy)

    batch.sort(key=lambda x: x['score'], reverse=True)

    return batch[:CULL]
```

## 通过模拟回合进行评分的方法

为了决定哪些方程式编码了最佳策略，我们使用Gymnasium框架进行Lunar Lander任务。

```py
def score_policy(policy, ep=10, render=False):
    observation = env.reset()[0]  # Reset the environment to start a new episode
    total_reward = 0
    sample = 0

    for episode in range(ep):

        while True:
            if render:
                env.render()

            values = list(observation)
            values =    {'x': values[0],
            'y': values[1],
            'dx': values[2],
            'dy': values[3],
            'angle': values[4],
            'dangle': values[5],
            'L': values[6],
            'R': values[7]
            }

            potential = policy_compute(policy['AP'], values)
            action = potential_to_action(potential)

            sample += 1

            observation, reward, done, info = env.step(action)[:4]
            total_reward += reward

            if done:  # If the episode is finished
                break

    return total_reward/EPISODES
```

主要循环用于评分，执行指定的回合数（模拟运行次数），每一回合我们都能看到基本的强化学习范式。

从初始观察开始，信息通过我们的方法用来计算一个动作，动作与环境进行交互，获得下一步的观察结果。

由于我们将方程式存储为树结构，因此我们需要一个单独的方法来计算这种形式的潜力。以下函数使用递归来从编码的方程式中根据观察值获得结果：

```py
def policy_compute(policy, values):

    if isinstance(policy, str):
        if policy in values:
            return values[policy]
        else:
            print('ERROR')

    elif isinstance(policy, list):
        operation = policy[0]
        branches = policy[1:]

        if operation in BIN_OPS:

            if len(branches) != 2:
                raise ValueError(f"At {policy}, Operation {operation} expects 2 operands, got {len(branches)}")

            operands = [operand for operand in branches]

            left = policy_compute(operands[0], values)
            right = policy_compute(operands[1], values)

            if operation == 'add':
                return left + right
            elif operation == 'sub':
                return left - right
            elif operation == 'mult':
                if left is None or right is None:
                    print('ERROR: left:',left,'right:',right)
                return left * right
            elif operation == 'div':
                if right == 0:
                    return 0
                return left / right

        elif operation in UN_OPS:
            if len(branches) != 1:
                raise ValueError(f"Operation {operation} expects 1 operand, got {len(branches)}")

            operand_value = policy_compute(next(iter(branches)), values)

            if operation == 'abs':
                return abs(operand_value)
            elif operation == 'exp':
                return math.exp(operand_value)
            elif operation == 'logabs':
                return math.log(abs(operand_value))
            elif operation == 'sin':
                return math.sin(operand_value)
            elif operation == 'cos':
                return math.cos(operand_value)
            elif operation == 'sqrtabs':
                return math.sqrt(abs(operand_value))

        else:
            raise ValueError(f"Unknown operation: {operation}")

    else:
        print('ERROR')
        return 0
```

上面的代码遍历树的每一层，检查当前符号是操作数还是操作符，并根据情况递归地计算左右两侧，或者返回递归栈中执行适当的操作符计算。

# 下一步

这就是实现的全部内容。在本系列的下一篇文章中，我将解释训练结果，激励实验框架中的变动，并探索通过改进变异和选择算法来扩展训练框架的路径。

与此同时，你可以通过[此链接](https://publish.obsidian.md/danilisle/2024+SIAM+Front+Range+Student+Conference+-+GENETIC+REINFORCEMENT+LEARNING+OF+OPTIMAL+STRATEGY+DYNAMICS+IN+SYMBOLIC+FORM)访问我在2024年科罗拉多大学丹佛分校举行的SIAM前沿学生会议上所做的一次讲座的幻灯片，讲座中讨论了初步的训练结果。

所有关于这个项目的代码都在我的仓库：[https://github.com/dreamchef/abm-dynamics-viz](https://github.com/dreamchef/abm-dynamics-viz)。如果你有任何发现，或者对我的工作有任何想法，欢迎在评论中与我交流！也可以通过[Twitter](https://twitter.com/dani_lisle)和[LinkedIn](https://www.linkedin.com/in/danilisle/)联系我。

*除非另有注明，所有图片均由作者创作。*
