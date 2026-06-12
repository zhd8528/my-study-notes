# Agent开发日志

## Hello-agents

### 1. 初识智能体

#### 1.1 什么是智能体

##### 1.1.1 传统视角下的智能体

**智能体（Agent）**：

> 四个基本要素：
>
> 通过**传感器（Sensors）**感知其所处**环境（Environment）**，并自主地通过**执行器（Actuators）**采取**行动（Action）**以达成特定目标的实体
>
> 智能体并非与环境隔离，通过其传感器持续地感知环境状态。摄像头、麦克风、雷达或各类**应用程序编程接口（Application Programming Interface，API）**返回的数据流，都是其感知能力的延伸
>
> 真正赋予智能体“智能”的是其**自主性（Autonomy）**

**反射智能体（Simple Reflex Agent）**：

> 结构最简单，完全依赖于当前的感知输入，不具备记忆或预测能力，因此无法应对需要理解上下文的复杂任务
>
> 它的局限性引出问题：
>
> 如果环境的当前状态不足以作为决策的全部依据，智能体该怎么办？

**基于模型的反射智能体（Model-Based Reflex Agent）**：

> 引入了“状态”的概念，这类智能体拥有一个内部的**世界模型（Workd Model）**，用于追踪和理解环境中那些无法被直接感知的方面
>
> 这个内部模型让智能体拥有了初级的“记忆”，使其决策不再仅仅依赖于瞬时感知，而是基于一个更连贯、更完整的世界状态理解

**基于目标的智能体（Goal-Based Agent）**：

> 仅仅理解世界还不够，智能体需要有明确的目标
>
> 与前两者不同，它的行为不再是被动地对环境做出反应，而是主动地、有预见地选择能够导向某个特定未来状态的行动
>
> 这类智能体需要回答的问题是：”我应该做什么才能达成目标？“
>
> 核心能力体现在了对未来的考量与规划上

**基于效用的智能体（Utility-Based Agent）**：

> 现实世界的目标往往不是单一的，当多个目标需要权衡时，它为每一个可能的世界状态都赋予一个效用值，这个值代表了满意度的高低
>
> 核心目标不再是简单地达成某个特定状态，而是最大化期望效用
>
> 它需要回答一个更复杂的问题：“哪种行为能为我带来最满意的结果？”
>
> 这种架构让智能体学会在相互冲突的目标之间进行权衡，使其决策更接近人类的理性选择

**学习型智能体（Learning Agent）**：

> 以上便是的核心思想，而**强化学习（Reinforcement Learning，RL）**是实现这一思想最具代表性的路径
>
> 一个学习型智能体包含一个性能元件（前面的各类智能体）和一个学习元件
>
> 学习元件通过观察性能元件在环境中的行动锁带来的结果来不断修正性能元件的决策策略

---

##### 1.1.2 大语言模型（Large Language Model，LLM）驱动的新范式

> 以 GPT（Generative Pre-trained Transformer）为代表的大语言模型的出现，正在显著改变智能体的构建方法与能力边界

**传统智能体与 LLM 驱动智能体的核心对比**：

| 对比维度    | 传统智能体                       | LLM 驱动的智能体                   |
| ----------- | -------------------------------- | ---------------------------------- |
| 核心引擎    | 基于显式编程的逻辑系统           | 基于预训练模型的推理引擎           |
| 知识来源    | 工程师预定义的规则、算法、知识库 | 从海量非结构化数据中间接学习、内化 |
| 处理指令    | 需结构化、精确的命令             | 可理解高层级、模糊的自然语言       |
| 工作模式    | 确定性的、可预测的               | 概率性的、生成式的                 |
| 泛化/适应性 | 弱，局限于预设框架               | 强，具备强大的涌现能力和泛化能力   |
| 开发范式    | 规则设计、算法编程、知识工程     | 模型训练、提示工程、微调           |

> 这种差异使得 LLM 智能体可以直接处理高层级、模糊且上下文信息的自然语言指令
>
> 它的工作方式体现了以下几点：
>
> - 规划与推理
> - 工具使用
> - 动态修正
>
> 总而言之，我们正从开发专用自动化工具转向构建能自主解决问题的系统
>
> 核心不再是编写代码，而是引导一个通用的“大脑”去规划、行动和学习

