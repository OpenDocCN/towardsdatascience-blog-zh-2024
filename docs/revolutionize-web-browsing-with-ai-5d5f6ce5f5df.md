# 用AI彻底改变网页浏览

> 原文：[https://towardsdatascience.com/revolutionize-web-browsing-with-ai-5d5f6ce5f5df?source=collection_archive---------4-----------------------#2024-03-10](https://towardsdatascience.com/revolutionize-web-browsing-with-ai-5d5f6ce5f5df?source=collection_archive---------4-----------------------#2024-03-10)

## 使用GPT-4V和Puppeteer解锁互动在线体验

[](https://medium.com/@bianbianzhu123?source=post_page---byline--5d5f6ce5f5df--------------------------------)[![Tianyi Li](../Images/40fce472f42c650daa1433641bf732df.png)](https://medium.com/@bianbianzhu123?source=post_page---byline--5d5f6ce5f5df--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5d5f6ce5f5df--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5d5f6ce5f5df--------------------------------) [Tianyi Li](https://medium.com/@bianbianzhu123?source=post_page---byline--5d5f6ce5f5df--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5d5f6ce5f5df--------------------------------) ·23分钟阅读·2024年3月10日

--

作者：[Tianyi Li](https://medium.com/u/4092d7367010?source=post_page---user_mention--5d5f6ce5f5df--------------------------------)，[Selina Li](https://medium.com/u/7b9ea39b0d79?source=post_page---user_mention--5d5f6ce5f5df--------------------------------)

![](../Images/5534a8ac0a28ede1472534e7d922e9d6.png)

图片由作者使用DALL·E生成

· [介绍](#ced3)

· [潜在的使用案例](#b5c4)

· [高级工作流](#efe3)

· [架构](#2455)

∘ [开始使用](#1813)

∘ [选择正确的路径](#3fd7)

∘ [这是一个循环！](#ce07)

∘ [目录结构](#30b8)

∘ [浏览器控制服务](#c456)

∘ [元素注释服务](#d19c)

· [结论](#b379)

# 介绍

假设你非常想参加本月在你所在城市举办的AI活动，但你心中有一些特定的标准，可能与活动的时间安排或重点内容有关。通常，这将涉及以下过程：

+   使用诸如“本月[你的城市]的AI事件”之类的关键词启动网络搜索。

+   从搜索结果中筛选出看起来有潜力的链接。

+   浏览所选网站以确定其相关性，可能需要通过其他链接进一步深入了解。

+   经过反复推敲，最终找到了符合你标准的活动，并将其详细信息记录到日历中。

如果我们分解上述过程，它基本上涉及的步骤可以归类为以下几类：

1.  控制浏览器，比如访问网址、点击链接、返回等。

1.  浏览页面内容

1.  根据页面内容做出决策，例如确定哪个链接与你的查询相关。

通过利用新兴的大型语言模型（LLM）技术，我们现在可以通过一个**LLM驱动的AI代理自动化整个过程**。

进入AI代理，它正如我们上面所描述的那样，做着你所做的一切。

1.  **浏览器控制**：AI使用像`Puppeteer`这样的工具来浏览互联网。可以把Puppeteer看作是AI的双手，它能轻松地打开标签页、点击链接，并在网页之间导航。

1.  **内容浏览**：可以把这个看作是AI的眼睛。`Puppeteer`能够拍摄网页截图，并将其传递给AI。

1.  **决策制定**：这是AI的大脑发挥作用的地方，借助大型语言模型（LLM）。它评估每个页面的截图，分析图像，判断相关性并决定下一步，模拟人类判断。

在本文中，我们将探索并构建一个利用OpenAI的`gpt-4-vision-preview`模型的AI代理。该模型可以**分析图像**并提供文本回复。

这个代理将能够与用户互动，控制网页浏览器，并处理数据。我们将探讨它的结构以及如何工作。

本文的灵感来源于一个YouTube视频 [GPT4V + Puppeteer = AI代理像人类一样浏览网页？🤖](https://www.youtube.com/watch?v=IXRkmqEYGZA&t=184s)，由[AI Jason](https://www.youtube.com/@AIJasonZ)发布。

**本文中展示的所有代码都可以在这个Github仓库中找到** [**AI Web Agent**](https://github.com/bianbianzhu/ai-web-agent/tree/main/agent)**.**

# 潜在的使用案例

+   与文本转语音配对，它可以帮助视力障碍者浏览网页。

+   快速定位电子商务网站上的产品。

+   自动浏览具有动态加载内容的网站（例如房地产列表）

+   自动浏览需要个人登录的网站（例如 LinkedIn、Instagram、Facebook）

+   参加在线测验和测试

+   玩网页游戏

+   在线抢座/抢票

+   自动化应用功能测试

# 高层次工作流程

![](../Images/586a1f94e2d077c069b61fadefe44de0.png)

显示代理高层次工作流程的流程图。

首先，用户向代理提出问题。

在接收到问题后，GPT-4V将返回一个URL作为网页搜索的入口。该URL可以由用户在问题中指定；如果未指定，默认情况下，它将是GPT从用户问题中提取的关键词进行Google搜索。

在接收到URL后，Puppeteer将打开该URL的网页。它会高亮显示页面上的所有互动元素，如链接、点击、按钮，然后截图并返回。

在接收到截图后，GPT-4V将查看页面并处理高亮显示的信息。作为大脑，GPT将决定是否已经得到了一个好的答案来回答用户的问题。

如果有一个好的答案，GPT会将答案返回给用户。

如果没有，GPT-4V 将决定下一个步骤应访问哪个 URL 或点击哪个元素。Puppeteer 将再次打开新的 URL，突出显示交互元素，并截取屏幕截图以供进一步处理。

这个过程将反复进行，直到 GPT 决定它已为用户找到了一个好的答案。

# 架构

既然我们现在已经了解了 AI 代理是如何工作的，接下来让我们看看代理的架构。代理的结构如下：

![](../Images/5a8375fa18a3f3434a9572dbdf104d0b.png)

代理架构概览

这种架构乍一看可能很复杂，但一旦理解了不同组件及其如何协同工作，其实非常简单。我们来逐步解析。

## 开始

这是工作流程的起点。用户通过提供明确任务的提示（`user task prompt`）与代理进行交互。同时，代理获得一些默认指令（`system prompt`），告诉它如何执行任务。这些指令告诉代理它的角色是什么，如何格式化回答，以及其他重要信息。

这是起始点的一个简单视图：

![](../Images/fffb4608e71295968f9cdae12e5bada0.png)

初始步骤

`user task prompt` 的示例可能是：

```py
I'm looking for AI events in Melbourne this month. Please prioritize events that are on weekends.
```

这是系统提示可能的样子：

![](../Images/dd728c47d52c1476b78ddd1a127ed3d6.png)

一个典型的系统提示，提供代理的上下文。

致谢：[JayZeeDesign](https://github.com/JayZeeDesign/Scrape-anything---Web-AI-agent)

系统提示非常关键，因为它们帮助代理决定如何行动。以下三个主要领域用不同颜色标出：

+   代理的角色是什么。（水绿色）

+   代理应该如何呈现其答案。（红色）

+   额外的指令，帮助代理执行任务。（黄色）

然后，这些提示会发送到 OpenAI 服务，该服务负责生成回答。

根据这些提示，代理的回答将被整理成仅 3 种类型：

1.  `{ "url": "https://www.example.com" }` - 代理希望导航到一个特定的网页。

1.  `{ "click": "text on a button" }` - 代理希望点击页面上的特定元素。

1.  `regular message` - 代理已完成任务并将所需信息提取给用户。

对于初始对话，回答通常会是一个 Google 搜索 URL，例如 `https://www.google.com/search?q=AI+events+in+Melbourne+March`。然后，该回答会触发浏览器控制服务，导航到该 URL。

**起始点的代码实现**

```py
const messages: ChatCompletionMessageParam[] = [];

// STEP 1: Welcome the user
console.log(staticMessageMap.welcome);
// STEP 2: provide the context of the conversation - system prompt
messages.push(promptMap.context());
// STEP 3: Ask and apply the user's query as a task - user task prompt
const userPrompt = await userPromptInterfaceV2(staticMessageMap.you);
messages.push(promptMap.task(userPrompt));
```

以下函数在终端中创建用户输入查询的界面。

```py
/**
 * This service creates a user prompt interface and returns a promise that resolves to the user's input. Allow user to input in the `terminal`.
 * @param query - The message to hint the user on what to input
 * @returns A promise that resolves to the user's input
 */
export const userPromptInterfaceV2 = async (query: string) => {
  // Create an interface to read input from the user
  const userInterface = readline.createInterface({
    input: process.stdin,
    output: process.stdout,
  });

  // Return a promise that resolves to the user's input
  // The userInterface.question method takes a query and a callback function
  // The reason for using a promise is to make the user's input accessible outside of the callback function
  return new Promise<string>((resolve) => {
    userInterface.question(query, (input) => {
      resolve(input);
      userInterface.close(); // Close the user interface
    });
  });
};
```

你可能会注意到代码中的 `promptMap` 对象。它是一个返回所有提示的函数映射。将其设为函数使得这些提示具有动态性，可以根据对话的上下文发生变化。以下是一个示例：

```py
export const promptMap = {
  context: (
    role: "teacher" | "student" | "AI"
  ): ChatCompletionMessageParam => ({
    role: "system",
    content: `You are a ${role}. You will be given instructions on what to do by browsing. You are connected to a web browser and you will be given the screenshot of the website you are on.`,
  }),
};
```

一个重要的注意事项是，为了让LLM能够记住对话的`memory`，我们需要始终将新的提示推送到消息数组中，并将整个数组发送给LLM。这可能会导致令牌数暴增。使用`max_tokens`参数来建立安全网。

下面是如何使用openai服务生成响应的方法。

```py
const taskFlow = async (): Promise<void> => {
  console.log(`${staticMessageMap.agent}Let me think...`);
  // Start the conversation with the LLM
  const response = await openai.chat.completions.create({
    model: "gpt-4-vision-preview",
    max_tokens: 1024,
    messages, // The messages array which contains the conversation history is sent to the LLM
    temperature: 0,
  });

// For the initial conversation, the agent will respond with the url (google search if not provided by the user)
  const { message } = response.choices[0];
  const { content: messageText } = message;
  if (messageText === null) {
    throw new Error("The response message text is null");
  }
  // Show the response in the terminal
  console.log(`${staticMessageMap.agent}${messageText}`);
  // Memorize the answer from agent
  messages.push({
    role: "assistant",
    content: messageText,
  });
};
await taskFlow();
```

当然，要使用`openai`服务，你需要安装`openai`包并配置API密钥。这里有一个单独的文件来处理openai服务。

```py
import dotenv from "dotenv";
dotenv.config();
import OpenAI from "openai";

/**
 * An instance of the OpenAI Class that can invoke the API methods
 * @example `openai.chat.completions.create`
 */
export const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY,
});
```

## 选择正确的路径

如前所述，LLM响应将仅被塑造成三种类型中的一种。每种类型都会触发不同的浏览器控制服务进行相应的操作。

![](../Images/6ca654ab4349b5e66c39e9985ead90bc.png)

代理程序根据LLM的反馈选择最佳路径。

**URL响应流**

+   **响应格式 { “url”: “**[**https://www.example.com**](https://www.example.com)**" }**

+   **指示代理程序导航到特定网页。**

+   **此响应将触发脚本中的以下步骤：**

1.  从响应中提取URL

1.  使用Puppeteer打开浏览器并导航到URL

1.  然后，代理程序会截图并通过另一个特殊指令提示发送给LLM，分析页面内容并决定下一步操作。

![](../Images/97229ad5607b2a6fcf9d24c47a1fe5a3.png)

‘url’响应消息的处理流程。

**点击响应流**

+   **响应格式 { “click”: “按钮上的文本” }**

+   **指示代理程序点击页面上的特定元素。**

+   **代理程序将执行以下步骤：**

1.  从响应中提取链接文本

1.  然后，代理程序使用Puppeteer查找匹配文本的元素并点击它

1.  代理程序会截图并通过另一个特殊指令提示发送给LLM，分析页面内容并决定下一步操作。

![](../Images/a2d29ca66285afcabd6ea669bf885bc1.png)

‘click’响应消息的处理流程。

让我们来看看代理程序在截图页面后发送给LLM的`特殊指令提示`。

![](../Images/ff292db3f933db519ba4f04613fe2010.png)

来源：[JayZeeDesign](https://github.com/JayZeeDesign/Scrape-anything---Web-AI-agent)

这个提示基本上告诉大语言模型（LLM）分析页面的截图并决定下一步操作。响应格式应该与我们在系统提示中提到的完全一致。对于LLM，通常需要反复说明规则或指南，以确保一致的表现。

**常规消息流 — 常规消息**

+   指示代理程序已完成任务并向用户提取所需信息。

+   这通常是当前任务的结束。

+   代理程序将简单地向用户展示信息。

**路径选择的代码实现**

```py
// Use typescript to define the 3 types of response
export type ResponseMessage =
  | {
      type: ResponseMessageCategory.URL;
      url: string;
    }
  | {
      type: ResponseMessageCategory.CLICK;
      linkText: string;
    }
  // This initial type is simply a placeholder to indicate the start of the conversation in which the LLM has not yet provided a response (not relevant to the path selection logic)
  | {
      type: ResponseMessageCategory.INITIAL;
      text: "initial";
    }
  | {
      type: ResponseMessageCategory.REGULAR;
      text: string;
    };
```

一个将LLM的**纯文本响应**转换为定义类型的函数。

```py
export const convertTextToResponseMessage = (text: string): ResponseMessage => {
  // The `extractActionFromString` function is a helper function that simply checks if a string contains a particular pattern, such as '{"url": "' or '{"click": "' which is directly related to the response format. See the repo for the full implementation.
  if (extractActionFromString(text, ResponseMessageCategory.URL) !== null) {
    return {
      type: ResponseMessageCategory.URL,
      // Extract the URL from the response and store it in the `url` property, so it can be accessed easily
      url: extractActionFromString(text, ResponseMessageCategory.URL) as string,
    };
  }

  if (extractActionFromString(text, ResponseMessageCategory.CLICK) !== null) {
    return {
      type: ResponseMessageCategory.CLICK,
      // Extract the link text from the response and store it in the `linkText` property, so it can be accessed easily
      linkText: extractActionFromString(
        text,
        ResponseMessageCategory.CLICK
      ) as string,
    };
  }

  if (text === ResponseMessageCategory.INITIAL) {
    return {
      type: ResponseMessageCategory.INITIAL,
      text,
    };
  }
  return {
    type: ResponseMessageCategory.REGULAR,
    text,
  };
};
```

要实现 `路径选择`，代码应具有以下骨架结构：

```py
// messageText is the plain string response from the LLM (see above code)
// It is then converted to the defined type using the `convertTextToResponseMessage` function
responseMessage = convertTextToResponseMessage(messageText);

// if-else statement to determine the path based on the response type
// URL Response Flow
if (responseMessage.type === ResponseMessageCategory.URL) {
  // 1\. Extract the URL from the response
  const { url } = responseMessage;
  // 2\. Opens a headless browser using Puppeteer and navigates to the URL and take a screenshot of the page
  const imagePath = await screenshot(url, page);
  if (imagePath === undefined) {
    throw new Error("The screenshot path is undefined");
  }
  // converts the screenshot to a format that LLM accepts
  const base64String = await imageToBase64String(imagePath);
  // 3\. Send the screenshot with the instruction prompt back to the LLM
  messages.push(
    promptMap.instruction({
      url: base64String,
      detail: "auto",
    })
  );
  return; // end of this path
}
// Click Response Flow
if (responseMessage.type === ResponseMessageCategory.CLICK) {
  // 1\. Extract the link text from the response
  const { linkText } = responseMessage;
  // 2\. The agent then uses Puppeteer to find the element with the matching text and clicks on it
  const imagePath = await clickNavigationAndScreenshot(linkText, page, browser);
  if (imagePath === undefined) {
    throw new Error("The screenshot path is undefined");
  }
  const base64String = await imageToBase64String(imagePath);
  // 3\. Send the screenshot with the instruction prompt back to the LLM
  messages.push(
    promptMap.instruction({
      url: base64String,
      detail: "auto",
    })
  );
  return;
}
// Regular Message Flow - return message directly
```

## 它是一个循环！

现在，我们清楚了代理可以采取的不同路径。需要注意的一点是，代理会一直循环这些步骤，直到任务完成并响应常规消息。

循环的示例如下：

![](../Images/cece81855a5ee85556bb1ccc9b2efffb.png)

代理工作流是一个循环！

URL 或点击响应流将多次触发，直到代理找到答案。

要实现循环，代码应具有以下结构：

首先，创建一个辅助函数来判断何时停止循环。

```py
export const shouldContinueLoop = (responseMessage: ResponseMessage) => {

// If the response is a regular message, the agent has finished the task and the loop should stop
  if (responseMessage.type === ResponseMessageCategory.REGULAR) {
    return false;
  }
  // Otherwise, the loop should continue
  return true;
};
```

然后是主循环函数。

```py
// The initial response message before the loop starts. Basically, a placeholder.
let responseMessage: ResponseMessage = {
  type: ResponseMessageCategory.INITIAL,
  text: "initial",
};
//==================================LOOP==================================
// shouldContinueLoop determines when to stop the loop
while (shouldContinueLoop(responseMessage)) {

  // +++++ openai service to generate the response +++++
  console.log(`${staticMessageMap.agent}Let me think...`);
  const response = await openai.chat.completions.create({
    model: "gpt-4-vision-preview",
    max_tokens: 1024,
    messages,
    temperature: 0,
  });

  const { message } = response.choices[0];
  const { content: messageText } = message;
  if (messageText === null) {
    throw new Error("The response message text is null");
  }
  console.log(`${staticMessageMap.agent}${messageText}`);
  // +++++++++++++++++++++++++++++++++++++++++++++++++++

  // Memorize the answer from agent
  messages.push({
    role: "assistant",
    content: messageText,
  });

  // +++++ Path Selection +++++
  responseMessage = convertTextToResponseMessage(messageText);
  // URL Response Flow
  if (responseMessage.type === ResponseMessageCategory.URL) {
    const { url } = responseMessage;
    const imagePath = await screenshot(url, page);
    if (imagePath === undefined) {
      throw new Error("The screenshot path is undefined");
    }
    const base64String = await imageToBase64String(imagePath);
    messages.push(
      promptMap.instruction({
        url: base64String,
        detail: "auto",
      })
    );
    // Instead of stopping here, we need to continue the loop for the next step
    continue;
  }
  // Click Response Flow
  if (responseMessage.type === ResponseMessageCategory.CLICK) {
    const { linkText } = responseMessage;
    try {
      const imagePath = await clickNavigationAndScreenshot(
        linkText,
        page,
        browser
      );
      if (imagePath === undefined) {
        throw new Error("The screenshot path is undefined");
      }
      const base64String = await imageToBase64String(imagePath);
      messages.push(
        promptMap.instruction({
          url: base64String,
          detail: "auto",
        })
      );
      // continue the loop for the next step
      continue;
    } catch (err) {
      // Handle the error and retry if the link is not found; sometimes the LLM just comes up with a link that doesn't exist or with a typo
      if (
        err instanceof Error &&
        err.message.includes("Link with text not found")
      ) {
        console.log(`...Error clicking on link: ${err.message}`);
        messages.push(promptMap.retryIfLinkNotFound(linkText));
        continue;
      } else {
        console.log(`...Unexpected error: ${err}. Please try again.`);
        break;
      }
    }
  }
  // ++++++++++++++++++++++++++++
}
```

正如你可能注意到的，我们没有在循环中指定常规消息流。这是因为常规消息不会触发任何浏览器控制服务。它只是结束循环并向用户显示消息，基本上是一个 `console.log` 语句。当然，你可以在常规消息流中添加更多的逻辑，例如将消息保存到 CSV 文件中等。

```py
console.log(`${staticMessageMap.agent}${messageText}`);
```

## 目录结构

在了解了代理的主要流程之后，接下来我们来看一下代理的目录结构。

```py
agent.ts
services/
├── browser-controller.ts
├── data-transformer.ts
├── element-annotator.ts
├── openai.ts
├── prompt-map.ts
└── user-prompt-interface.ts
utils.ts
```

`agent.ts`：这是我们代理的主脚本。它协调其他服务，负责代理的主要执行流程。它包含起始点、主循环和路径选择逻辑。

`services/`：该目录包含代理使用的各种服务。每个服务负责一个特定任务。

+   browser-controller.ts：该服务使用 Puppeteer 控制浏览器。它可以浏览页面、与元素交互以及截图。

+   data-transformer.ts：该服务为代理转换数据。它可以格式化数据、清洗数据，并准备进一步处理。

+   element-annotator.ts：该服务为代理注释 HTML 元素。它可以高亮显示元素，给元素添加唯一标识符等。

+   openai.ts：该服务与 OpenAI API 进行接口对接。

+   prompt-map.ts：该服务将用户提示映射到具体操作。它可以根据用户输入判断代理应该采取的行动。

+   user-prompt-interface.ts：该服务与用户接口，以获取提示。它可以读取用户输入并将其传递给代理。

`utils.ts`：该文件包含代理使用的工具函数。这些函数在代理代码中用于执行常见任务。

有关完整代码实现，请参阅 [Ai Web Agent](https://github.com/bianbianzhu/ai-web-agent)。

## 浏览器控制服务

现在，让我们深入了解代理如何控制浏览器——它的“手”是如何工作的。这里使用了一个名为 `Puppeteer` 的库。它提供了一个基于 Chrome DevTools 协议的高级 API。详细信息请参见 [Puppeteer 文档](https://pptr.dev/)。

本质上，Puppeteer 会在代理从 LLM 接收到响应后被调用。在我们上面提到的不同路径下，Puppeteer 会执行不同的操作。

总体而言，浏览器控制器服务负责以下任务：

1.  导航到一个 URL

1.  点击一个链接

1.  截取页面截图

![](../Images/3a30655d1ff49b52ff7e6a8eaa31cb5f.png)

Puppeteer 在工作流程中执行的任务。

实际上，在代理截图页面之前，还有一个步骤 — `高亮交互元素`。我们将这个服务分离到另一个文件中，以使代码更加模块化，并且易于维护。它将在下一部分讨论。

**浏览器控制器服务的代码实现**

步骤 1 — 初始化浏览器并打开一个新标签页

```py
const browserWindowSize = { width: 900, height: 1600 };

/**
 * This service initializes a new browser session and a new page tab
 * @returns An object containing the browser and the page
 */
export const initController = async () => {
  const pup = puppeteer.default.use(StealthPlugin());
  // launch the browser
  const browser = await pup.launch({
    // detailed configurations
    headless: false, // Determines whether to run the browser in headless mode (without a GUI). boolean | "new" | undefined
    executablePath: process.env.GOOGLE_CHROME_CANARY_PATH, // path to a browser executable to use instead of the bundled Chromium
    userDataDir: process.env.GOOGLE_CHROME_CANARY_USER_DATA_DIR, // Path to a user data directory, i.e, the user profile
    args: [
      `--profile-directory=${process.env.PROFILE}`, // Select the expected profile
      "--disable-setuid-sandbox",
      "--no-sandbox",
      "--no-zygote",
      `--window-size=${browserWindowSize.width},${browserWindowSize.height}`,
    ],
  });
  // open a new tab
  const page = await browser.newPage();
  // set the viewport
  await page.setViewport({
    width: browserWindowSize.width,
    height: browserWindowSize.height,
    deviceScaleFactor: 1,
  });
  // the initialized browser and page are returned
  return { browser, page };
};
```

根据不同的响应流程，代理要么导航到一个 URL 并截图，要么点击一个链接触发导航并截图。

步骤 2 — `URL 响应流程`：导航到一个 URL 并截图

```py
export const navToUrlAndScreenshot = async (url: string, page: Page) => {
  console.log(`...Opening ${url}`);
  // validate the URL
  if (!isValidURL(url)) {
    throw new Error(`Invalid URL: ${url}`);
  }

  // +++++ go to the URL +++++
  await page.goto(url, {
    // a simple logic to determine if the page is loaded: wait 500 ms after the number of active network requests are 2
    waitUntil: "networkidle2",
    timeout: TIMEOUT,
  });
  // +++++++++++++++++++++++++++

  // +++++ take a screenshot of the page +++++
  // also include waiting for the page to load completely
  const imagePath = await waitAndScreenshot(page);
  // +++++++++++++++++++++++++++++++++++++++++
  return imagePath;
};
```

`waitAndScreenshot`函数

```py
const waitAndScreenshot = async (page: Page) => {
  // is the page still loading? `document.readyState` === 'loading' or there is a loading indicator on the page
  const isLoading = await isPageExplicitlyLoading(page);

  // if the page is still loading, wait for the page to load completely
  isLoading && (await waitTillHTMLRendered(page));

  // apply Set-of-Mark Prompting
  console.log(`...Highlight all interactive elements`);
  await highlightInteractiveElements(page);

  // take a screenshot of the page
  console.log(`...Taking screenshot`);
  await page.screenshot({
    //path: "/agent/web-agent-screenshot.jpg" is a wrong path
    path: imagePath,
    fullPage: true,
  });

  return imagePath;
};
```

截图是通过调用`page.screenshot()`简单完成的。你可以根据需要更改配置。

`waitTillHTMLRendered`函数

如图所示，导航到 URL 是通过`page.goto()`完成的。该方法利用`waitUntil`选项来等待页面加载。页面加载完毕后，代理会截图。在截图之前，等待所有视觉内容加载完毕是至关重要的，以确保代理能够获取页面的完整信息。

不幸的是，在许多情况下，`GoToOptions`中的`waitUntil`（page.goto()）不足以等待页面完全加载（特别是动态加载的内容），因此我们需要使用自定义函数`waitTillHTMLRendered`。

这个函数基本上通过`document.readyState`检查页面是否仍在加载，或者页面上是否有加载指示器。如果页面仍在加载，函数将调用`waitTillHTMLRendered`进行全面检查：

1.  每秒检查一次 HTML 大小。

1.  如果 HTML 大小连续 3 秒钟保持不变，则假定页面已加载完毕。

1.  如果页面在 30 秒后仍在加载（超时），则抛出错误。

以下流程图展示了`waitTillHTMLRendered`函数的逻辑：

![](../Images/5620f6b1b199b44699cdf9b89410420e.png)

`waitTillHTMLRendered`函数的流程图。

为了更好地理解`waitAndScreenshot`函数，我们来看看该函数运行时的日志：

![](../Images/76781d17a83b9de376b0b37229a00f7c.png)

捕获动态内容渲染的日志。

在页面**完全**加载后，所有交互元素被高亮显示并截图。

```py
export const waitTillHTMLRendered = async (
  page: Page,
  timeout: number = 30000,
  checkOnlyHTMLBody: boolean = false
) => {
  const waitTimeBetweenChecks: number = 1000;
  const maximumChecks: number = timeout / waitTimeBetweenChecks; // assuming check itself does not take time
  let lastHTMLSize = 0;
  let stableSizeCount = 0;
  const COUNT_THRESHOLD = 3;

  const isSizeStable = (currentSize: number, lastSize: number) => {
    if (currentSize !== lastSize) {
      return false; // still rendering
    } else if (currentSize === lastSize && lastSize === 0) {
      return false; // page remains empty - failed to render
    } else {
      return true; // stable
    }
  };

  for (let i = 0; i < maximumChecks; i++) {
    const html = await page.content();
    const currentHTMLSize = html.length;
    const currentBodyHTMLSize = await page.evaluate(
      () => document.body.innerHTML.length
    );

    const currentSize = checkOnlyHTMLBody
      ? currentBodyHTMLSize
      : currentHTMLSize;
    // logging
    console.log(
      "last: ",
      lastHTMLSize,
      " <> curr: ",
      currentHTMLSize,
      " body html size: ",
      currentBodyHTMLSize
    );

    stableSizeCount = isSizeStable(currentSize, lastHTMLSize)
      ? stableSizeCount + 1
      : 0;
    console.log(`Stable size count: ${stableSizeCount}`);

    // if the HTML size remains the same for 3 consecutive seconds, it assumes the page has finished loading
    if (stableSizeCount >= COUNT_THRESHOLD) {
      console.log("Page rendered fully..");
      break;
    }

    lastHTMLSize = currentSize;
    await page.waitForTimeout(waitTimeBetweenChecks);
  }
};
```

步骤 2（续） — `点击响应流程`：`clickNavigationAndScreenshot`函数

该函数用于点击页面上的特定元素，等待页面完全加载后再截图。对于`click`操作，它使用了另一个名为`clickOnLink`的函数。

```py
export const clickNavigationAndScreenshot = async (
  linkText: string,
  page: Page,
  browser: Browser
) => {
  let imagePath;

  try {
    const navigationPromise = page.waitForNavigation();
    // The Click action
    const clickResponse = await clickOnLink(linkText, page);

    if (!clickResponse) {
      // if the link triggers a navigation on the same page, wait for the page to load completely and then take a screenshot
      await navigationPromise;
      imagePath = await waitAndScreenshot(page);
    } else {
      // if the link opens in a new tab, ignore the navigationPromise as there won't be any navigation
      navigationPromise.catch(() => undefined);
      // switch to the new tab and take a screenshot
      const newPage = await newTabNavigation(clickResponse, page, browser);

      if (newPage === undefined) {
        throw new Error("The new page cannot be opened");
      }

      imagePath = await waitAndScreenshot(newPage);
    }

    return imagePath;
  } catch (err) {
    throw err;
  }
};
```

`clickOnLink`函数

这个功能会遍历所有带有`gpt-link-text`属性的元素（这是元素注释过程中获得的唯一标识符），并点击与LLM提供的链接文本匹配的元素。

```py
const clickOnLink = async (linkText: string, page: Page) => {
  try {
    const clickResponse = await page.evaluate(async (linkText) => {

      const isHTMLElement = (element: Element): element is HTMLElement => {
        return element instanceof HTMLElement;
      };

      const elements = document.querySelectorAll("[gpt-link-text]");

      // loop through all elements with `gpt-link-text` attribute
      for (const element of elements) {

        if (!isHTMLElement(element)) {
          continue;
        }

        // find the element that contains the targeted link text
        if (
          element
            .getAttribute("gpt-link-text")
            ?.includes(linkText.trim().toLowerCase())
        ) {
          // This if statement is to handle the case where the link opens in a new tab
          if (element.getAttribute("target") === "_blank") {
            return element.getAttribute("gpt-link-text");
          }

          // highlight and perform the click action
          element.style.backgroundColor = "rgba(255,255,0,0.25)";
          element.click();
          return;
        }

      }

      // only if the loop ends without returning
      throw new Error(`Link with text not found: "${linkText}"`);
    }, linkText);

    return clickResponse;
  } catch (err) {
    if (err instanceof Error) {
      throw err;
    }
  }
};
```

## 元素注释服务

让我们更深入地了解`highlightInteractiveElements`函数，它在`waitAndScreenshot`内部被调用。

这是一个为代理注释交互式HTML元素的服务。它可以用`红色边框`突出显示元素，并为它们添加唯一标识符。

想象一下，给你的AI代理配上一副特殊的眼镜，让它能看到网站上的交互点——按钮、链接和字段——就像宝藏图上的发光宝藏一样。

这基本上就是`highlightInteractiveElements`函数的作用。它就像数字世界中的荧光笔，在可点击的项目周围画上红框，并用数字标签标记它们。

**通过注释，代理对图像的理解准确性得到了大幅提升**。这个概念叫做`Set-of-Mark Prompting`。

这是注释截图的一个示例：

![](../Images/b85a7f190d2179f011cdda31a784a805.png)

电子商务网站，交互元素被红色高亮显示。图像来自Officeworks网站。

有一篇研究论文详细讨论了这个话题的重要性：[Set-of-Mark Prompting](https://arxiv.org/abs/2310.11441)。

以下是它的执行方式：

1.  它首先移除可能会混淆我们AI的旧数字标签（html属性`gpt-link-text`）。

1.  然后，它会用红色轮廓突出显示它发现的每个可点击的内容，帮助AI识别应该“点击”哪里。

1.  每个交互元素都会有一个独特的标签。这个标签/属性将用于标识Puppeteer稍后可以与之互动的元素。

需要记住的一个关键细节是，当处理Puppeteer或任何其他以编程方式与网页交互的测试框架时，带有链接文本的元素可能不可见。这里是一个简单的例子：

```py
<div style="display: none">
  <a href="https://www.example.com">
    <span>Click me</span>
  </a>
</div>
```

父div被隐藏，所以链接不可见。这个元素应该被排除。需要递归检查父元素以确保该元素可见。请参见下图中的逻辑：

![](../Images/171f42e9c803a67604cb42a80db2a9c2.png)

`isElementVisible`函数的流程图。

`highlightInteractiveElements`函数的代码实现

```py
import { Page } from "puppeteer";

const INTERACTIVE_ELEMENTS = [
  "a",
  "button",
  "input",
  "textarea",
  "[role=button]",
  "[role=treeitem]",
  '[onclick]:not([onclick=""])',
];
/**
 * Reset the unique identifier attribute and remove previously highlighted elements
 * @param page
 */
const resetUniqueIdentifierAttribute = async (page: Page): Promise<void> => {
  await page.evaluate(() => {
    const UNIQUE_IDENTIFIER_ATTRIBUTE = "gpt-link-text";
    const elements = document.querySelectorAll(
      `[${UNIQUE_IDENTIFIER_ATTRIBUTE}]`
    );
    for (const element of elements) {
      element.removeAttribute(UNIQUE_IDENTIFIER_ATTRIBUTE);
    }
  });
};
/**
 * This function annotates all the interactive elements on the page
 * @param page
 */
const annotateAllInteractiveElements = async (page: Page) => {
  // $$eval method runs Array.from(document.querySelectorAll(selector)) within the `page`and passes the result as the first argument to the pageFunction.
  // If no elements match the selector, the first argument to the pageFunction is [].
  await page.$$eval(
    INTERACTIVE_ELEMENTS.join(", "), // the selector can be defined outside the browser context
    // the argument `elements` can be an empty array if no elements match the selector
    function (elements) {
      // any console.log will not be visible in the node terminal
      // instead, it will be visible in the browser console
      // handle empty array
      if (elements.length === 0) {
        throw new Error("No elements found");
      }
      //======================================VALIDATE ELEMENT CAN INTERACT=================================================
      // This run-time check must be defined inside the pageFunction as it is running in the browser context. If defined outside, it will throw an error: "ReferenceError: isHTMLElement is not defined"
      const isHTMLElement = (element: Element): element is HTMLElement => {
        // this assertion is to allow Element to be treated as HTMLElement and has `style` property
        return element instanceof HTMLElement;
      };
      const isElementStyleVisible = (element: Element) => {
        const style = window.getComputedStyle(element);
        return (
          style.display !== "none" &&
          style.visibility !== "hidden" &&
          style.opacity !== "0" &&
          style.width !== "0px" &&
          style.height !== "0px"
        );
      };
      const isElementVisible = (element: Element | undefined | null) => {
        if (element === null || element === undefined) {
          throw new Error("isElementVisible: Element is null or undefined");
        }
        let currentElement: Element | null = element;
        while (currentElement) {
          if (!isElementStyleVisible(currentElement)) {
            return false;
          }
          currentElement = currentElement.parentElement;
        }
        return true;
      };
      //========================================PREPARE UNIQUE IDENTIFIER================================================
      const setUniqueIdentifierBasedOnTextContent = (element: Element) => {
        const UNIQUE_IDENTIFIER_ATTRIBUTE = "gpt-link-text";
        const { textContent, tagName } = element;
        // if the node is a document or doctype, textContent will be null
        if (textContent === null) {
          return;
        }
        element.setAttribute(
          UNIQUE_IDENTIFIER_ATTRIBUTE,
          textContent.trim().toLowerCase()
        );
      };
      //========================================HIGHLIGHT INTERACTIVE ELEMENTS================================================
      for (const element of elements) {
        if (isHTMLElement(element)) {
          // highlight all the interactive elements with a red bonding box
          element.style.outline = "2px solid red";
        }
        // assign a unique identifier to the element
        if (isElementVisible(element)) {
          // set a unique identifier attribute to the element
          // this attribute will be used to identify the element that puppeteer should interact with
          setUniqueIdentifierBasedOnTextContent(element);
        }
      }
    }
  );
};
/**
 * This function highlights all the interactive elements on the page
 * @param page
 */
export const highlightInteractiveElements = async (page: Page) => {
  await resetUniqueIdentifierAttribute(page);
  await annotateAllInteractiveElements(page);
};
```

# 结论

在本文中，我们探讨了AI代理的架构、每个步骤的代码实现以及设计背后的某些概念，如Set-of-Mark Prompting。该代理是一个优雅的系统，需要精心协调不同的服务才能有效运行，目前仍存在许多问题和局限性。如果您有任何问题或建议，请随时与我联系。我很愿意进一步讨论这个话题。

# 喜欢这个故事吗？

**Jason Li**（[Tianyi Li](https://medium.com/u/4092d7367010)，[LinkedIn](https://www.linkedin.com/in/tianyi-li-jason/)）是位于澳大利亚墨尔本的 [Mindset Health](https://www.mindsethealth.com/) 的全栈开发工程师。Jason 对人工智能、前端开发和与空间相关的技术充满热情。

**Selina Li**（[Selina Li](https://medium.com/u/7b9ea39b0d79)，[LinkedIn](https://www.linkedin.com/in/selina-zhuohang-li-3b7355120/)）是位于澳大利亚墨尔本的 [Officeworks](https://www.officeworks.com.au/) 的首席数据工程师。Selina 对人工智能/机器学习、数据工程和投资充满热情。

Jason 和 Selina 希望探索技术，帮助人们实现他们的目标。

*除非另有说明，所有图片均由作者提供。*
