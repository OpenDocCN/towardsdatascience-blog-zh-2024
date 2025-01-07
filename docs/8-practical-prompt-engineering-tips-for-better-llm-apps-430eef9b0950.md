# 8个实用的提示工程技巧，帮助提升LLM应用

> 原文：[https://towardsdatascience.com/8-practical-prompt-engineering-tips-for-better-llm-apps-430eef9b0950?source=collection_archive---------4-----------------------#2024-08-01](https://towardsdatascience.com/8-practical-prompt-engineering-tips-for-better-llm-apps-430eef9b0950?source=collection_archive---------4-----------------------#2024-08-01)

[](https://medium.com/@almogbaku?source=post_page---byline--430eef9b0950--------------------------------)[![Almog Baku](../Images/3ac36986f6ca0ba56c8edced6ec7dd07.png)](https://medium.com/@almogbaku?source=post_page---byline--430eef9b0950--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--430eef9b0950--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--430eef9b0950--------------------------------) [Almog Baku](https://medium.com/@almogbaku?source=post_page---byline--430eef9b0950--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--430eef9b0950--------------------------------) ·9分钟阅读·2024年8月1日

--

提示工程无疑是开发LLM本地应用程序中最关键的技能，因为制定正确的提示可以显著影响应用程序的性能和可靠性。

在过去的两年里，我一直在帮助组织构建和部署数十个用于实际案例的LLM应用程序。这段经历让我对有效的提示技巧有了宝贵的洞察。

本文基于[LLM三角原理](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e)介绍了**八个实用的提示**，帮助提升你的LLM提示工程技能。

> “LLM本地应用程序是10%的复杂模型和90%的实验性数据驱动工程工作。”

![](../Images/605d5e3a9c4a90b2659fc5bd57d9585e.png)

（由Midjourney生成）

在本文中，我们将使用“登录页面生成器”示例来演示如何应用每个技巧，以增强实际的LLM应用程序。

> 你也可以查看[登录页面生成器示例的完整脚本](https://gist.github.com/AlmogBaku/5de026a355f9ef8984fa6a5bebd8f51f)，以便全面了解。
> 
> **注意：** 这是一个简化的非生产示例，旨在说明这些技巧。代码和提示故意简化，以突出讨论的概念。

# 1. 定义明确的认知过程边界

从定义每个代理或提示的目标开始。坚持**每个代理一个认知过程类型**，例如：构思一个登录页面、选择组件或为特定部分生成内容。

拥有明确的边界有助于保持LLM交互中的专注和清晰，这与[LLM三角原则](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e#3221)的[*工程技术*](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e#3221)顶端保持一致。

> *“*我们流程中的每个步骤都是一个独立的过程，必须完成才能实现我们的任务。”* 

例如，避免在同一提示中组合不同的认知过程，这可能会产生不理想的结果。相反，可以将这些过程分解成独立的、专注的代理：

```py
def generate_landing_page_concept(input_data: LandingPageInput) -> LandingPageConcept:
    """
    Generate a landing page concept based on the input data.
    This function focuses on the creative process of conceptualizing the landing page.
    """
    pass

def select_landing_page_components(concept: LandingPageConcept) -> List[LandingPageComponent]:
    """
    Select appropriate components for the landing page based on the concept.
    This function is responsible only for choosing components,
    not for generating their content or layout.
    """
    pass

def generate_component_content(component: LandingPageComponent, concept: LandingPageConcept) -> ComponentContent:
    """
    Generate content for a specific landing page component.
    This function focuses on creating appropriate content based on the component type and overall concept.
    """
    pass
```

通过为每个代理定义*明确的边界*，我们可以确保工作流中的每个步骤都专门针对特定的心理任务。这将**提高输出的质量**，并使**调试**和精炼变得**更容易**。

# 2. 明确指定输入/输出

定义**明确的输入和输出**结构，以反映目标并创建明确的数据模型。这个实践涉及到[LLM三角原则](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e)的[*工程技术*](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e#3221)和[*上下文数据*](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e#7b49)的顶端。

```py
class LandingPageInput(BaseModel):
    brand: str
    product_desc: str
    campaign_desc: str
    cta_message: str
    target_audience: str
    unique_selling_points: List[str]

class LandingPageConcept(BaseModel):
    campaign_desc_reflection: str
    campaign_motivation: str
    campaign_narrative: str
    campaign_title_types: List[str]
    campaign_title: str
    tone_and_style: List[str]
```

这些[Pydantic](https://pydantic.dev/)模型定义了我们**输入和输出数据**的结构，并为代理定义了明确的边界和期望。

# 3. 实施防护措施

放置验证以确保LLM输出的质量和适度。[Pydantic](https://pydantic.dev/)非常适合实现这些防护措施，我们可以利用其原生功能来实现。

```py
class LandingPageConcept(BaseModel):
    campaign_narrative: str = Field(..., min_length=50)  # native validations
    tone_and_style: List[str] = Field(..., min_items=2)  # native validations

    # ...rest of the fields... #

    @field_validator("campaign_narrative")
    @classmethod
    def validate_campaign_narrative(cls, v):
        """Validate the campaign narrative against the content policy, using another AI model."""
        response = client.moderations.create(input=v)

        if response.results[0].flagged:
            raise ValueError("The provided text violates the content policy.")

        return v
```

在这个例子中，通过定义两种类型的验证器来确保我们应用程序的质量：

+   **使用Pydantic的** `**Field**` 来定义简单的验证，例如至少包含2个语调/风格属性，或叙述至少包含50个字符

+   **使用自定义的** `**field_validator**` 来确保生成的叙述符合我们的内容审查政策（使用AI）。

# 4. 与人类认知过程对齐

通过将复杂的任务分解为遵循逻辑顺序的小步骤，来构建模拟人类认知过程的LLM工作流。为此，遵循[LLM三角原则](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e#be9a)中的*SOP（标准操作程序）*指导原则。

> *“没有标准操作程序（SOP），即使是最强大的LLM也无法 consistently 交付一致的高质量结果。”*

## 4.1 捕捉隐藏的隐性认知跳跃

在我们的例子中，我们期望模型返回`LandingPageConcept`作为结果。通过要求模型输出某些字段，我们指导LLM，就像人类市场营销人员或设计师在创建着陆页概念时可能采取的方式一样。

```py
class LandingPageConcept(BaseModel):
    campaign_desc_reflection: str  # Encourages analysis of the campaign description
    campaign_motivation: str       # Prompts thinking about the 'why' behind the campaign
    campaign_narrative: str        # Guides creation of a cohesive story for the landing page
    campaign_title_types: List[str]# Promotes brainstorming different title approaches
    campaign_title: str            # The final decision on the title
    tone_and_style: List[str]      # Defines the overall feel of the landing page
```

`LandingPageConcept` 结构鼓励 LLM 跟随类似人类的推理过程，模仿专家在直觉上所做的 *微妙心理跃迁*（[隐性认知“跳跃”](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e#f8c9)），就像我们在标准操作程序（SOP）中所建模的那样。

## 4.2 将复杂的过程分解为多个步骤/代理

对于复杂任务，将过程分解为多个步骤，每个步骤由单独的 LLM 调用或 *"代理"* 处理：

```py
async def generate_landing_page(input_data: LandingPageInput) -> LandingPageOutput:
    # Step 1: Conceptualize the campaign
    concept = await generate_concept(input_data)

    # Step 2: Select appropriate components
    selected_components = await select_components(concept)

    # Step 3: Generate content for each selected component
    component_contents = {
        component: await generate_component_content(input_data, concept, component)
        for component in selected_components
    }

    # Step 4: Compose the final HTML
    html = await compose_html(concept, component_contents)

    return LandingPageOutput(concept, selected_components, component_contents, html)
```

![](../Images/7faf32bd345890ba949330bc1e8ad532.png)

多代理过程代码的示意图。（作者提供的图片）

这种多代理方法与人类解决复杂问题的方式一致——通过将问题分解成更小的部分。

# 5. 利用结构化数据（YAML）

[YAML](https://en.wikipedia.org/wiki/YAML) 是一种 *流行的* 人类友好的数据序列化格式。它设计上既便于人类阅读，又易于机器解析——这使得它成为 LLM 使用中的经典选择。

我发现 YAML 在 LLM 交互中特别有效，并且在不同模型之间能产生更好的结果。它将令牌处理集中在有价值的内容上，而非语法。

YAML 在不同的 LLM 提供者之间具有更强的可移植性，允许你保持结构化的输出格式。

```py
async def generate_component_content(input_data: LandingPageInput, concept: LandingPageConcept,component: LandingPageComponent) -> ComponentContent:
    few_shots = {
        LandingPageComponent.HERO: {
            "input": LandingPageInput(
                brand="Mustacher",
                product_desc="Luxurious mustache cream for grooming and styling",
                # ... rest of the input data ...
            ),
            "concept": LandingPageConcept(
                campaign_title="Celebrate Dad's Dash of Distinction",
                tone_and_style=["Warm", "Slightly humorous", "Nostalgic"]
                # ... rest of the concept ...
            ),
            "output": ComponentContent(
                motivation="The hero section captures attention and communicates the core value proposition.",
                content={
                    "headline": "Honor Dad's Distinction",
                    "subheadline": "The Art of Mustache Care",
                    "cta_button": "Shop Now"
                }
            )
        },
        # Add more component examples as needed
    }

    sys = "Craft landing page component content. Respond in YAML with motivation and content structure as shown."

    messages = [{"role": "system", "content": sys}]
    messages.extend([
        message for example in few_shots.values() for message in [
            {"role": "user", "content": to_yaml({"input": example["input"], "concept": example["concept"], "component": component.value})},
            {"role": "assistant", "content": to_yaml(example["output"])}
        ]
    ])
    messages.append({"role": "user", "content": to_yaml({"input": input_data, "concept": concept, "component": component.value})})

    response = await client.chat.completions.create(model="gpt-4o", messages=messages)
    raw_content = yaml.safe_load(sanitize_code_block(response.choices[0].message.content))
    return ComponentContent(**raw_content)
```

请注意我们如何使用少样本示例来 *“通过展示，而不是告诉”* 预期的 YAML 格式。与提示中明确的输出结构指令相比，这种方法更为有效。

# 6. 精心设计你的上下文数据

仔细考虑如何将数据建模并呈现给 LLM。这一提示对于 [*上下文数据*](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e#7b49) 是 [LLM 三角原理](/the-llm-triangle-principles-to-architect-reliable-ai-apps-d3753dd8542e#7b49) 顶端的关键。

> *“即使是最强大的模型，也需要相关且结构化的上下文数据才能发挥作用。”*

不要丢弃你所有关于模型的数据，而是将与你定义的目标相关的信息提供给模型。

```py
async def select_components(concept: LandingPageConcept) -> List[LandingPageComponent]:
    sys_template = jinja_env.from_string("""
    Your task is to select the most appropriate components for a landing page based on the provided concept.
    Choose from the following components:
    {% for component in components %}
    - {{ component.value }}
    {% endfor %}
    You MUST respond ONLY in a valid YAML list of selected components.
    """)

    sys = sys_template.render(components=LandingPageComponent)

    prompt = jinja_env.from_string("""
    Campaign title: "{{ concept.campaign_title }}"
    Campaign narrative: "{{ concept.campaign_narrative }}"
    Tone and style attributes: {{ concept.tone_and_style | join(', ') }}
    """)

    messages = [{"role": "system", "content": sys}] + few_shots + [
        {"role": "user", "content": prompt.render(concept=concept)}]

    response = await client.chat.completions.create(model="gpt-4", messages=messages)

    selected_components = yaml.safe_load(response.choices[0].message.content)
    return [LandingPageComponent(component) for component in selected_components]
```

在这个示例中，我们使用 [Jinja](https://jinja.palletsprojects.com/en/3.1.x/) 模板动态生成提示。这为每次 LLM 交互优雅地创建了聚焦且相关的上下文。

> “数据为 LLM 原生应用提供动力。战略性地设计上下文数据能释放它们的真正潜力。”

# 6.1 利用少样本学习的力量

少样本学习是提示工程中必不可少的技术。向 LLM 提供相关的示例可以显著提高其对任务的理解。

请注意，在我们下面讨论的两种方法中，我们 **重用我们的 Pydantic 模型进行少样本学习**——这一技巧确保了示例和实际任务之间的一致性！不幸的是，我是通过艰难的方式学到这一点的。

## 6.1.1 示例 少样本学习

看看第 5 节中的 `few_shots` 字典。在这种方法中：

示例作为单独的用户和助手消息添加到`消息`列表中，随后是实际的用户输入。

```py
messages.extend([
    message for example in few_shots for message in [
        {"role": "user", "content": to_yaml(example["input"])},
        {"role": "assistant", "content": to_yaml(example["output"])}
    ]
])
# then we can add the user prompt
messages.append({"role": "user", "content": to_yaml(input_data)})
```

通过将示例作为`消息`放置，我们与指令模型的训练方法保持一致。这使得模型在处理用户输入之前，能看到多个“示例交互”——帮助它理解预期的输入输出模式。

随着应用程序的增长，你可以添加更多的少量示例（few-shots）来涵盖更多的使用案例。对于更高级的应用，考虑实现[动态少量示例](https://arxiv.org/abs/1804.09458)选择，根据当前输入选择最相关的示例。

## 6.1.2 任务特定的少量示例学习

这种方法使用与当前任务直接相关的示例*在* ***提示*** *中*。例如，这个提示模板用于生成额外的独特卖点：

```py
Generate {{ num_points }} more unique selling points for our {{ brand }} {{ product_desc }}, following this style:
{% for point in existing_points %}
- {{ point }}
{% endfor %}
```

这通过直接将示例包含在提示中，而不是作为单独的消息，提供了针对特定内容生成任务的有针对性的指导。

# 7\. KISS — 保持简单，傻瓜

尽管像“思维树”（Tree of Thoughts）或“思维图”（Graph of Thoughts）这样的精妙提示工程技术很有趣，特别是在研究中，但我发现它们在实际生产中相当不切实际，并且往往过于复杂。对于真实的应用程序，重点应该放在设计合适的LLM架构（即工作流工程）上。

这同样适用于在你的LLM应用中使用代理。理解标准代理与自主代理之间的区别至关重要：

> **代理：** “通过做XYZ把我从A带到B。”
> 
> **自主代理：** “通过做某件事把我从A带到B，我不在乎怎么做。”

虽然自主代理提供了灵活性和更快的开发速度，但它们也可能带来不可预测性和调试挑战。谨慎使用自主代理——只有在其优点*明显超过*潜在的控制损失和复杂性增加时才使用。

![](../Images/cd8b67d63da85cbb333002cf95f30b70.png)

（由Midjourney生成）

# 8\. 迭代，迭代，再迭代！

持续的实验对于改进你的LLM原生应用至关重要。不要对实验的想法感到害怕——它们可以像调整一个提示那么小。如在["构建LLM应用：清晰的逐步指南"](/building-llm-apps-a-clear-step-by-step-guide-1fe1e6ef60fd)中所述，*建立基准*并根据它跟踪改进是至关重要的。

> 像其他所有“AI”领域的技术一样，LLM原生应用需要**研究和实验的*思维方式*。**

另一个好技巧是尝试在比你计划在生产中使用的模型性能更弱的模型上测试你的提示（例如，开源的8B模型）——在较小模型上表现“还行”的提示在更大模型上会表现得更好。

# 结论

这八个技巧为有效的提示工程提供了坚实的基础，适用于LLM原生应用。通过在提示中应用这些技巧，你将能够创建更可靠、高效和可扩展的LLM原生应用。

记住，目标不是创建最复杂的系统，而是构建一个在现实世界中有效的系统。继续实验、学习和构建——可能性是无限的。

如果你觉得这篇文章对你有帮助，请在Medium上给它一些**掌声**👏并**分享**给你身边的AI爱好者。你的支持对我意义重大！🌍

让我们继续保持对话——随时通过[电子邮件](mailto:almog.baku@gmail.com)或[在LinkedIn上联系](https://www.linkedin.com/in/almogbaku/) 🤝

特别感谢[Liron Izhaki Allerhand](https://medium.com/u/251cd1007ce8?source=post_page---user_mention--430eef9b0950--------------------------------)和[Yam Peleg](https://medium.com/u/e0607cd7607d?source=post_page---user_mention--430eef9b0950--------------------------------)；这篇文章基于我们之间的对话和见解。