---

##### 1.1.3 智能体的类型

**基于内部决策架构的分类**

> 第一种分类维度是依据智能体内部决策架构的复杂程度
>
> 它涵盖了从简单的**反应式**智能体，到引入内部模型的**模型式**智能体，再到更具前瞻性的**基于目标**和**基于效用**的智能体
>
> 此外，**学习能力**则是一种可赋予上述所有类型的元能力，使其能通过经验自我改进

**基于时间与反应性的分类**

> 还可以从智能体处理决策的时间维度进行分类
>
> 这揭示了智能体设计中一个核心权衡：追求速度的**反应性（Reactivity）**与追求最优解的**规划性（Deliberation）**之间的平衡
>
> **反应式智能体**：
>
> 对环境刺激做出近乎即时的响应，决策延迟极低
>
> 通常遵循从感知到行动的直接映射，不进行或只进行极少的未来规划
>
> 上文的**简单反应式**和**基于模型**的只能用都属于此类别
>
> 其核心优势在于**速度快、计算开销低**，这在需要快速决策的动态环境中至关重要
>
> 然而，这种速度的代价是“短视”，由于缺乏长远规划，反应式智能体容易陷入局部最优，难以完成需要多步骤协调的复杂任务
>
> **规划式智能体**：
>
> 与反应式智能体相对，规划式（或审议式）智能体在行动前会进行复杂的思考和规划
>
> 它们不会立即对感知做出反应，而是会利用其内部的世界模型，系统地探索未来的各种可能性，评估不同行动序列的后果，以期找到一条能够达成目标的最佳路径
>
> **基于目标**和**基于效用**的智能体是典型的规划式智能体
>
> **混合式智能体**：
>
> 现实世界的复杂任务，往往需要即时反应，也需要长远规划
>
> 混合式智能体旨在结合两者的优点，实现反应与规划的平衡
>
> 一种经典的混合架构是分层设计：底层是一个快速的反应模块，处理紧急情况和基本动作；
>
> 高层则是一个审慎的规划模块，负责制定长远目标
>
> 而现代的 LLM 智能体，则展示了一种更灵活的混合模式
>
> 它们通常在一个“思考-行动-观察”的循环中运作，巧妙地将两种模式融为一体：
>
> - **规划（Reasoning）**：在“思考”阶段，LLM 分析当前状况，规划出下一步的合理行动
>   - 这是一个审议过程
> - **反应（Acting & Observing）**：在“行动”和“观察”阶段，智能体与外部工具或环境交互，并立即获得反馈
>   - 这是一个反应过程
>
> 通过这种方式，智能体将一个需要长远规划的宏大任务，分解为一系列“规划-反应”的微循环
>
> 这使其既能灵活应对环境的即时变化，又能通过连贯的步骤，最终完成复杂的长期目标

**基于知识表示的分类**：

