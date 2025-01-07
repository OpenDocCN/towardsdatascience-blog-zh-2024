# 使用结果加权学习估算个性化治疗规则

> 原文：[https://towardsdatascience.com/estimating-individualized-treatment-rules-using-outcome-weighted-learning-1095b3c2d6e9?source=collection_archive---------8-----------------------#2024-03-31](https://towardsdatascience.com/estimating-individualized-treatment-rules-using-outcome-weighted-learning-1095b3c2d6e9?source=collection_archive---------8-----------------------#2024-03-31)

## 一种用于为患者制定个性化治疗的非参数方法

[](https://medium.com/@nadavgoo?source=post_page---byline--1095b3c2d6e9--------------------------------)[![Nadav Har-Tuv](../Images/981fadd23cdfb60cfe0fa02dbb8edca6.png)](https://medium.com/@nadavgoo?source=post_page---byline--1095b3c2d6e9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1095b3c2d6e9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1095b3c2d6e9--------------------------------) [Nadav Har-Tuv](https://medium.com/@nadavgoo?source=post_page---byline--1095b3c2d6e9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1095b3c2d6e9--------------------------------) ·阅读时间6分钟·2024年3月31日

--

在许多疾病中，不同的患者对不同的治疗反应不同。对一些患者有效的药物可能对其他具有不同特征的患者无效。因此，通过根据患者的特征进行治疗，而不是对所有患者采用相同治疗，可以显著提高医疗效果。

在本文中，我将尝试向你展示如何训练一个机器学习模型来学习最优的个性化治疗。

本文讨论的是个性化医疗领域，但其结果可以应用于任何领域。例如：不同的人对社交媒体上的不同广告反应不同，因此，在同一产品有多个广告的情况下，如何选择向哪些观众展示哪个广告？

该方法在任何必须给出治疗但每个样本个体只能接受一种治疗的情况下非常有用，因此你无法知道该个体如果接受其他治疗会有怎样的反应。

# 让我们形式化这个问题

进行了一项实验，比较了两种（或更多）治疗方法。我们将它们命名为T = 1,2……一个协变量向量X表示每个患者。每个患者*i*具有一个协变量向量Xᵢ，接受了治疗Tᵢ，并且有一个记录的治疗反应Rᵢ。

例如，假设你想测试三种不同的糖尿病药物，我们将这些药物命名为“1”、“2”、“3”。

我们有一个名为Esther的患者，她64岁，8年前被诊断为糖尿病，体重65公斤，身高1.54米。Esther接受了药物“1”，并且在服用新药后，她的血糖下降了10个点。

在我们的例子中，我们对Esther的数据点是X = {女性，64岁，诊断8年，65公斤，1.54米}，T = “1”，R = 10。

在这种设定下，我们希望学习一个最优的决策规则D(x)，它为每个患者分配治疗“1”、“2”或“3”，以优化该患者的治疗效果。

解决这个问题的旧方法是将结果建模为数据和治疗的函数，并将预测结果表示为*f*(X,T)。一旦我们有了模型，我们就可以创建一个决策规则D(x)：我们计算*f*(X,1)，*f*(X,2)，和*f*(X,3)，并给予患者能够最大化其期望结果的药物。

当我们对生成数据的基础模型有相当好的理解时，这个解决方案是有效的。在这种情况下，我们只需要一些微调来找到适合我们案例的最佳参数。

然而，如果模型不好，那么我们的结果也会不好，无论手头的数据有多少。

我们能否提出一个不带参数的决策规则，并且不假设数据与治疗结果之间有任何先验关系？

答案是肯定的，我们可以使用机器学习找到一个不假设反应和治疗之间关系的决策规则！

# 使用Outcome Weighted Learning（OWL）方法解决非参数问题

解决这个问题的方法是解决一个分类问题，其中标签是实验中给予的治疗，每个数据点*i*的权重为Rᵢ/π(Tᵢ|Xᵢ)，其中π(Tᵢ|Xᵢ)是给定你有特征Xᵢ的情况下，获得治疗Tᵢ的倾向性，这可以从数据中计算出来。

这有道理，因为我们试图遵循实验的结果，但仅仅是最有效的地方。我们通过倾向性进行除法，是为了修正类别大小的偏差。如果你学过强化学习，那么这个整个过程对你来说应该是熟悉的。

这里是一个使用支持向量机（SVM）的猫头鹰分类器的例子。你可以自由选择任何你喜欢的分类器。

```py
import numpy as np
from sklearn.linear_model import LogisticRegression
from sklearn import svm

def owl_classifier(X_train, T, R, kernel, gamma):
  n = len(T)
  pi = np.zeroes(n) #Initialize pi as a vector of zeroes
  probs = LogisticRegression().fit(X_train, T).predict_proba(X_train)#This is a n*unique(T) matrix that gives every person the probability of getting each treatment
  for t in np.unique(T):
    pi += probs[,t]*(T==t) #Every data point is assigned the probability of getting the treatment that it got, given the covariates
  clf = svm.SVC(kernel = kernel, gamma = gamma) # initialize an svm classifier, the parameters need to be found by cross validation
  clf.fit(X_train, T, sample_weight = R/pi) # fit the classifier with the treatments as labels and R/pi as sample weights 
```

# 用于测试OWL方法的仿真

模拟数据可以用来测试猫头鹰方法。我们创建奖励函数，以便知道每个患者的最佳治疗方案。然后，我们可以在数据上训练OWL分类器，并检查其与最佳分类器的契合程度。

例如：

我创建了50个特征，它们都是从U([-1,1])分布中采样的。我随机均匀地给予患者三种治疗方案之一{1,2,3}。

响应函数是从N(μ, 1)分布中采样的，其中μ = (X₁ + X₂)*I(T=1) + (X₁ — X₂)*I(T=2) + (X₂-X₁)*I(T=3)

```py
# This code block creates the data for the simulation
import numpy as np

n_train = 500 # I purposely chose a small training set to simulate a medical trial
n_col = 50 # This is the number of features
n_test = 1000
X_train = np.random.uniform(low = -1, high = 1, size = (n_train, n_col))
T = np.random.randint(3, size = n_train) # Treatments given at random uniformly
R_mean = (X_train[:,0]+X_train[:,1])*(T==0) + (X_train[:,0]-X_train[:,1])*(T==1) + (X_train[:,1]-X_train[:,0])*(T==2)
R = np.random.normal(loc = R_mean, scale = .1) # The stanadard deviation can be tweaked
X_test = np.random.uniform(low = -1 , high = 1, size = (n_test, n_col))

# The optimal classifier can be deduced from the design of R
optimal_classifier = (1-(X_test[:,0] >0)*(X_test[:,1]>0))*((X_test[:,0] > X_test[:,1]) + 2*(X_test[:,1] > X_test[:,0]))
```

不难看出，最佳治疗方案是在X₁和X₂都为正时给治疗1。如果它们都为负，则当X₂ < X₁时给予治疗2，当X₁ < X₂时给予治疗3。如果X₁为正且X₂为负，则给予治疗2。如果X₂为正且X₁为负，则给予治疗3。

或者我们可以通过图像展示这个内容。这些是最佳治疗的不同范围，针对X₁和X₂的范围展示：

![](../Images/ae2b6be38b81bf4c36916484382a510e.png)

X₁、X₂组合的最佳治疗范围

我采样了500个数据点，包含50个特征，并使用我上面描述的奖励函数。我用高斯（‘rbf’）核函数拟合了一个OWL分类器，并得到了以下分类结果，我将其可视化以展示X₁和X₂的值：

![](../Images/77bcf965453c276e5caa43f081138e22.png)

针对X₁、X₂值的治疗组分类可视化

```py
# Code for the plot 
import seaborn as sns

kernel = 'rbf'
gamma = 1/X_train.shape[1] 
# gamma is a hyperparameter that has to be found by cross validation but this is a good place to start
D = owl_classifier(X_train, T, R, kernel, gamma)
prediction = D.predict(X_test)
sns.scatterplot(x = X_test[:,0], y = X_test[:,1], c = prediction )
```

如果你没有注意到这里发生了什么：数据由两个影响反应的特征和48个噪声特征组成。模型成功地学习到了这两个重要特征的影响，而我们并没有以任何方式对这个关系进行建模！

这只是一个简单的例子，我让奖励函数依赖于X₁和X₂，以便易于理解和可视化，但你可以自由使用其他例子并尝试不同的分类器。

# 结论

Outcome-weighted learning 可以用来学习最佳治疗方案，适用于我们在训练数据中每个病人只看到一个治疗方法的情况，而不需要将反应建模为特征和治疗方法的函数。

有一些数学内容我从本文中省略了，这些内容证明了整个过程的合理性，我并不是凭空编造的。

未来对此主题的研究应包括：

1.  开发与探索：即使我们已经学到了治疗规则，有时探索那些模型认为不是最佳的选项仍然是有益的。模型可能是错误的。

1.  序列治疗：当存在一系列治疗时，每一次治疗都会改变病人的状态。整个序列的解决方案应通过动态规划来求解。

1.  设计：在本文中，我只是假设治疗是按照给定规则提供的。也许我们可以找到一些设计来改进学习过程。
