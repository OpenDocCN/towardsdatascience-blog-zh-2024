# AI代理与CrewAI的强大协作

> 原文：[https://towardsdatascience.com/powerful-collaboration-of-ai-agents-with-crewai-17f84378430b?source=collection_archive---------3-----------------------#2024-02-12](https://towardsdatascience.com/powerful-collaboration-of-ai-agents-with-crewai-17f84378430b?source=collection_archive---------3-----------------------#2024-02-12)

## 一个实战营销用例

[](https://toon-beerten.medium.com/?source=post_page---byline--17f84378430b--------------------------------)[![Toon Beerten](../Images/f169eaa8cefa00f17176955596972d57.png)](https://toon-beerten.medium.com/?source=post_page---byline--17f84378430b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--17f84378430b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--17f84378430b--------------------------------) [Toon Beerten](https://toon-beerten.medium.com/?source=post_page---byline--17f84378430b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17f84378430b--------------------------------) ·阅读时间11分钟·2024年2月12日

--

![](../Images/8e6b6dc2cf7f29bc959f6bdc65a4ae87.png)

图片由作者提供（[通过](https://huggingface.co/spaces/prodia/fast-stable-diffusion)）

在本文中，我将向你展示如何编排AI“木偶”来成功应对现实生活中的营销挑战，其中代理们共同合作，完成以下任务：

+   分析客户数据资料

+   为目标营销选择理想产品

+   为这些产品创建有吸引力的推广文案。

我们将使用一个新的框架——[CrewAI](https://github.com/joaomdmoura/crewAI)，使得自主AI代理能够协作并实现共同目标。所有内容都有文档记录在提供的[Colab笔记本](https://colab.research.google.com/github/Toon-nooT/notebooks/blob/main/Marketing_Campaign__With_CrewAI.ipynb)中，因此你可以复制并将其适应到你自己的用例中。

# 什么是CrewAI？

[CrewAI](https://docs.crewai.com/)是一个新的框架，旨在促进AI代理之间的协作。代理可以扮演特定角色，分享共同目标，并作为一个整体高效地运作。它是开源的，基于[Langchain](https://www.langchain.com/)构建的。在同一领域的一些替代方案包括[微软的AutoGen](https://microsoft.github.io/autogen/)和[ChatDev](https://github.com/OpenBMB/ChatDev)。

![](../Images/4afc85d7770d80f5787167c25db39ee8.png)

图片来自官方[文档](https://joaomdmoura.github.io/crewAI/)

CrewAI的主要概念围绕三个核心实体：**代理**、**任务**和**小组**。

1.  **代理**：这些是独立单元，编程用于执行任务、做出决策并与其他代理沟通。它们可以使用**工具**，这些工具可以是简单的搜索功能，或者是涉及其他链条、API等的复杂集成。

1.  **任务**：任务是人工智能代理需要完成的工作或职责。任务可以包含额外的信息，例如哪个代理应该执行此任务以及他们可能需要哪些工具。

1.  **团队**是由每个具有特定角色的代理组成的团队，这些代理共同努力实现一个共同的目标。组建团队的过程包括汇集代理、定义他们的任务并确定任务执行顺序。

为了测试它的能力，我设计了以下情景……

# **营销挑战**

假设你是一个地方零售商的负责人。下周，你将对12种产品进行促销活动。你会将哪些产品推广给哪些客户？正如你所想，向男性客户推广口红几乎没有意义。这个过程能通过人工智能进行优化吗？

通过使用忠诚卡和数据挖掘，您可以访问一个包含客户个性分析的数据库。这有助于了解客户的特点、偏好和行为。通过精心设计的提示（见笔记本），我几秒钟内便获得了这一客户数据集：

```py
birth_year,sex,marital_status,yearly_household_income_percentile,number_of_toddlers,number_of_teens,highest_education,monthly_spend_on_wine,monthly_spend_on_vegetables,monthly_spend_on_toys,last_month_coupon_use
1975,F,married,65,1,0,masters,30,65,20,high
1992,M,single,30,0,0,bachelors,15,40,0,none
1980,F,married,80,0,2,bachelors,50,80,85,low 
1968,M,divorced,45,0,0,high_school,45,50,0,high
1990,F,single,25,0,0,associates,10,35,0,low
1985,M,married,90,2,1,phd,80,100,120,high
2000,F,single,40,0,0,bachelors,25,55,0,none
1972,M,married,70,0,1,masters,60,70,40,low
1988,F,married,55,1,0,associates,20,40,30,high
1970,M,single,60,0,0,high_school,35,60,0,none
```

对于可能的促销产品，我提出了以下几项：

```py
Fresh Lettuce
Diapers
Irish whiskey
laundry detergent
Chips
Spaghetti cans (ready to eat)
Minecraft Video Game
Mascara
Toilet Paper (best value)
Wagyu beef steak
Organic avocados
Cigarettes
```

这些产品是特别挑选的，因为我预期会看到某些关联。例如，婴儿尿布的促销与幼儿数量之间的关系，以及和牛牛排与家庭收入之间的关系。代理们能否通过常识合作发现这种逻辑？

# **实现**

我的第一组将通过为每个特定客户选择最合适的三款产品来精准定位产品。在这个团队中，我创建了三个代理：

+   **首席促销总监**：负责主要任务，监督其他人的工作

+   **客户画像专家**：用于了解客户

+   **产品专家**：精通将产品与客户匹配

每个代理都需要一个角色、目标和背景故事。我们可以使用自然语言来描述这一点。在我的[笔记本](https://github.com/Toon-nooT/notebooks)中，它变成了这样：

```py
profiler = Agent(
  role='profiler',
  goal='''From limited data, you logically deduct conclusions about people.''',
  backstory='You are an expert psychologist with decades of experience.',
  llm=llm,
  verbose=True,
  allow_delegation=True
)
product_specialist = Agent(
  role='product specialist',
  goal='''Match the product to the customer''',
  backstory='You have exceptional knowledge of the products and can say
             how valuable they are to a customer.',
  llm=llm,
  verbose=True,
  allow_delegation=True
)

Chief_Promotional_Director = Agent(
    role="Chief Promotion Director",
    goal='''
     Oversee the work done by your team to make sure it's the best possible
     and aligned with the product's goals, review, approve, ask clarifying
     question or delegate follow up work if necessary to make decisions''',
    backstory='''
     You're the Chief Promotion Officer of a large retailer. You're
     launching a personalized ad campaign, trying to make sure your team
     is crafting the best possible content for the customer.''',
    tools=[],
    llm=llm,
    verbose=True
)
```

以下**任务**交给了代理**首席促销总监**，他会将工作分配给其他代理：

```py
select_3_products_task = f'''You're creating a targeted marketing campaign
tailored to what we know about our customers.
For each customer, we have to choose exactly three products to promote 
in the next campaign. Make sure the selection is the best possible and
aligned with the customer. Review, approve, ask clarifying question or
delegate follow up work if necessary to make decisions. When delegating
work send the full draft as part of the information.
This is the list of all the products participating in the campaign: {products}.
This is all we know so far from the customer: {customer_description}.
To start this campaign we will need to build first an understanding of our
customer. Once we have a profile about the customers interests, lifestyle and
means and needs, we have to select exactly three products that have the
highest chance to be bought by them.
Your final answer MUST be exactly 3 products from the list, each with a short
description why it matches with this customer. '''
```

所有代理彼此互动，直到**首席促销总监**满意并结束任务。为了更好地理解当这组代理被解除任务时发生了什么，我创建了这个示意图：

![](../Images/677abbd30b9354d5e71d11dfaddd2035.png)

作者图片

第二组将为每个产品编写简短的促销文本。文本内容也应与可以从客户数据中推断出的信息一致。为此，我创建了一个额外的**创意内容创作者**代理：

```py
creative_content_creator_agent = Agent(
   role="Creative Content Creator",
   goal=dedent("""\
    Develop compelling and innovative content
    for ad campaigns, with a focus customer specific ad copies."""),
   backstory=dedent("""\
    As a Creative Content Creator at a top-tier digital marketing
    agency, you excel in crafting advertisements that resonate with
    potential customers. Your expertise lies in turning marketing
    strategies into engaging stories that capture attention and
    inspire buying action."""),
   llm=llm,
   verbose=True
  )
```

该代理将与之前的“老板”代理合作，组成新的团队，执行以下任务：

```py
get_ad_campaign_written_task = f'''
You're creating a targeted marketing campaign tailored to what we know 
about our customers.
For each customer, we have chosen three products to promote in the next
campaign. This selection is tailored specifically to the customer: {selection}
To end this campaign succesfully we will need a promotional message 
advertising these products to the customer with the ultimate intent that 
they buy from us. This message should be around 3 paragraphs, so that it can
be easily integrated into the full letter. For example:
Tired of making dinner, get our best ready made canned tuna.
Your lifestyle deserves a taste of this fresh lobster.
In the weekends, go on a day trip with the kids with these new lunch box containers.

You need to review, approve, and delegate follow up work if necessary to 
have the complete promotional message. When delegating work send the full
draft as part of the information.
Your final answer MUST include the 3 products from the list,
each with a short promotional message.'''
```

每个代理都与他们将使用的大型语言模型连接。我将他们连接到[Mistral API](https://docs.mistral.ai/)。我首先集成了他们的Mistral 7B Instruct v0.2模型，但它推理得非常奇怪，例如对于选择尿布的解释：“*虽然客户目前是单身，但她未来可能会有孩子。尿布是未来为父母准备的实际投资，因为它们是照顾婴儿和幼儿所必需的。*”

通过升级到更大的型号：Mixtral 8x7B，这个问题得以解决。我再也没见过类似的逻辑缺陷。

# **结果**

在我设置好两组人员后，是时候让他们开始分析10个客户了。通过将详细模式设置为2，我能够在屏幕后面查看并跟踪代理的思考和互动。例如：

```py
Thought: Do I need to use a tool? Yes
Action: Ask question to co-worker
Action Input: profiler|Can you provide a customer profile based on the given demographic and behavioral data?|The customer was born in 1968\. Its sex is M. Its marital status is divorced. Its yearly household income percentile is 45\. The customer has 0 toddlers. The customer has 0 teenage kids. The customer highest education is high_school. The customer spends on average 45 $ on wine. The customer spends on average 50 $ on vegetables. The customer spends on average 0 $ on toys. The customer's coupon use is high.

Observation: The profiler responds with: Our customer is a 54-year-old divorced man with a mid-level income. He has no children living with him. He has a high school education. He enjoys wine and vegetables, and does not spend on toys. He is likely to be budget-conscious and looking for deals, given his high coupon use.

Thought: Do I need to use a tool? Yes
Action: Ask question to co-worker
Action Input: product specialist|Which three products would you recommend for this customer, based on his profile?|54-year-old divorced man with a mid-level income, high school education, enjoys wine and vegetables, budget-conscious, and uses coupons.

Observation: The product specialist responds with: Based on the customer's profile, I would recommend:
1\. Laundry detergent: A necessary household item, and we can offer a deal or discount to appeal to his budget-conscious nature.
2\. Fresh lettuce and vegetables: Given his interest in vegetables, promoting fresh produce is a good fit.
3\. Wine or Irish whiskey: Since he spends a significant amount on wine, promoting a higher-end option like Irish whiskey could be appealing, especially if we offer a discount.

Thought: Do I need to use a tool? No
Final Answer:
1\. Laundry detergent : A necessary household item, and we can offer a deal or discount to appeal to his budget-conscious nature.
2\. Fresh lettuce and vegetables : Given his interest in vegetables, promoting fresh produce is a good fit.
3\. Irish whiskey : Since he spends a significant amount on wine, promoting a higher-end option like Irish whiskey could be appealing, especially if we offer a discount.

Observation: I have provided the three recommended products for the customer based on their profile.
```

现在，让我们详细分析两个客户。第一个客户是：

```py
1990,F,single,25,0,0,associates,10,35,0,low
```

将其交给**分析代理**，其回应是：“*我们的客户是一位20多岁末或30岁初的单身女性，收入中等。她喜欢葡萄酒和蔬菜，可能是自己做饭。她没有孩子，具有较高的教育背景，这表明她可能更加注重健康并且生活节奏繁忙。她不常使用优惠券，说明她可能更偏向于便利而非省钱。*”

**产品专家**代理回复：“*根据客户档案，我推荐以下产品：*”

+   *新鲜生菜：考虑到她对蔬菜的兴趣，她可能会喜欢新鲜生菜作为健康的餐食配料。*

+   *睫毛膏：作为单身女性，她可能会喜欢睫毛膏等美容产品，这与她的高学历和忙碌的生活方式相符。*

+   *意大利面罐头（即食）：由于她忙碌的生活方式，她可能没有时间做饭，因此即食意大利面是一个便捷的选择。*

这些选择非常合理！现在让我们看看其他小组提出的促销信息。我已将其粘贴在这个示意图中。产品信息非常精准：

![](../Images/87ce8b71f59b0114d515810727461338.png)

作者提供的图片

让我们看看他们如何处理第二位客户：

```py
1985,M,married,90,2,1,phd,80,100,120,high
```

构建的个人档案非常基础，但完整：“*客户出生于1985年，男性，已婚，年收入位于前90%，有2个幼儿和1个青少年孩子，最高学历博士，花费80美元买葡萄酒，100美元买蔬菜，120美元买玩具，使用优惠券频繁。*”

以下是所选择并推广的产品：

![](../Images/a71b809c7ac1fd13bcab5ede68f97436.png)

作者提供的图片

产品选择和促销文案与客户已知的信息非常匹配。他们因为客户收入较高推荐了和牛肉，因为他是一个有3个孩子的忙碌父母，而游戏则是因为他有青少年孩子。

拉远一点，这里是我从整个小组输出中得到的**观察**：

✔️ 尿布只推荐给有幼儿的客户

✔️ 睫毛膏只推荐给女性客户

✔️ 和牛肉只推荐给最高收入的客户

✔️ 洗衣粉被推荐了4次。两次推荐给女性，也有两次推荐给未婚（！）男性

✔️ 香烟从未被推荐过（*拍了拍 Mixtral* —— 好AI）

✔️ 所有的配套文本都根据客户的情况量身定制，而不会显得过于具体或包含虚假信息

✔️ 我从未看到不合适的产品建议

这正是一个营销人员所期待的！团队似乎具备一些常识，并且能够围绕这些常识进行推理。

# 伦理性

当我说常识时，请注意，它并不是普遍适用的常识。每个大型语言模型都固有地带有一定的偏见，这取决于训练数据和训练优化（例如，RLHF）。

举个例子：我发现，在我的例子中，模型只向未婚男性推荐洗衣粉。这是偶然的，还是假设已婚男性在家庭中不洗衣服？也许是因为模型只见过那些只有女性做洗衣的夫妻？这从统计学上可能是正确的，但事实是我们不知道还存在哪些其他偏见。我们应该小心在没有审查的情况下将这些框架投入生产，因为它们可能会传播关于性别、政治、宗教、种族等的刻板印象。这仍然是一个正在进行的研究领域，旨在减少这些风险。更多的阅读资料我可以推荐：OpenAI 的 [博客文章](https://openai.com/blog/how-should-ai-systems-behave)、[🤗 评估库](https://huggingface.co/blog/evaluating-llm-bias) 和 [实践建议](https://huggingface.co/blog/ethics-soc-2)。

# 结论

我的结果表明，AI代理能够根据客户的个人资料推理出最佳产品，并提供精准的促销信息，量身定制给每个客户，考虑到年龄、性别、婚姻状况和收入等因素。

我们看到自主AI代理协作、委派或被分配子任务、呼叫帮助并互相检查工作。当它们这样做时，它们的输出质量显著提升，从而做出更可靠、更符合常识的决策。像 CrewAI 这样的框架通过允许自然语言指令提供了一种直接而有效的方式来利用这一能力。我还讨论了大型语言模型中内嵌的常识，以及偏见的存在，应该努力追求公平。

这只是一个简单的例子，专注于营销。还有许多其他的应用场景：数据解析、自动社交媒体发布、Markdown 校验器或城市旅行规划器……可能性无穷无尽。也许它能帮助你应对下一个挑战？

[](https://colab.research.google.com/github/Toon-nooT/notebooks/blob/main/Marketing_Campaign__With_CrewAI.ipynb?source=post_page-----17f84378430b--------------------------------) [## Colab Notebook

### [CrewAI 强大的 AI 代理协作：营销应用案例](https://colab.research.google.com/github/Toon-nooT/notebooks/blob/main/Marketing_Campaign__With_CrewAI.ipynb?source=post_page-----17f84378430b--------------------------------)  [## 首页

### 用于编排角色扮演、自主 AI 代理的前沿框架。通过促进协作智能…

[docs.crewai.com](https://docs.crewai.com/?source=post_page-----17f84378430b--------------------------------) [](https://github.com/joaomdmoura/crewAI?source=post_page-----17f84378430b--------------------------------) [## GitHub - joaomdmoura/crewAI：用于编排角色扮演、自主 AI 代理的框架。通过…

### 用于编排角色扮演、自主 AI 代理的框架。通过促进协作智能，CrewAI…

[github.com](https://github.com/joaomdmoura/crewAI?source=post_page-----17f84378430b--------------------------------)