> 这是一个更根本的分类维度，它探究智能体用以决策的知识，究竟是以何种形式存于其“思想”之中
>
> - **符号主义 AI（Symbolic AI）**：常被称为传统人工智能，其核心信念是：智能源于对符号的逻辑操作
>   - 这里的符号是人类可读的实体（如词语、概念），操作则遵循严格的逻辑规则
>   - 其主要优势在于透明和可解释
>     - 由于推理步骤明确，其决策过程可以被完整追溯，这在金融、医疗等高风险领域至关重要
>   - 然而，脆弱性在于：它依赖于一个完备的规则体系，但在充满模糊和例外的现实世界中，任何未被覆盖的新情况都可能导致系统失灵，这就是所谓的“知识获取瓶颈”
> - **亚符号主义 AI（Sub-symbolic AI）**：或称连接主义，在这里，知识并非显式的规则，而是内隐地分布在一个由大量神经元组成的复杂网络中，是次海量数据中学习到的统计模式
>   - 神经网络和深度学习是其代表
>   - 这种方法的强的之处在于其模式识别能力和对噪声数据的鲁棒性
>     - 它能够轻松处理图像、声音等非结构化数据，这在符合主义 AI 看来是极其困难的任务
>   - 然而，这种强大的直觉能力也伴随着不透明性
>     - 亚符号主义系统通常被视为一个**黑箱（Black Box）**
>       - 它能以惊人的准确率识别出图片中的物体，但若问它理由，它很可能无法给出一个合乎逻辑的解释
>       - 此外，它在纯粹的逻辑推理任务上表现不佳，有时会产生看似合理却事实错误的幻觉
> - **神经符号主义 AI（Neuro-Symbolic AI）**：也称神经符号混合主义，它的目标是融合两大范式的优点，创造一个既能像神经网络一样从数据中学习，又能像符合系统一样进行逻辑推理的混合智能体
>   - 它试图弥合感知与认知、直觉与理性之间的鸿沟
>     - **系统1**是缓慢、有条理、基于逻辑的审慎思维，恰如符号主义 AI 的推理过程
>     - **系统2**是快速、凭直觉、并行的思维模式，类似于亚符号主义 AI 强大的模式识别能力
>   - 人类的智能正源于这两个系统的协同工作，同样，一个真正鲁棒的 AI 也需要兼具二者之长
>     - 大语言模型驱动的智能体是神经符号主义的一个极佳实践范例。其内核是一个巨大的神经网络，使其具备模式识别和语言生成能力
>     - 然而，当它工作时，它会产生一系列结构化的中间步骤，如思想、计划或 API 调用，这些都是明确的、可操作的符号
>     - 通过这种方式，它实现了感知与认知、直觉与理性的初步融合

---

#### 1.2 智能体的构成与运行原理

##### 1.2.1 任务环境定义

> 要理解智能体的运作，我们必须先理解它所处的**任务环境**
>
> 在人工智能领域，通常使用 **PEAS 模型**来精确描述一个任务环境，即分析其**性能度量（Performance）、环境（Environment）、执行器（Actuators）和传感器（Sensors）**

**智能旅行助手的 PEAS 描述**（展示了如何运用 PEAS 模型对其任务环境进行规约）：

| 维度                    | 描述                                                         |
| ----------------------- | ------------------------------------------------------------ |
| Performance（性能度量） | 在预算和时间内，最大化用户满意度与行程合理性                 |
| Environment（环境）     | 航旅预订网站、地图服务、天气预报 API 等网络服务              |
| Actuators（执行器）     | 调用 API 的函数、向用户界面生成和显示格式化文本              |
| Sensors（传感器）       | 解析 API 返回的数据（如 JSON，HTML）、读取用户输入的自然语言 |

> 在实践中，LLM 智能体所处的数字环境展现出若干复杂特性，这些特性直接影响着智能体的设计
>
> 首先，环境通常是**部分可观察的**
>
> 旅行助手智能通过调用航班预订 API，看到该 API 返回的部分数据，这就要求智能体必须具备记忆（记住已查询过的航线）和探索（尝试不同的查询日期）的能力
>
> 其次，行动的结果也并非总是确定的。根据结果的可预测性，环境可分为**确定性**和**随机性**
>
> 当旅行助手搜索票价时，两次相邻的调用返回的机票价格和余票数量都可能不同，这就要求智能体必须具备处理不确定性、监控变化并及时决策的能力
>
> 此外，环境中还可能存在其他行动者，从而形成**多智能体（Multi-agent）**环境
>
> 它们的行动（例如，订走最后一张特价票）会直接改变旅行助手所处环境的状态，这对智能体的快速响应和策略选择提出了更高要求
>
> 最后，几乎所有任务都发生在**序贯**且**动态**的环境中
>
> “序贯”意味着当前动作会影响未来；
>
> 而“动态”则意味着环境自身可能在智能体决策时发生变化
>
> 这就要求智能体的“感知-思考-行动-观察”循环必须能够快速、灵活地适应持续变化的世界

---

##### 1.2.2 智能体的运行机制

