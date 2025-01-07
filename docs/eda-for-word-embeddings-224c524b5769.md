# 使用EDA深入探索词向量

> 原文：[https://towardsdatascience.com/eda-for-word-embeddings-224c524b5769?source=collection_archive---------1-----------------------#2024-07-12](https://towardsdatascience.com/eda-for-word-embeddings-224c524b5769?source=collection_archive---------1-----------------------#2024-07-12)

## 可视化文本数据中的意外见解

[](https://medium.com/@crackalamoo?source=post_page---byline--224c524b5769--------------------------------)[![Harys Dalvi](../Images/cf7fa3865063408efd1fd4c0b4b603db.png)](https://medium.com/@crackalamoo?source=post_page---byline--224c524b5769--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--224c524b5769--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--224c524b5769--------------------------------) [Harys Dalvi](https://medium.com/@crackalamoo?source=post_page---byline--224c524b5769--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--224c524b5769--------------------------------) ·阅读时间：14分钟·2024年7月12日

--

在开始使用新数据集时，通常从一些**探索性数据分析**（EDA）入手是个不错的选择。在训练任何复杂模型之前，花时间了解你的数据有助于你理解数据集的结构，识别任何明显的问题，并运用领域特定的知识。

你可以在各种形式中看到EDA，从[房价](https://www.kaggle.com/code/spscientist/a-simple-tutorial-on-exploratory-data-analysis)到数据科学行业中的高级应用。但我仍然没有看到它应用于当前最火的新数据集：**词向量**，这也是我们最优秀的大型语言模型的基础。那么，为什么不尝试一下呢？

在这篇文章中，我们将**将EDA应用于GloVe词向量**，使用**协方差矩阵、聚类、PCA和向量数学**等技术。这将帮助我们理解词向量的结构，为我们在此数据基础上构建更强大的模型提供一个有用的起点。当我们探索这个结构时，我们会发现它并不总是表面看起来的那样，某些意想不到的偏差隐藏在语料库中。

你将需要：

+   基本的线性代数、统计学和向量数学知识

+   Python 包：`numpy`、`sklearn` 和 `matplotlib`

+   约3 GB的空闲磁盘空间

# 数据集

要开始，请下载数据集，链接为：[huggingface.co/stanfordnlp/glove/resolve/main/glove.6B.zip](https://huggingface.co/stanfordnlp/glove/resolve/main/glove.6B.zip)[1]。此文件包含三个文本文件，每个文件包含一组单词及其向量表示。我们将使用**300维的表示**（glove.6B.300d.txt）。

简单说明一下这个数据集的来源：基本上，这是从维基百科和各种新闻来源中获取的60亿个标记的**共现数据**生成的词嵌入列表。使用共现的一个有用副作用是，**意思相近的词往往会聚集在一起**。例如，由于“the *red* bird”和“the *blue* bird”都是有效句子，我们可能会预期“red”和“blue”的向量会彼此接近。更多技术信息，可以参考[原始的GloVe论文](https://nlp.stanford.edu/pubs/glove.pdf)[1]。

需要明确的是，这些**不是**为大型语言模型训练的词嵌入。它们是一种完全无监督的技术，基于大量语料库。然而，它们展示了许多与语言模型嵌入相似的特性，且本身也很有趣。

这个文本文件的每一行由一个单词和该单词对应的300个向量分量组成，分量之间以空格分开。我们可以用Python将其加载进来。（为了减少噪音并加速处理，我这里使用了完整数据集的前10% `//10`，但如果你愿意的话，可以调整。）

```py
import numpy as np

embeddings = {}

with open(f"glove.6B/glove.6B.300d.txt", "r") as f:
    glove_content = f.read().split('\n')

for i in range(len(glove_content)//10):
    line = glove_content[i].strip().split(' ')
    if line[0] == '':
        continue
    word = line[0]
    embedding = np.array(list(map(float, line[1:])))
    embeddings[word] = embedding

print(len(embeddings))
```

那么我们现在加载了40,000个嵌入向量。

# 相似度度量

我们可能会问的一个自然问题是：**向量通常是否与意义相似的其他向量接近？** 作为后续问题，我们如何量化这个？

我们将量化向量之间相似度的主要方式有两种：一种是**欧几里得距离**，这就是我们熟悉的自然的毕达哥拉斯定理距离。另一种是**余弦相似度**，它衡量两个向量之间的*角度*的余弦值。一个向量与自身的余弦相似度为1，与相反的向量为-1，与正交向量为0。

让我们在NumPy中实现这些：

```py
def cos_sim(a, b):
    return np.dot(a,b)/(np.linalg.norm(a) * np.linalg.norm(b))
def euc_dist(a, b):
    return np.sum(np.square(a - b)) # no need for square root since we are just ranking distances
```

现在我们可以找到与给定单词或嵌入向量最接近的所有向量！我们将按升序进行操作。

```py
def get_sims(to_word=None, to_e=None, metric=cos_sim):
    # list all similarities to the word to_word, OR the embedding vector to_e
    assert (to_word is not None) ^ (to_e is not None) # find similarity to a word or a vector, not both
    sims = []
    if to_e is None:
        to_e = embeddings[to_word] # get the embedding for the word we are looking at
    for word in embeddings:
        if word == to_word:
            continue
        word_e = embeddings[word]
        sim = metric(word_e, to_e)
        sims.append((sim, word))
    sims.sort()
    return sims
```

现在我们可以写一个函数来显示最相似的10个词。最好能加上一个反向选项，这样我们就能显示*最不*相似的词。

```py
def display_sims(to_word=None, to_e=None, n=10, metric=cos_sim, reverse=False, label=None):
    assert (to_word is not None) ^ (to_e is not None)
    sims = get_sims(to_word=to_word, to_e=to_e, metric=metric)
    display = lambda sim: f'{sim[1]}: {sim[0]:.5f}'
    if label is None:
        label = to_word.upper() if to_word is not None else ''
    print(label) # a heading so we know what these similarities are for
    if reverse:
        sims.reverse()
    for i, sim in enumerate(reversed(sims[-n:])):
        print(i+1, display(sim))
    return sims
```

最后，我们可以进行测试了！

```py
display_sims(to_word='red')
# yellow, blue, pink, green, white, purple, black, colored, sox, bright
```

看起来波士顿红袜队在这里意外亮相了。不过除此之外，这大致符合我们的预期。

也许我们可以尝试一些动词，而不仅仅是名词和形容词？怎么样，试试像“share”这样一个友好且温暖的动词？

```py
display_sims(to_word='share')
# shares, stock, profit, percent, shared, earnings, profits, price, gain, cents
```

我猜“share”在这个数据集中作为动词使用的情况不多。唉，没关系。

我们还可以尝试一些更传统的例子：

```py
display_sims(to_word='cat')
# dog, cats, pet, dogs, feline, monkey, horse, pets, rabbit, leopard
display_sims(to_word='frog')
# toad, frogs, snake, monkey, squirrel, species, rodent, parrot, spider, rat
display_sims(to_word='queen')
# elizabeth, princess, king, monarch, royal, majesty, victoria, throne, lady, crown
```

# 类比推理

词嵌入的一个迷人特性是，**类比是通过向量数学内建的**。GloVe论文中的例子是*king - queen = man - woman*。换句话说，重新排列这个方程，我们预期会得到*king = man - woman + queen*。这是真的吗？

```py
display_sims(to_e=embeddings['man'] - embeddings['woman'] + embeddings['queen'], label='king-queen analogy')
# queen, king, ii, majesty, monarch, prince...
```

并不完全正确：与*man - woman + queen*最接近的向量实际上是*queen*（余弦相似度 0.78），接下来是*king*（余弦相似度 0.66），不过差距有点远。受到这段精彩的[3Blue1Brown 视频](https://www.youtube.com/watch?v=wjZofJX0v4M)的启发，我们可以试试用*aunt*和*uncle*代替：

```py
display_sims(to_e=embeddings['aunt'] - embeddings['woman'] + embeddings['man'], label='aunt-uncle analogy')
# aunt, uncle, brother, grandfather, grandmother, cousin, uncles, grandpa, dad, father
```

这个结果更好（余弦相似度 0.7348 对比 0.7344），但仍然不完美。不过我们可以尝试改用欧几里得距离。现在我们需要设置`reverse=True`，因为*较高*的欧几里得距离实际上代表了*较低*的相似度。

```py
display_sims(to_e=embeddings['aunt'] - embeddings['woman'] + embeddings['man'], metric=euc_dist, reverse=True, label='aunt-uncle analogy')
# uncle, aunt, grandfather, brother, cousin, grandmother, newphew, dad, grandpa, cousins
```

现在我们明白了。但似乎类比数学可能没有我们希望的那样完美，至少在我们目前采用的这种简单方法中。

# 幅度

余弦相似度完全与向量之间的*角度*有关。那么，向量的**幅度**是否也很重要呢？

我们可以通过将幅度表示为与零向量的欧几里得距离来重用现有的代码。让我们看看哪些单词具有最大和最小的幅度：

```py
zero_vec = np.zeros_like(embeddings['the'])
display_sims(to_e=zero_vec, metric=euc_dist, label='largest magnitude')
# republish, nonsubscribers, hushen, tael, www.star, stoxx, 202-383-7824, resend, non-families, 225-issue
display_sims(to_e=zero_vec, metric=euc_dist, reverse=True, label='smallest magnitude')
# likewise, lastly, interestingly, ironically, incidentally, moreover, conversely, furthermore, aforementioned, wherein
```

看起来大幅度向量的意义似乎没有什么规律，但它们似乎都有非常具体（有时甚至令人困惑）的含义。另一方面，最小幅度的向量往往是一些非常常见的单词，可以在各种语境中找到。

**幅度之间有巨大的差异**：从最小的向量大约 2.6 到最大的向量大约 17。这个分布看起来是怎样的呢？我们可以绘制一个**直方图**来更好地展示这一点。

```py
import matplotlib.pyplot as plt

def plot_magnitudes():
    words = [w for w in embeddings]
    magnitude = lambda word: np.linalg.norm(embeddings[word])
    magnitudes = list(map(magnitude, words))
    plt.hist(magnitudes, bins=40)
    plt.show()

plot_magnitudes()
```

![](../Images/1cc1402864a59a660414c0cefbb9d77f.png)

我们的词嵌入的幅度直方图

这个分布看起来大致呈正态分布。如果我们想进一步测试这一点，可以使用[Q-Q 图](https://medium.com/analytics-vidhya/what-are-qq-plots-4beb00670d81)。但就目前而言，这样已经足够了。

# 数据集偏差

事实证明，向量嵌入中的方向和子空间可以编码多种不同的概念，通常是有偏的。[这篇论文](https://arxiv.org/pdf/1607.06520)[2]研究了这种情况如何与**性别偏见**相关。

我们也可以在 GloVe 嵌入中复制这个概念。首先，让我们找到“男性气质”概念的方向。我们可以通过计算**向量之间的差异**来完成这个任务，比如*he* 和 *she*、*man* 和 *woman*，等等：

```py
gender_pairs = [('man', 'woman'), ('men', 'women'), ('brother', 'sister'), ('he', 'she'),
                    ('uncle', 'aunt'), ('grandfather', 'grandmother'), ('boy', 'girl'),
                    ('son', 'daughter')]
masc_v = zero_vec
for pair in gender_pairs:
    masc_v += embeddings[pair[0]]
    masc_v -= embeddings[pair[1]]
```

现在我们可以找到“最具男性气质”和“最具女性气质”的向量，这些都是根据嵌入空间的判断来确定的。

```py
display_sims(to_e=masc_v, metric=cos_sim, label='masculine vecs')
# brother, colonel, himself, uncle, gen., nephew, brig., brothers, son, sir
display_sims(to_e=masc_v, metric=cos_sim, reverse=True, label='feminine vecs')
# actress, herself, businesswoman, chairwoman, pregnant, she, her, sister, actresses, woman
```

现在，我们可以进行一个简单的测试，检测数据集中的偏见：计算*nurse*与*man*和*woman*之间的相似度。从理论上讲，这两个值应该大致相等：nurse 不是一个性别化的词汇。这是真的吗？

```py
print("nurse - man", cos_sim(embeddings['nurse'], embeddings['man'])) # 0.24
print("nurse - woman", cos_sim(embeddings['nurse'], embeddings['woman'])) # 0.45
```

这是一个相当大的差异！(请记住，余弦相似度的范围是 -1 到 1，其中正相关值在 0 到 1 之间。)作为参考，0.45 也接近于*cat* 和 *leopard* 之间的余弦相似度。

# 聚类

让我们看看能否使用***k*-均值聚类**来**聚类具有相似含义的词语**。使用`scikit-learn`包可以轻松实现这一点。我们将使用300个聚类，虽然听起来很多，但相信我：几乎所有聚类都非常有趣，你可以仅通过解释它们写一篇完整的文章！

```py
from sklearn.cluster import KMeans

def get_kmeans(n=300):
    kmeans = KMeans(n_clusters=n, n_init=1)
    X = np.array([embeddings[w] for w in embeddings])
    kmeans.fit(X)
    return kmeans

def display_kmeans(kmeans):
    # print all clusters and 5 associated words for each
    words = np.array([w for w in embeddings])
    X = np.array([embeddings[w] for w in embeddings])
    y = kmeans.predict(X) # get the cluster for each word
    for cluster in range(kmeans.cluster_centers_.shape[0]):
        print(f'KMeans {cluster}')
        cluster_words = words[y == cluster] # get all words in each cluster
        for i, w in enumerate(cluster_words[:5]):
            print(i+1, w)

kmeans = get_kmeans()
display_kmeans(kmeans)
```

这里有很多内容可以查看。我们有涉及不同主题的聚类，如纽约市（`manhattan, n.y., brooklyn, hudson, borough`）、分子生物学（`protein, proteins, enzyme, beta, molecules`）和印度名字（`singh, ram, gandhi, kumar, rao`）。

但是**有时这些聚类并不像看起来那样简单**。让我们编写代码来显示包含给定词语的聚类中的所有词汇，以及最近和最远的聚类。

```py
def get_kmeans_cluster(kmeans, word=None, cluster=None):
    # given a word, find the cluster of that word. (or start with a cluster index.)
    # then, get all words of that cluster.
    assert (word is None) ^ (cluster is None)
    if cluster is None:
        cluster = kmeans.predict([embeddings[word]])[0]
    words = np.array([w for w in embeddings])
    X = np.array([embeddings[w] for w in embeddings])
    y = kmeans.predict(X)
    cluster_words = words[y == cluster]
    return cluster, cluster_words

def display_cluster(kmeans, word):
    cluster, cluster_words = get_kmeans_cluster(kmeans, word=word)
    # print all words in the cluster
    print(f"Full KMeans ({word}, cluster {cluster})")
    for i, w in enumerate(cluster_words):
        print(i+1, w)
    # rank all clusters (excluding this one) by Euclidean distance of their centers from this cluster's center
    distances = np.concatenate([kmeans.cluster_centers_[:cluster], kmeans.cluster_centers_[cluster+1:]], axis=0)
    distances = np.sum(np.square(distances - kmeans.cluster_centers_[cluster]), axis=1)
    nearest = np.argmin(distances, axis=0)
    _, nearest_words = get_kmeans_cluster(kmeans, cluster=nearest)
    print(f"Nearest cluster: {nearest}")
    for i, w in enumerate(nearest_words[:5]):
        print(i+1, w)
    farthest = np.argmax(distances, axis=0)
    print(f"Farthest cluster: {farthest}")
    _, farthest_words = get_kmeans_cluster(kmeans, cluster=farthest)
    for i, w in enumerate(farthest_words[:5]):
        print(i+1, w)
```

现在让我们尝试这段代码。

```py
display_cluster(kmeans, 'animal')
# species, fish, wild, dog, bear, males, birds...
display_cluster(kmeans, 'dog')
# same as 'animal'
display_cluster(kmeans, 'birds')
# same again
display_cluster(kmeans, 'bird')
# spread, bird, flu, virus, tested, humans, outbreak, infected, sars....?
```

你可能不会每次都得到完全相同的结果：聚类算法是非确定性的。但大多数情况下，“鸟类”会与疾病相关词汇而非动物词汇关联。似乎原始数据集倾向于在疾病传播媒介的语境中使用“鸟”这个词。

这里有成百上千个聚类等待你探索它们的内容。我发现一些有趣的聚类包括“伊利诺伊州”和“成吉思汗”。

# 主成分分析

**主成分分析（PCA）**是我们用来找出数据集在向量空间中方差最大的方向的工具。让我们试试它。和聚类一样，`sklearn`使得这一过程非常简单。

```py
from sklearn.decomposition import PCA

def get_pca_vecs(n=10): # get the first 10 principal components
    pca = PCA()
    X = np.array([embeddings[w] for w in embeddings])
    pca.fit(X)
    principal_components = list(pca.components_[:n, :])
    return pca, principal_components

pca, pca_vecs = get_pca_vecs()
for i, vec in enumerate(pca_vecs):
    # display the words with the highest and lowest values for each principal component
    display_sims(to_e=vec, metric=cos_sim, label=f'PCA {i+1}')
    display_sims(to_e=vec, metric=cos_sim, label=f'PCA {i+1} negative', reverse=True)
```

就像我们的*k*-均值实验一样，这些PCA向量中有很多非常有趣的内容。例如，让我们来看看主成分9：

```py
 PCA 9
1 featuring: 0.38193
2 hindi: 0.37217
3 arabic: 0.36029
4 sung: 0.35130
5 che: 0.34819
6 malaysian: 0.34474
7 ka: 0.33820
8 video: 0.33549
9 bollywood: 0.33347
10 counterpart: 0.33343
    PCA 9 negative
1 suffolk: -0.31999
2 cumberland: -0.31697
3 northumberland: -0.31449
4 hampshire: -0.30857
5 missouri: -0.30771
6 calhoun: -0.30749
7 erie: -0.30345
8 massachusetts: -0.30133
9 counties: -0.29710
10 wyoming: -0.29613
```

看起来，组件9的正值与中东、南亚和东南亚的术语相关，而负值则与北美和英国的术语相关。

另一个有趣的成分是成分3。所有的正值都是十进制数，显然这是这个模型中的一个显著特征。成分8也展示了类似的模式。

```py
 PCA 3
1 1.8: 0.57993
2 1.6: 0.57851
3 1.2: 0.57841
4 1.4: 0.57294
5 2.3: 0.57019
6 2.6: 0.56993
7 2.8: 0.56966
8 3.7: 0.56660
9 1.9: 0.56424
10 2.2: 0.56063
```

## 降维

PCA的一个主要优点是，它允许我们**将一个高维数据集**（在此案例中为300维）**通过投影到前几个组件，仅用两维或三维展示**。让我们尝试做一个二维图，看是否能从中提取出任何信息。我们还会使用*k*-均值进行按聚类的颜色编码。

```py
def plot_pca(pca_vecs, kmeans):
    words = [w for w in embeddings]
    x_vec = pca_vecs[0]
    y_vec = pca_vecs[1]
    X = np.array([np.dot(x_vec, embeddings[w]) for w in words])
    Y = np.array([np.dot(y_vec, embeddings[w]) for w in words])
    colors =  kmeans.predict([embeddings[w] for w in words])
    plt.scatter(X, Y, c=colors, cmap='spring') # color by cluster
    for i in np.random.choice(len(words), size=100, replace=False):
        # annotate 100 randomly selected words on the graph
        plt.annotate(words[i], (X[i], Y[i]), weight='bold')
    plt.show()

plot_pca(pca_vecs, kmeans)
```

![](../Images/8754000d85b058dbbdcf1ea2133c5029.png)

我们的嵌入数据集的第一（X轴）和第二（Y轴）主成分的图表

不幸的是，这个图表完全乱了！从中学到的信息非常有限。看起来仅凭两个维度来解释300个维度的数据集，至少在这个数据集的情况下，并不是很容易。

有两个例外。首先，我们看到名字通常会聚集在图表的顶部。其次，在左下角有一个突出的小部分，像个伤疤一样。这一区域似乎与数字相关，尤其是十进制数字。

# 协方差

了解**输入特征之间的协方差**往往很有帮助。在这种情况下，我们的输入特征只是难以解释的抽象向量方向。然而，协方差矩阵可以告诉我们这些信息实际上被利用了多少。如果我们看到较高的协方差，意味着某些维度之间高度相关，或许我们可以稍微减少维度。

```py
def display_covariance():
    X = np.array([embeddings[w] for w in embeddings]).T # rows are variables (components), columns are observations (words)
    cov = np.cov(X)
    cov_range = np.maximum(np.max(cov), np.abs(np.min(cov))) # make sure the colorbar is balanced, with 0 in the middle
    plt.imshow(cov, cmap='bwr', interpolation='nearest', vmin=-cov_range, vmax=cov_range)
    plt.colorbar()
    plt.show()

display_covariance()
```

![](../Images/bdf321419db9850f8aaabea3567d3ad2.png)

我们数据集中所有300个向量组件的协方差矩阵

当然，主对角线有一条明显的线，表示每个组件与自身有很强的相关性。除此之外，这个图并没有什么特别有趣的地方。大部分区域看起来几乎是空白的，这其实是一个好兆头。

如果仔细观察，你会发现有一个例外：组件9和276似乎有较强的相关性（协方差为0.308）。

![](../Images/bd08c04d3fad3c5c45538546d37a43e3.png)

聚焦于组件9和276的协方差矩阵。观察这里有一个稍微明亮的红点，以及沿着行和列的奇怪行为。

让我们通过打印与组件9和276最相关的向量来进一步调查。这个操作相当于计算与一个全零基向量的余弦相似度，除了相关组件的位置为1。

```py
e9 = np.zeros_like(zero_vec)
e9[9] = 1.0
e276 = np.zeros_like(zero_vec)
e276[276] = 1.0
display_sims(to_e=e9, metric=cos_sim, label='e9')
# grizzlies, supersonics, notables, posey, bobcats, wannabe, hoosiers...
display_sims(to_e=e276, metric=cos_sim, label='e276')
# pehr, zetsche, steadied, 202-887-8307, bernice, goldie, edelman, kr...
```

这些结果很奇怪，并且信息量不大。

等一下：如果一个向量组件中具有非常*负*值的词汇在另一个组件中也倾向于具有非常负的值，那么这些组件之间也可能存在正协方差。让我们试着反转相似性的方向。

```py
display_sims(to_e=e9, metric=cos_sim, label='e9', reverse=True)
# therefore, that, it, which, government, because, moreover, fact, thus, very
display_sims(to_e=e276, metric=cos_sim, label='e276', reverse=True)
# they, instead, those, hundreds, addition, dozens, others, dozen, only, outside
```

看起来这两个组件都与基本功能词和数字相关，这些词可以在许多不同的语境中找到。这有助于解释它们之间的协方差，至少比正协方差的情况更能解释这一点。

# 结论

在本文中，我们对一个包含300维的**GloVe词向量**数据集应用了各种**探索性数据分析（EDA）**技术。我们使用余弦相似度来衡量词汇意义之间的相似性，使用聚类将词汇分组为相关群体，并通过主成分分析（PCA）来识别对词向量模型最重要的向量空间方向。

我们通过主成分分析（PCA）在视觉上观察到输入特征之间的协方差非常小。我们尝试使用PCA将所有300维的数据投影到二维空间中，但结果还是有点混乱。

我们还测试了数据集中的假设和偏差。通过比较*nurse*与*man*和*woman*的余弦相似度，我们识别出了数据集中的性别偏见。我们尝试使用向量数学表示类比（例如，“king”与“queen”的关系就像“man”与“woman”），并取得了一定的成功。通过减去指代男性和女性的向量示例，我们能够发现与性别相关的向量方向，并展示数据集中“最男性化”和“最女性化”的向量。

你可以在词向量数据集上尝试更多的EDA，但我希望这能作为一个良好的起点，帮助你理解一般的EDA技术以及词向量的结构。如果你想查看与本文相关的完整代码及一些额外的示例，可以访问我的GitHub：[crackalamoo/glove-embeddings-eda](https://github.com/crackalamoo/glove-embeddings-eda)。感谢阅读！

## 参考文献

[1] J. Pennington, R. Socher 和 C. Manning，[GloVe：用于词表示的全局向量](https://nlp.stanford.edu/projects/glove/)（2014年），斯坦福大学自然语言处理（公开领域数据集）

[2] T. Bolukbasi, K. Chang, J. Zou, V. Saligrama 和 A. Kalai，[人类和计算机程序员的关系，就像女人和家庭主妇的关系？去偏见词向量嵌入](https://arxiv.org/pdf/1607.06520)（2016年），微软研究院新英格兰分院

*所有图片均由作者使用Matplotlib制作。*
