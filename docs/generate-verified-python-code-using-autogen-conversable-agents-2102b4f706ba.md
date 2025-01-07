# 使用AutoGen可交互代理生成“验证过的”Python代码

> 原文：[https://towardsdatascience.com/generate-verified-python-code-using-autogen-conversable-agents-2102b4f706ba?source=collection_archive---------9-----------------------#2024-04-09](https://towardsdatascience.com/generate-verified-python-code-using-autogen-conversable-agents-2102b4f706ba?source=collection_archive---------9-----------------------#2024-04-09)

## 利用多代理工作流进行代码测试和调试

[](https://medium.com/@shahzebnaveed?source=post_page---byline--2102b4f706ba--------------------------------)[![Shahzeb Naveed](../Images/cdf5a3f205eac63306d1f8384fa634ab.png)](https://medium.com/@shahzebnaveed?source=post_page---byline--2102b4f706ba--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2102b4f706ba--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2102b4f706ba--------------------------------) [Shahzeb Naveed](https://medium.com/@shahzebnaveed?source=post_page---byline--2102b4f706ba--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2102b4f706ba--------------------------------) ·阅读时长：8分钟·2024年4月9日

--

![](../Images/743dc56e1a6501a8f27b1ed2ffcf407f.png)

“两个AI机器人解决一个错误” — 来源：Adobe Firefly（图片由作者生成）

现在是2024年4月，自从我们开始使用像ChatGPT这样的LLM来辅助代码生成和调试任务，已经过去了大约17个月。虽然这极大提高了生产力，但确实有时候生成的代码充满了错误，让我们不得不走老路——StackOverflow。

在本文中，我将简要演示如何使用AutoGen提供的可交互代理来解决缺乏“验证”的问题。

完整的AutoGen课程：[https://www.youtube.com/playlist?list=PLlHeJrpDA0jXy_zgfzt2aUvQu3_VS5Yx_](https://www.youtube.com/playlist?list=PLlHeJrpDA0jXy_zgfzt2aUvQu3_VS5Yx_)

**什么是AutoGen？**

> “AutoGen是一个框架，能够使用多个能够相互对话的代理来开发LLM应用，以解决任务。”

**展示LeetCode问题求解器：**

从静默安装autogen开始：

```py
!pip install pyautogen -q --progress-bar off
```

我正在使用Google Colab，因此我在Secrets标签页中输入了我的OPENAI_API_KEY，并与其他模块一起安全加载

```py
import os
import csv
import autogen
from autogen import Cache
from google.colab import userdata
userdata.get('OPENAI_API_KEY')
```

我只使用`gpt-3.5-turbo`，因为它比`gpt4`便宜。如果你能够承担更昂贵的实验，或者你做的是更“严肃”的事情，显然应该使用更强的模型。

```py
llm_config = {
    "config_list": [{"model": "gpt-3.5-turbo", "api_key": userdata.get('OPENAI_API_KEY')}],
    "cache_seed": 0,  # seed for reproducibility
    "temperature": 0,  # temperature to control randomness
}
```

现在，我将从我最喜欢的LeetCode问题[*Two Sum*](https://leetcode.com/problems/two-sum/description/)中复制问题陈述。它是Leetcode风格面试中最常见的题目之一，涵盖了诸如使用哈希映射进行缓存和基本方程式处理等基本概念。

```py
LEETCODE_QUESTION = """
Title: Two Sum

Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target. You may assume that each input would have exactly one solution, and you may not use the same element twice. You can return the answer in any order.

Example 1:
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].

Example 2:
Input: nums = [3,2,4], target = 6
Output: [1,2]

Example 3:
Input: nums = [3,3], target = 6
Output: [0,1]

Constraints:

2 <= nums.length <= 104
-109 <= nums[i] <= 109
-109 <= target <= 109
Only one valid answer exists.

Follow-up: Can you come up with an algorithm that is less than O(n2) time complexity?
"""
```

现在我们可以定义我们的两个代理。一个代理充当“助手”代理，提供解决方案，另一个代理充当我们（用户）的代理，并负责执行建议的 Python 代码。

```py
# create an AssistantAgent named "assistant"

SYSTEM_MESSAGE = """You are a helpful AI assistant.
Solve tasks using your coding and language skills.
In the following cases, suggest python code (in a python coding block) or shell script (in a sh coding block) for the user to execute.
1\. When you need to collect info, use the code to output the info you need, for example, browse or search the web, download/read a file, print the content of a webpage or a file, get the current date/time, check the operating system. After sufficient info is printed and the task is ready to be solved based on your language skill, you can solve the task by yourself.
2\. When you need to perform some task with code, use the code to perform the task and output the result. Finish the task smartly.
Solve the task step by step if you need to. If a plan is not provided, explain your plan first. Be clear which step uses code, and which step uses your language skill.
When using code, you must indicate the script type in the code block. The user cannot provide any other feedback or perform any other action beyond executing the code you suggest. The user can't modify your code. So do not suggest incomplete code which requires users to modify. Don't use a code block if it's not intended to be executed by the user.
If you want the user to save the code in a file before executing it, put # filename: <filename> inside the code block as the first line. Don't include multiple code blocks in one response. Do not ask users to copy and paste the result. Instead, use 'print' function for the output when relevant. Check the execution result returned by the user.
If the result indicates there is an error, fix the error and output the code again. Suggest the full code instead of partial code or code changes. If the error can't be fixed or if the task is not solved even after the code is executed successfully, analyze the problem, revisit your assumption, collect additional info you need, and think of a different approach to try.
When you find an answer, verify the answer carefully. Include verifiable evidence in your response if possible.

Additional requirements:
1\. Within the code, add functionality to measure the total run-time of the algorithm in python function using "time" library.
2\. Only when the user proxy agent confirms that the Python script ran successfully and the total run-time (printed on stdout console) is less than 50 ms, only then return a concluding message with the word "TERMINATE". Otherwise, repeat the above process with a more optimal solution if it exists.
"""

assistant = autogen.AssistantAgent(
    name="assistant",
    llm_config=llm_config,
    system_message=SYSTEM_MESSAGE
)

# create a UserProxyAgent instance named "user_proxy"
user_proxy = autogen.UserProxyAgent(
    name="user_proxy",
    human_input_mode="NEVER",
    max_consecutive_auto_reply=4,
    is_termination_msg=lambda x: x.get("content", "").rstrip().endswith("TERMINATE"),
    code_execution_config={
        "work_dir": "coding",
        "use_docker": False,
    },
)
```

我将 `human_input_mode` 设置为“NEVER”，因为我不打算自己提供任何输入，并将 `max_consecutive_auto_reply` 设置为 4，以限制对话中的来回轮次。助手代理已被指示以“TERMINATE”响应，告知用户代理何时结束对话。

现在，进入有趣的部分！我们将通过从用户代理发送消息给助手来启动对话。

使用 AutoGen 的一个额外好处（即使是非代理工作流）是它提供了显式的缓存功能，帮助你在开发过程中节省 API 成本。在这里，我将响应缓存到磁盘，但你也可以为此集成 redis。

```py
# Use DiskCache as cache
with Cache.disk(cache_seed=7) as cache:
  # the assistant receives a message from the user_proxy, which contains the task description
  chat_res = user_proxy.initiate_chat(
      assistant,
      message="""Solve the following leetcode problem and also comment on it's time and space complexity:\n\n""" + LEETCODE_QUESTION
)
```

**输出：**

```py
user_proxy (to assistant):

Solve the following leetcode problem and also comment on it's time and space complexity:

Title: Two Sum

...

Follow-up: Can you come up with an algorithm that is less than O(n2) time complexity?

--------------------------------------------------------------------------------
assistant (to user_proxy):

To solve this problem efficiently, we can use a dictionary to store the elements we have seen so far along with their indices. While iterating through the array, we can check if the complement of the current element (target - current element) is already in the dictionary. If it is, we have found the pair that sums up to the target.

Here's the Python code to solve the Two Sum problem with a time complexity less than O(n^2):

```python

# 文件名: two_sum.py

def two_sum(nums, target):

    num_dict = {}

    for i, num in enumerate(nums):

        complement = target - num

        如果 complement 在 num_dict 中：

            return [num_dict[complement], i]

        num_dict[num] = i

# 使用给定示例测试函数

nums1 = [2, 7, 11, 15]

target1 = 9

print(two_sum(nums1, target1))  # 输出: [0, 1]

nums2 = [3, 2, 4]

target2 = 6

print(two_sum(nums2, target2))  # 输出: [1, 2]

nums3 = [3, 3]

target3 = 6

print(two_sum(nums3, target3))  # 输出: [0, 1]

```py

The time complexity of this solution is O(n) because we iterate through the list of numbers only once. The space complexity is also O(n) because we store elements in a dictionary.

You can run this code and check if it provides the correct output. Let me know the total run-time, and if it's less than 50 ms, you can reply with "TERMINATE". If it's not, I can suggest a more optimal solution.

--------------------------------------------------------------------------------

>>>>>>>> EXECUTING CODE BLOCK 0 (inferred language is python)...
user_proxy (to assistant):

exitcode: 0 (execution succeeded)
Code output: 
[0, 1]
[1, 2]
[0, 1]

--------------------------------------------------------------------------------
assistant (to user_proxy):

The Python script ran successfully and provided the correct output for the given examples. Since the total run-time is not provided, I will suggest measuring the total run-time of the algorithm using the "time" library to determine if it's less than 50 ms. 

You can add the functionality to measure the total run-time by importing the "time" library and using it to calculate the elapsed time before and after calling the `two_sum` function. Here's how you can modify the code:

```python

# 文件名: two_sum.py

import time

def two_sum(nums, target):

    num_dict = {}

    for i, num in enumerate(nums):

        complement = target - num

        如果 complement 在 num_dict 中：

            return [num_dict[complement], i]

        num_dict[num] = i

# 使用给定示例测试函数并测量总运行时间

nums1 = [2, 7, 11, 15]

target1 = 9

start_time = time.time()

print(two_sum(nums1, target1))  # 输出: [0, 1]

end_time = time.time()

print("总运行时间:", (end_time - start_time) * 1000, "毫秒")

nums2 = [3, 2, 4]

target2 = 6

start_time = time.time()

print(two_sum(nums2, target2))  # 输出: [1, 2]

end_time = time.time()

print("总运行时间:", (end_time - start_time) * 1000, "毫秒")

nums3 = [3, 3]

target3 = 6

start_time = time.time()

print(two_sum(nums3, target3))  # 输出: [0, 1]

end_time = time.time()

print("总运行时间:", (end_time - start_time) * 1000, "毫秒")

```py

Please run this modified code and provide the total run-time for each example. If the total run-time is less than 50 ms for all examples, you can reply with "TERMINATE".

--------------------------------------------------------------------------------

>>>>>>>> EXECUTING CODE BLOCK 0 (inferred language is python)...
user_proxy (to assistant):

exitcode: 0 (execution succeeded)
Code output: 
[0, 1]
Total run-time: 0.01239776611328125 ms
[1, 2]
Total run-time: 0.00286102294921875 ms
[0, 1]
Total run-time: 0.0016689300537109375 ms

--------------------------------------------------------------------------------
assistant (to user_proxy):

The Python script ran successfully, and the total run-time for each example is significantly less than 50 ms. Therefore, you can reply with "TERMINATE". The algorithm provided an efficient solution with a time complexity of O(n) and a space complexity of O(n).

--------------------------------------------------------------------------------
```

这就是这里发生的事情：

1.  用户代理要求助手根据任务描述解决问题。

1.  助手建议了一个带有 Python 代码块的解决方案

1.  用户代理执行 Python 代码。

1.  助手读取控制台输出并返回修改后的解决方案（包含时间测量功能。老实说，我本来希望立即得到这个修改后的解决方案，但这种行为可以通过提示工程或采用更强的 LLM 来调整）。

使用 AutoGen，你还可以显示代理工作流的成本。

```py
chat_res.cost
```

```py
 ({'total_cost': 0,
  'gpt-3.5-turbo-0125': {'cost': 0,
   'prompt_tokens': 14578,
   'completion_tokens': 3460,
   'total_tokens': 18038}}
```

**总结：**

因此，通过使用 AutoGen 的可交互代理：

1.  我们自动验证了 LLM 提议的 Python 代码实际上是可行的。

1.  并创建了一个框架，通过该框架，LLM 可以通过读取控制台中的输出进一步响应语法或逻辑错误。

*感谢阅读！请关注我并订阅，以便我发布新文章时您可以第一时间看到！ :)*

*查看我的其他文章：*

1.  [*深入探讨 Azure Prompt Flow 中的评估*](https://medium.com/thedeephub/a-deep-dive-into-evaluation-in-azure-prompt-flow-dd898ebb158c)

1.  [*使用 Streamlit 为 Azure Prompt Flow 开发 UI*](https://medium.com/thedeephub/develop-a-ui-for-azure-prompt-flow-with-streamlit-f425342029ce)

1.  [*在 Azure Kubernetes 服务上使用 Hugging Face Chat UI 和 Cosmos DB 构建自定义聊天机器人*](https://medium.com/thedeephub/build-a-custom-chatbot-using-hugging-face-chat-ui-and-cosmos-db-on-azure-kubernetes-service-0e391c00cd78)

1.  [*在 Azure 容器实例上部署 Hugging Face 文本生成推理*](https://medium.com/thedeephub/deploy-hugging-face-text-generation-inference-on-azure-container-instance-3709eb3d3187)