> 智能体并发一次性完成任务，而是通过一个持续的循环与环境进行交互，这个核心机制被称为**智能体循环（Agent Loop）**
>
> 该循环描述了智能体与环境之间的动态交换过程，构成了其自主行为的基础
>
> 这个循环主要包含以下几个相互关联的阶段：
>
> - **感知（Perception）**：这是循环的起点
>   - 智能体通过其传感器（例如，API 的监听端口、用户输入接口）接收来自环境的输入信息
>   - 这些信息，即**观察（Observation）**，既可以是用户的初始指令，也可以是上一步行动所导致的环境状态变化反馈
> - **思考（Thought）**：接收到观察信息后，智能体进入其核心决策阶段
>   - 对于 LLM 智能体而言，这通常是由大语言模型驱动的内部推理过程
>   - 思考阶段可进一步细分为两个关键环节：
>     - **规划（Planning）**：智能体基于当前的观察和其内部记忆，更新对任务和环境的理解，并制定或调整一个行动计划。这可能涉及将复杂目标分解为一系列更具体的子任务
>     - **工具选择（Tool Selection）**：根据当前计划，智能体从其可用的工具库中，选择最适合执行下一步骤的工具，并确定调用该工具所需的具体参数
> - **行动（Action）**：决策完成后，智能体通过其执行器（Actuators）执行具体的行动
>   - 这通常表现为调用一个选定的工具（如代码解释器、搜索引擎 API），从而对环境施加影响，意图改变环境的状态
>
> 行动并非循环的终点
>
> 智能体的行动会引起**环境（Environment）**的**状态变化（State Change）**，环境随即会产生一个新的**观察（Observation）**作为结果反馈
>
> 这个新的观察又会在下一轮循环中被智能体的感知系统捕获，形成一个持续的“感知-思考-行动-观察”的闭环
>
> 智能体正是通过不断重复这一循环，逐步推进任务，从初始状态向目标状态演进

---

##### 1.2.3 智能体的感知与行动

> 在工程实践中，为了让 LLM 能够有效驱动这个循环，我们需要一套明确的**交互协议（Interaction Protocol）**来规范其与环境之间的信息交换
>
> 在许多现代智能体框架中，这一协议体现在对智能体每一次输出的结构化定义上
>
> 智能体的输出不再是单一的自然语言回复，而是一段遵循特定格式的文本，其中明确地展示了其内部的推理过程与最终决策
>
> 这个结构通常包含两个核心部分：
>
> - **Thought（思考）**：这是智能体内部决策的“快照”
>   - 它以自然语言形式阐述了智能体如何分析当前情境、回顾上一步的观察结果、进行自我反思与问题分解，并最终规划出下一步的具体行动
> - **Action（行动）**：这是智能体基于思考后，决定对环境施加的具体操作，通常以函数调用的形式表示

**例如，一个正在规划旅行的智能体可能会生成如下格式的输出**：

```bash
Thought：用户想知道北京的天气。我需要调用天气查询工具
Action：get_weather("北京")
```

> 这里的`Action`字段构成了对外部世界的指令
>
> 一个外部的**解析器（Parser）**会捕捉到这个指令，并调用相应的`get_weather`函数
>
> 行动执行后，环境会返回一个结果
>
> 例如，`get_weather`函数可能会返回一个包含详细天气数据的 JSON 对象
>
> 然而，原始的机器可读数据（如 JSON）通常包含 LLM 无需关注的冗余信息，且格式不符合其自然语言处理的习惯

**因此，感知系统的一个重要职责就是扮演传感器的角色**（将这个原始输出处理并封装成一段简洁、清晰的自然语言文本，即观察）：

```bash
Observation：北京当前天气为晴，气温25摄氏度，微风。
```

> 这段`Observation`文本会被反馈给智能体，作为下一轮循环的主要输入信息，供其进行新一轮的`Thought`和`Action`
>
> 综上所述，通过这个由 Thought、Action、Observation 构成的演进循环，LLM 智能体得以将内部的语言推理能力，与外部环境的真实信息和工具操作能力有效结合起来

---

#### 1.3 动手体验：5 分钟实现第一个智能体

