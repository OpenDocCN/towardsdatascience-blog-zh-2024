# 数据科学在家：用蒙特卡罗与遗传算法解决保姆日程难题

> 原文：[https://towardsdatascience.com/data-science-at-home-solving-the-nanny-schedule-puzzle-with-monte-carlo-and-genetic-algorithms-eb4b63ada9fe?source=collection_archive---------8-----------------------#2024-09-06](https://towardsdatascience.com/data-science-at-home-solving-the-nanny-schedule-puzzle-with-monte-carlo-and-genetic-algorithms-eb4b63ada9fe?source=collection_archive---------8-----------------------#2024-09-06)

## 在为我们的育儿寻找完美保姆的过程中，带来秩序与简化我们的搜索

[](https://courtney-perigo.medium.com/?source=post_page---byline--eb4b63ada9fe--------------------------------)[![Courtney Perigo](../Images/883923640d601a1160ca6d49c4c7c50b.png)](https://courtney-perigo.medium.com/?source=post_page---byline--eb4b63ada9fe--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--eb4b63ada9fe--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--eb4b63ada9fe--------------------------------) [Courtney Perigo](https://courtney-perigo.medium.com/?source=post_page---byline--eb4b63ada9fe--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--eb4b63ada9fe--------------------------------)·12分钟阅读·2024年9月6日

--

作为一名数据科学领域的领导者，我习惯了带领团队将混乱转化为清晰。但当混乱发生在自己家庭的保姆日程上时，即使是最精心安排的计划也可能出错。工作会议、午休时间以及不可预测的班次让我们的思绪不停地打转——直到我意识到，我可以用那些解决商业问题的算法来解决一个非常私人的问题。凭借蒙特卡罗模拟、遗传算法和一丝父母的智慧，我开始了将我们混乱的日程一项项通过算法调整的旅程。结果呢？好吧，可以说我们的保姆新日程看起来简直是完美契合。

![](../Images/2a59f180e3abe545d9fcbc065578c1fc.png)

图片来自[Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 设置舞台：伟大的日程难题

我们的家庭日程就像是闯入瓷器店的公牛之后的残局。父母1的工作时间是标准的朝九晚五，算是这个难题中的简单部分。但接着，父母2出现了，他在芝加哥一家医院的急诊科值班，时间完全无法预测。有些天是黎明破晓开始，有些则一直延续到深夜，完全没有规律可循。突然之间，原本简单的日程变成了一个没有解决方案的魔方。

![](../Images/3f286767a7ee06f76d50a61aa944a7af.png)

图片由[Nick Fewings](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们把自己想象成这个混乱中的父母。早晨变成了疯狂的冲刺，下午总是充满了猜测，晚上——谁知道呢？我们的家庭似乎要迎来一个“谁在看保姆？”的未来。我们需要一个决策分析解决方案，能够像急诊室一样迅速应对突发情况。

就在那时我突然意识到：如果我能使用我在工作中依赖的工具来解决这个不断变化的难题会怎样？如果我们不再与混乱作斗争，而是能够驾驭它——甚至预测它呢？带着这个想法，到了将我们保姆的时间表置于算法显微镜下的时刻。

# 数据科学工具箱：当怀疑时，进行模拟

由于我们的家庭时间表像被公牛闯入的瓷器店一样凌乱不堪，显然我们需要的不仅仅是一个日历和一份祈祷。这时，我转向了蒙特卡洛模拟——数据科学家版的水晶球。这个想法很简单：如果我们不能准确预测混乱何时降临，为什么不模拟所有可能出错的情况呢？

*蒙特卡洛模拟是一种通过随机抽样来建模系统行为的技术。在这个案例中，我们将使用它随机生成父母2的可能工作时间表，从而模拟他们班次的不确定性，经过多次迭代。*

想象一下运行数千个“如果”的场景：如果父母2被叫去做早班怎么办？如果紧急情况让他们在医院耽搁了怎么办？如果不幸的是，两个父母的时间表在最糟糕的时刻重叠怎么办？蒙特卡洛方法的魅力在于，它不仅仅给你一个答案——它给你数千个答案，每一个都是对未来的不同展望。

这不仅仅是预测父母2何时可能被叫去参与抢救；它关乎确保我们的保姆准备好应对急诊室可能抛向我们的每一个变化。无论是早班还是深夜的紧急情况，模拟帮助我们看到所有可能性，以便我们能为最可能发生的情况——以及最糟糕的情况——做好规划。可以把它看作是一种混乱保险，并且附带一点心安。

在以下代码块中，模拟生成了父母2的五天工作周（周一至周五）的工作时间表。每天，父母2被叫去工作的概率是固定的，如果被叫去工作，就会根据这些概率从一组预定义的班次中随机选择一个班次。我们还增加了一个功能，考虑到周三下午1点的固定会议，并据此调整父母2的时间表。

```py
import numpy as np

def simulate_parent_2_schedule(num_days=5):
    parent_2_daily_schedule = []  # Initialize empty schedule for Parent 2

    for day in range(num_days):
        if np.random.rand() < parent_2_work_prob:  # Randomly determine if Parent 2 works
            shift = np.random.choice(
                list(parent_2_shift_probabilities.keys()), 
                p=[parent_2_shift_probabilities[shift]['probability'] for shift in parent_2_shift_probabilities]
            )
            start_hour = parent_2_shift_probabilities[shift]['start_hour']  # Get start time
            end_hour = parent_2_shift_probabilities[shift]['end_hour']  # Get end time

            # Check if it's Wednesday and adjust schedule to account for a meeting
            if day == 2:  
                meeting_start = 13
                meeting_end = 16
                # Adjust schedule if necessary to accommodate the meeting
                if end_hour <= meeting_start:  
                    end_hour = meeting_end  
                elif start_hour >= meeting_end:
                    parent_2_daily_schedule.append({'start_hour': meeting_start, 'end_hour': end_hour})
                    continue  
                else:
                    if start_hour > meeting_start:
                        start_hour = meeting_start  
                    if end_hour < meeting_end:
                        end_hour = meeting_end  

            parent_2_daily_schedule.append({'start_hour': start_hour, 'end_hour': end_hour})
        else:
            # If Parent 2 isn't working that day, leave the schedule empty or just the meeting
            if day == 2:  
                parent_2_daily_schedule.append({'start_hour': 14, 'end_hour': 16})
            else:
                parent_2_daily_schedule.append({'start_hour': None, 'end_hour': None})

    return parent_2_daily_schedule
```

我们可以使用 simulate_parent_2_schedule 函数来模拟父母2的工作周排班，并将其与父母1更为固定的 9 到 5 的排班相结合。通过重复这一过程 52 周，我们可以模拟一个典型的年份，并识别父母照看空档。这使我们能够计划在最需要保姆的时候。下图总结了在模拟的 52 周期间父母的不可用时间，帮助我们可视化在哪些时段需要额外的育儿支持。

![](../Images/1560f29f544f90c5affe21f43a86fe69.png)

作者特别提供的图片

# 培育完美保姆：基因算法的威力

拥有了模拟我们排班可能遇到的各种变数，我知道是时候引入一些强力的优化技术了。于是，基因算法登场——这是一种受自然选择启发的优化方法，通过迭代进化候选解的种群来找到最佳解决方案。

![](../Images/feedfe4a0fe11f0ffb240c165927d58c.png)

图片来自 [Sangharsh Lohakare](https://unsplash.com/@sangharsh_l?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在这种情况下，每个“候选者”都是一个潜在的保姆特征集合，例如她们的可用性和灵活性。算法评估不同的保姆特征，并通过迭代优化这些特征，找到最适合我们家庭需求的保姆。结果是什么？一位高度优化的保姆，她的排班偏好能够平衡我们的父母照看空档和保姆的可用性。

这种方法的核心是我喜欢称之为“保姆染色体”的东西。在基因算法中，染色体只是表示潜在解的一种方式——在我们的案例中，就是不同的保姆特征。每个“保姆染色体”都有一组特征来定义她的排班：每周保姆可以工作的天数、她每天最多可以工作的小时数，以及她调整开始时间的灵活性。这些特征构成了算法将考虑的每个潜在保姆排班的基本构件。

## 定义保姆染色体

在基因算法中，“染色体”代表一个可能的解决方案，在本例中，它是一组定义保姆排班的特征。以下是我们如何定义保姆的特征：

```py
# Function to generate nanny characteristics
def generate_nanny_characteristics():
    return {
        'flexible': np.random.choice([True, False]),  # Nanny's flexibility
        'days_per_week': np.random.choice([3, 4, 5]),  # Days available per week
        'hours_per_day': np.random.choice([6, 7, 8, 9, 10, 11, 12])  # Hours available per day
    }
```

每个保姆的排班由她的灵活性（是否可以调整开始时间）、每周可以工作的天数以及每天可以工作的最大小时数来定义。这为算法提供了评估各种潜在排班的灵活性。

## 为每个保姆构建排班表

一旦定义了保姆的特征，我们需要生成一个符合这些约束条件的每周排班表：

```py
# Function to calculate a weekly schedule based on nanny's characteristics
def calculate_nanny_schedule(characteristics, num_days=5):
    shifts = []
    for _ in range(num_days):
        start_hour = np.random.randint(6, 12) if characteristics['flexible'] else 9  # Flexible nannies have varying start times
        end_hour = start_hour + characteristics['hours_per_day']  # Calculate end hour based on hours per day
        shifts.append((start_hour, end_hour))
    return shifts  # Return the generated weekly schedule
```

该函数根据保姆的灵活性和工作时间构建保姆的时间表。灵活的保姆可以在早上6点到中午12点之间开始工作，而其他保姆则有固定的时间表，工作时间从固定时间开始和结束。这使得算法能够评估一系列可能的每周时间表。

## 选择最佳候选人

一旦我们生成了初始的保姆时间表群体，我们就使用适应度函数来评估哪些时间表最符合我们的育儿需求。最适合的时间表会被选择进入下一代：

```py
# Function for selection in genetic algorithm
def selection(population, fitness_scores, num_parents):
    # Normalize fitness scores and select parents based on probability
    min_fitness = np.min(fitness_scores)
    if min_fitness < 0:
        fitness_scores = fitness_scores - min_fitness

    fitness_scores_sum = np.sum(fitness_scores)
    probabilities = fitness_scores / fitness_scores_sum if fitness_scores_sum != 0 else np.ones(len(fitness_scores)) / len(fitness_scores)

    # Select parents based on their fitness scores
    selected_parents = np.random.choice(population, size=num_parents, p=probabilities)
    return selected_parents
```

在选择步骤中，算法通过适应度函数评估保姆时间表群体，衡量保姆的可用性与家庭需求的匹配度。最适合的时间表，即那些最能满足所需工作时间的时间表，会被选为下一代的“父母”。

## 添加变异保持趣味性

为了避免陷入次优解，我们通过变异引入了一些随机性。这使得算法可以通过偶尔调整保姆的时间表，探索新的可能性：

```py
# Function to mutate nanny characteristics
def mutate_characteristics(characteristics, mutation_rate=0.1):
    if np.random.rand() < mutation_rate:
        characteristics['flexible'] = not characteristics['flexible']
    if np.random.rand() < mutation_rate:
        characteristics['days_per_week'] = np.random.choice([3, 4, 5])
    if np.random.rand() < mutation_rate:
        characteristics['hours_per_day'] = np.random.choice([6, 7, 8, 9, 10, 11, 12])
    return characteristics
```

通过引入小的变异，算法能够探索到一些本来可能没有考虑到的新时间表。这种多样性对于避免局部最优解并通过多个代改进解非常重要。

## 向完美时间表进化

最后的步骤是进化。在选择和变异机制到位后，遗传算法在多个代中进行迭代，每一轮都会生成更好的保姆时间表。以下是我们如何实现进化过程的：

```py
# Function to evolve nanny characteristics over multiple generations
def evolve_nanny_characteristics(all_childcare_weeks, population_size=1000, num_generations=10):
    population = [generate_nanny_characteristics() for _ in range(population_size)]  # Initialize the population

    for generation in range(num_generations):
        print(f"\n--- Generation {generation + 1} ---")

        fitness_scores = []
        hours_worked_collection = []

        for characteristics in population:
            fitness_score, yearly_hours_worked = fitness_function_yearly(characteristics, all_childcare_weeks)
            fitness_scores.append(fitness_score)
            hours_worked_collection.append(yearly_hours_worked)

        fitness_scores = np.array(fitness_scores)

        # Find and store the best individual of this generation
        max_fitness_idx = np.argmax(fitness_scores)
        best_nanny = population[max_fitness_idx]
        best_nanny['actual_hours_worked'] = hours_worked_collection[max_fitness_idx]

        # Select parents and generate a new population
        parents = selection(population, fitness_scores, num_parents=population_size // 2)
        new_population = []
        for i in range(0, len(parents), 2):
            parent_1, parent_2 = parents[i], parents[i + 1]
            child = {
                'flexible': np.random.choice([parent_1['flexible'], parent_2['flexible']]),
                'days_per_week': np.random.choice([parent_1['days_per_week'], parent_2['days_per_week']]),
                'hours_per_day': np.random.choice([parent_1['hours_per_day'], parent_2['hours_per_day']])
            }
            child = mutate_characteristics(child)
            new_population.append(child)

        population = new_population  # Replace the population with the new generation

    return best_nanny  # Return the best nanny after all generations
```

在这里，算法通过多个代进行进化，基于适应度分数选择最佳的保姆时间表，并通过变异允许新解的出现。经过几个代后，算法会收敛到最好的保姆时间表，优化我们家庭的覆盖需求。

## 最终思考

采用这种方法，我们应用遗传算法迭代改进保姆时间表，确保所选的时间表能够应对第二位家长不可预测的工作班次，同时平衡我们家庭的需求。遗传算法对于这项任务可能有些过于复杂，但它们使我们能够探索各种可能性，并随着时间的推移优化解。

下图描述了保姆适应度分数随时间的变化过程。算法在经过几个代后，能够迅速收敛到最佳保姆染色体。

![](../Images/c63c6cf7389714c269f4c5689608ff85.png)

作者特别插图

![](../Images/76012a7fd850e3c7b79f6eb8510e8481.png)

作者特别插图

# 从混乱到清晰：可视化解决方案

在算法完成工作并优化了我们所寻找的保姆特征后，接下来的步骤是理解结果。这就是可视化派上用场的地方，我必须说，这真是一个改变游戏规则的工具。在我们有了图表和图形之前，我们的排班就像是一个错综复杂的网络，充满了相互冲突的承诺、无法预见的变化和临时的调整。但一旦我们将数据转化为可视化的形式，一切就开始变得清晰。

## 热力图：一目了然的覆盖情况

热力图提供了一抹美丽的色彩，将抽象的内容转化为可触及的东西。颜色越深，表示需要的保姆覆盖时间越多；颜色越浅，表示我们需要的保姆覆盖时间越少。这使得我们可以一眼就发现潜在的问题。星期五需要更多的覆盖吗？查看热力图。星期三保姆的工作时长是否过长？（是的，这很可能。）热力图会告诉你。它让我们迅速理清思路，帮助我们在需要的地方调整排班，并在一切都完美匹配时带来平静的心态。

![](../Images/fb27cbfe60d85ac5094792641f114841.png)

作者特别插图

通过可视化结果，我们不仅解决了排班难题——我们还让它变得易于理解和跟随。我们不再急于弄清楚需要什么样的保姆，而是可以直接查看可视化图表，了解他们需要覆盖哪些内容。从混乱到清晰，这些可视化工具将数据转化为洞察，帮助我们轻松挑选保姆。

# 影响：家庭和谐

在我将数据科学工具包应用到我们家庭的排班问题之前，感觉有些压倒性。我们开始面试保姆，但并没有真正了解我们在寻找什么，或者我们需要什么来维持家里的秩序。

但是在用蒙特卡罗模拟和遗传算法优化了保姆排班之后，差异简直是天壤之别。从前的混乱，现在变成了理解。突然间，我们有了一个清晰的计划，一张谁在何时何地的地图，最重要的是，一张关于要找什么样保姆的路线图。

最大的变化不仅仅是在排班表本身——而是我们感受到的变化。知道你有一个行之有效的计划，它能够在意外发生时灵活适应，这种心态带来了某种平静。对我个人来说，这个项目不仅仅是数据科学的另一个应用，它是一个将我每天在职业生活中使用的技能，应用到直接影响家庭的事务上的机会。

# 数据科学在家庭中的力量

我们往往认为数据科学是为职场而存在的东西，帮助企业优化流程或做出更聪明的决策。但正如我在保姆排班项目中所学到的那样，数据科学的力量并不必局限于办公室的门外。它是一个可以解决日常挑战、简化混乱局面，甚至给家庭生活带来更多平静的工具包。

![](../Images/f3a78985207a9ba22adf08187b3dbd2e.png)

图片由[Kenny Eliason](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

也许你的“保姆难题”并不是关于育儿的。也许它是关于制定最有效的购物清单、管理家庭财务，或是规划家庭假期的行程。无论是什么情况，我们在工作中使用的工具——蒙特卡洛模拟、遗传算法和数据驱动的优化——在家中也能发挥奇妙的作用。你不需要一个复杂的问题来开始，只需要对数据如何帮助解决即使是最平凡挑战的好奇心。

所以这是我给你的挑战：环顾你的生活，找出一个地方，看看数据能带来什么变化。也许你会偶然发现一种节省时间、金钱，甚至只是带来一点心灵宁静的方法。它可能从一个简单的电子表格开始，但谁知道它能引领到哪里呢？也许你会最终构建属于自己的“保姆奥林匹克”或解决自己的一场调度噩梦。

随着我们不断前进，我认为数据科学将成为我们个人生活中更加重要的一部分——不仅仅是我们工作中使用的工具，更是帮助我们管理日常挑战的工具。最终，关键是利用数据的力量让我们的生活变得更加轻松。

Nanny调度问题的代码和数据可以在Github上找到：[https://github.com/agentdanger/nanny-simulation](https://github.com/agentdanger/nanny-simulation)

关于我的专业信息可以在我的网站上找到：[https://courtneyperigo.com](https://courtneyperigo.com)