> 我们的目标是构建一个能处理分步任务的智能旅行助手
>
> 需要解决的用户任务定义为：“你好，请帮我查询一下今天北京的天气，然后根据天气推荐一个合适的旅游景点。”
>
> 要完成这个任务，智能体必须展现出清晰的逻辑规划能力
>
> 它需要先调用天气查询工具，并将获得的观察结果作为下一步的依据
>
> 在下一轮循环中，它再调用景点推荐工具，从而得出最终建议

##### 1.3.1 准备工作

> 为了能从 Python 程序中访问网络 API，我们需要一个 HTTP 库
>
> `requests`是 Python 社区中最流行、最易用的选择
>
> `tavily-python`是一个强大的 AI 搜索 API 客户端，用于获取实时的网络搜索结果，可以在官网注册后获取 API
>
> `openai`是 OpenAI 官方提供的 Python SDK，用于调用 GPT 等大语言模型服务

```bash
pip install requests tavily-python openai
```

**（1）指令模版**：

> 驱动真实 LLM 的关键在于**提示工程（Prompt Engineering）**
>
> 我们需要设计一个“指令模版”，告诉 LLM 它应该扮演什么角色、拥有哪些工具、以及如何格式化它的思考和行动
>
> 这是我们智能体的“说明书”，它将作为`system_prompt`传递给 LLM

```bash
AGENT_SYSTEM_PROMPT = """
你是一个智能旅行助手。你的任务是分析用户的请求，并使用可用工具一步步地解决问题

# 可用工具：
- `get_weather(city: str)`：查询指定城市的实时天气。
- `get_attraction(city: str, weather: str)`：根据城市和天气搜索推荐的旅游景点。

# 输出格式要求：
你的每次回复必须严格遵循以下格式，包含一对Thought和Action：

Thought：[你的思考过程和下一步计划]
Action：[你要执行的具体行动]

Action的格式必须是以下之一：
1. 调用工具：function_name(arg_name="arg_value")
2. 结束任务：Finish[最终答案]

# 重要提示：
- 每次只输出一对Thought-Action
- Action必须在同一行，不要换行
- 当收集到足够信息可以回答用户问题时，必须使用 Action: Finish[最终答案] 格式结束

请开始吧！
"""
```

> 上面的提示词经实际测试发现无法完成任务，用 AI 改了一版

```py
AGENT_SYSTEM_PROMPT = """
你是一个智能旅行助手。你的任务是分析用户的请求，并使用可用工具一步步地解决问题。

# 可用工具：
- `get_weather(city: str)`：查询指定城市的实时天气。
- `get_attraction(city: str, weather: str)`：根据城市和天气搜索推荐的旅游景点。

# 任务执行流程（**必须严格按顺序执行**）：
1.  第一步：调用 `get_weather` 获取目标城市的天气信息。
2.  第二步：拿到天气结果后，调用 `get_attraction`，传入城市和天气，获取景点推荐。
3.  第三步：拿到景点推荐后，**必须立刻使用 `Finish` 格式给出最终答案，禁止再调用任何工具**。

# 输出格式要求：
你的每次回复必须严格遵循以下格式，包含一对Thought和Action：
Thought: [你的思考过程和下一步计划，必须清晰说明你当前在执行流程的哪一步]
Action: [你要执行的具体行动，只能是工具调用或Finish]

# Action的格式必须是以下之一：
1.  调用工具：`function_name(arg_name="arg_value")`，例如：
    Action: get_weather(city="北京")
    Action: get_attraction(city="北京", weather="晴朗")
2.  结束任务：`Finish[最终答案]`，最终答案必须包含：天气情况 + 推荐景点。例如：
    Action: Finish[北京今天天气晴朗，气温20度，推荐你去故宫游玩]

# 重要提示：
- 每次只输出一对Thought-Action，不要输出多余内容。
- Action必须在同一行，不要换行。
- 当你已经拿到天气和景点推荐时，必须使用 `Action: Finish[最终答案]` 格式结束任务，禁止继续调用工具。
- 禁止在Action中写自然语言，必须严格使用上面定义的格式。

请开始吧！
"""
```

**（2）工具 1：查询真实天气**

> 我们将使用免费的天气查询服务`wttr.in`，它能以 JSON 格式返回指定城市的天气数据

**下面是实现该工具的代码**：

```python
import requests
def get_weather(city: str) -> str:
    """
    通过调用 wttr.in API 查询真实的天气信息
    """
    # API端点，我们请求JSON格式的数据
    url = f"https://wttr.in/{city}?format=j1"
    
    try:
        # 发起网络请求
        response = requests.get(url)
        # 检查响应状态码是否为200（成功）
        response.raise_for_status()
        # 解析返回的JSON数据
        data = response.json()
        
        # 提取当前天气状况
        current_condition = data['current_condition'][0]
        weather_desc = current_condition['weatherDesc'][0]['value']
        temp_c = current_condition['temp_C']
        
        # 格式化成自然语言返回
        return f"{city}当前天气：{weather_desc}，气温{temp_c}摄氏度"
    
except requests.exceptions.RequestException as e:
    # 处理网络错误
    return f"错误：查询天气时遇到网络问题 - {e}"
except (KeyError, IndexError) as e:
    # 处理数据解析错误
    return f"错误：解析天气数据解析失败，可能是城市名称无效 - {e}"
```

**（3）工具 2：搜索并推荐旅游景点**

> 我们将定义一个新工具`search_attraction`，它会根据城市和天气状况，互联网上搜索合适的景点：

```python
import os
from tavily import TavilyClient

def get_attraction(city: str, weather: str) -> str:
    """
    根据城市和天气，使用Tavily Search API搜索并返回优化后的景点推荐。
    """
    # 1. 从环境变量中读取API密钥
    api_key = os.environ.get("TAVILY_API_KEY")
    if not api_key:
        return "错误：未配置TAVILY_API_KEY环境变量。"
    
    # 2. 初始化Tavily客户端
    tavily = TavilyClient(api_key=api_key)
    
    # 3. 构造一个精确的查询
    query = f"'{city}' 在'{weather}'天气下最值得去的旅游景点推荐及理由"
    
    try:
        # 4. 调用API，include_answer=True会返回一个综合性的回答
        reponse = tavily.search(query=query, search_depth = "basic", include_answer=True)
        
        # 5. Tavily返回的结果已经非常干净，可以直接使用
        # response['answer'] 是一个基于所有搜索结果的总结性回答
        if response.get("answer"):
            return response["answer"]
        
        # 如果没有综合性回答，则格式化原始结果
        formatted_result = []
        for result in response.get("results", []):
            formatted_results.append(f"- {result['title']}: {result['content']}")
        if not formatted_results:
            return "抱歉，没有找到相关的旅游景点推荐。"
        
        return "根据搜索，为您找到以下信息：\n" + "\n".join(formatted_results)
    
except Exception as e:
    return f"错误：执行Tavily搜索时出现问题 - {e}"
```

**最后，我们将所有工具函数放入一个字典，供主循环调用**：

```python
# 将所有工具函数放入一个字典，方便后续调用
available_tools = {
    "get_weather": get_weather,
    "get_attraction": get_attraction,
}
```

---

##### 1.3.2 接入大语言模型

> 当前，许多 LLM 服务提供商（包括 OpenAI、Azure、以及众多开源模型服务框架如 Ollama、vLLM 等）都遵循了与 OpenAI API 相似的接口规范
>
> 这种标准化为开发者带来了极大的便利
>
> 智能体的自主决策能力来源于 LLM
>
> 我们将实现一个通用的客户端`OpenAICompatibleClient`，它可以连接到任何兼容 OpenAI 接口规范的 LLM 服务

```python
from openai import OpenAI

class OpenAICompatibleClient:
    """
    一个用于调用任何兼容OpenAI接口的LLM服务的客户端。
    """
    def _init_(self, model: str, api_key: str, base_url: str):
        self.model = model
        self.client = OpenAI(api_key=api_key, base_url=base_url)
        
	def generate(self, prompt: str, system_prompt: str) -> str:
        """调用LLM API来生成回应。"""
        print("正在调用大语言模型...")
        try:
            messages = [
                {'role': 'system', 'content': system_prompt},
                {'role': 'user', 'content': prompt}
            ]
            response = self.client.chat.completions.create(
                model=self.model,
                messages=messages,
                stream=False
            )
            answer = response.choices[0].message.content
            print("大语言模型响应成功。")
            return answer
        except Exception as e:
            print(f"调用LLM API时发生错误： {e}")
            return "错误：调用语言模型服务时出错。"
```

> 要实例化此类，您需要提供三个信息：`API_KEY`、`BASE_URL`和`MODEL_ID`，具体值取决于您使用的服务商（如 OpenAI 官方、Azure、或 Ollama 等本地模型）

---

**1.3.3 执行行动循环**

> 下面的主循环将整合所有组件，并通过格式化后的 Prompt 驱动 LLM 进行决策。

```python
import re

# --- 1. 配置LLM客户端 ---
# 请根据您使用的服务，将这里替换成对应的凭证和地址
API_KEY = "YOUR_API_KEY"
BASE_URL = "YOUR_BASE_URL"
MODEL_ID = "YOUR_MODEL_ID"
TAVILY_API_KEY="YOUR_TAVILY_KEY"
os.environ['TAVILY_API_KEY'] = "YOUR_TAVILY_API_KEY"

llm = OpenAICompatibleClient(
    model=MODEL_ID,
    api_key=API_KEY,
    base_url=BASE_URL
)

# --- 2. 初始化 ---
user_prompt = "你好，请帮我查询一下今天北京的天气，然后根据天气推荐一个合适的旅游景点。"
prompt_history = [f"用户请求：{user_prompt}"]

print(f"用户输入：{user_prompt}\n" + "="*40)

# --- 3. 运行主循环 ---
for i in range(S): # 设置最大循环次数
    print(f"--- 循环 {i+1} ---\n")
    
    # 3.1. 构建Prompt
    full_prompt = "\n".join(prompt_history)
    
    # 3.2. 调用LLM进行思考
    llm_output = llm.generate(full_prompt, system_prompt=AGENT_SYSTEM_PROMPT)
    # 模型可能会输出多余的Thought-Action，需要截断
    match = re.search(r'(Thought:.*?Action:.*?)(?=\n\s*(?:Thought:|Action:|Observation:)|\Z)', llm_output, re.DOTALL)
    if match:
        truncated = match.group(1).strip()
        if truncated != llm_output.strip():
            llm_output = truncated
            print("已截断多余的 Thought-Action 对")
	print(f"模型输出：\n{llm_output}\n")            
    prompt_history.append(llm_output)
    
    # 3.3. 解析并执行行动
    action_match = re.search(r"Action: (.*)", llm_output, re.DOTALL)
    if not action_match:
        observation = "错误：未能解析到 Action 字段。请确保你的恢复严格遵循  'Thought: ... Action: ...' 的格式。"
        observation_str = f"Observation:{observation}"
        print(f"{observation_str}\n" + "="*40)
        prompt_history.append(observation_str)
        continue
	action_str = action_match.group(1).strip()
    
    if action_str.startswith("Finish"):
        final_answer = re.match(r"Finish\[(.*)\]", action_str).group(1)
        print(f"任务完成，最终答案：{final_answer}")
        break
        
	tool_name = re.search(r"(\w+)\(", action_str).group(1)
    args_str = re.search(r"\((.*)\)", action_str).group(1)
    kwargs = dict(re.findall(r'(\w+)="([^"]*)"', args_str))
    
    if tool_name in available_tools:
        observation = available_tools[tool_name](**kwargs)
    else:
        observation = f"错误：未定义的工具 '{tool_name}'"
        
	# 3.4. 记录观察结果
    observation_str = f"Observation: {observation}"
    print(f"{observation_str}\n" + "="*40)
    prompt_history.append(observation_str)
```

> 通过以上步骤，我们构建了一个完整的、由真实 LLM 驱动的智能体
>
> 其核心在于”工具“和”提示工程“的结合，这正是当前主流智能体框架（如 LangChain、LlamaIndex 等）的设计精髓

---

##### 1.3.4 运行案例分析