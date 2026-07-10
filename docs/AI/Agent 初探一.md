# Agent 初探（一）：原理、模式与 Claude Code 的差异

## 一、AI Agent 核心原理

### 1.1 什么是 AI Agent

AI Agent 是具备**感知、决策、行动**能力的智能系统。与传统 LLM 的一次性问答不同，Agent 能够：

- **使用工具**（Tool Use）：调用外部 API、查询数据库、执行代码
- **推理规划**（Reasoning & Planning）：分析任务并拆解执行步骤
- **与环境交互**（Environment Interaction）：根据反馈调整行动
- **记忆上下文**（Memory）：维护短期对话历史和长期知识

核心特征：**LLM 作为"大脑"，通过不断与环境（工具）交互，逐步逼近目标**。

---

### 1.2 ReAct：Agent 的基础行为模式

ReAct（Reasoning + Acting）由普林斯顿与 Google 于 2022 年提出，是构建 LLM Agent 的**事实标准基础范式**。

#### 核心循环

```
Observation（观察） → Thought（思考） → Action（行动）
     ↑                                          ↓
     └────────── Observation（观察结果）──────────┘
```

#### 示例

```text
用户：北京今天气温多少？乘以 2 是多少？

Thought: 用户想知道北京今天气温，然后乘以 2。我需要先查询天气。
Action: 调用 weather_tool(location="北京")
Observation: 今天北京气温 25°C

Thought: 已经获得气温 25°C，现在需要计算 25 * 2 = 50。
Action: 调用 calculator_tool(expression="25 * 2")
Observation: 50

Thought: 已经得到最终结果，可以回复用户了。
Action: Final Answer: 50
```

#### 为什么 ReAct 成为行业标准

| 优势 | 说明 |
|------|------|
| **符合直觉** | 模拟人类解决问题的真实过程 |
| **可解释性强** | 每一步都显式输出 Thought，便于调试 |
| **效果显著** | 推理 + 行动协同，准确率高于纯推理（CoT）或纯工具调用 |

---

### 1.3 单智能体系统（Single Agent）

单智能体遵循 ReAct 循环，适合工具调用和单线推理任务。

#### 技术实现

| 框架 | 实现方式 |
|------|----------|
| **LangChain** | 使用 `AgentExecutor` 编排循环，开发者定义 `Tool` 集合和 `PromptTemplate` |
| **LangGraph** | 用状态图显式建模工作流，将"思考"和"行动"定义为节点，边连接形成循环 |

#### 典型应用场景

- 客服机器人（查询订单 → 调用 API → 生成回复）
- 数据分析助手（执行 Python → 生成图表）

---

### 1.4 多智能体协作系统（Multi-Agent）

多智能体系统的核心思想是**"分而治之 + 协同通信"**。复杂任务被拆分为子任务，由不同专长的 Agent 负责。

#### 主要协作模式

| 模式 | 原理 | 适用场景 |
|------|------|----------|
| **监督者模式** | Supervisor Agent 动态分派任务给 Worker Agents，并汇总结果 | 任务可明确拆分，需要中央协调 |
| **对等网络** | Agents 通过共享状态直接协作，无中央控制器 | 头脑风暴、互相评审 |
| **层级流水线** | Agents 按固定顺序传递结果（研究 → 写作 → 编辑） | 流程标准化的任务 |

#### LangGraph 实现特点

- 每个 Agent 封装为图中的一个 `Node`
- 使用 `add_conditional_edges` 实现动态任务分发
- 定义共享 `AgentState` 实现跨 Agent 信息传递

---

### 1.5 Agent 模式的演进

ReAct 是基础，但生产级 Agent 通常会叠加更复杂的模式：

| 演进模式 | 核心特点 | 代表框架 |
|----------|----------|----------|
| **Plan-and-Solve** | 先制定完整计划，再逐步执行 | LangChain Plan-and-Execute Agent |
| **Reflexion** | 执行失败后自我反思，记住错误避免重犯 | 高级 Agent 系统 |
| **Multi-Agent** | 多个 ReAct Agent 协作，各自分工 | AutoGen、LangGraph |
| **ReWOO** | 一次性生成所有工具调用计划，减少 LLM 调用次数 | 成本优化型系统 |

---

## 二、Claude Code 与通用 Agent 框架的区别

### 2.1 定位差异：框架 vs 产品

| 维度 | LangChain / LangGraph | Claude Code |
|------|------------------------|-------------|
| **本质** | 开源开发框架 | 终端 AI 编程助手产品 |
| **使用者** | **构建者**：写代码搭建系统 | **终端用户**：命令行交互 |
| **灵活性** | 极高，可定制任何行为 | 在预设能力范围内使用 |
| **对接模型** | 任意 LLM（GPT、Claude、Llama 等） | 固定使用 Anthropic Claude 模型 |

**类比**：LangChain 像乐高积木，Claude Code 像一台已经组装好的机器人。

---

### 2.2 技术栈差异

Claude Code **不是基于 LangChain 或 LangGraph**，而是 Anthropic **自研的 Agent 运行时**。

| 层级 | 通用 Agent（LangChain） | Claude Code |
|------|------------------------|-------------|
| **模型层** | 可切换任意模型 | 直接调用内部 Claude 模型 |
| **SDK** | 通过 LangChain 抽象层 | `@anthropic-ai/sdk` 官方 SDK |
| **工具系统** | 开发者用 `@tool` 自行封装 | 内置工具集（Read、Edit、Bash 等），原生 Tool Use API |
| **Agent 循环** | `AgentExecutor` / `StateGraph` | 自研编排逻辑 |
| **扩展协议** | 写代码注册 Tool | MCP（Model Context Protocol） |

---

### 2.3 Claude Code 的多模式融合架构

Claude Code 不是单一 ReAct，而是**分层混合架构**：

```
用户输入
    ↓
[规划层] Plan-and-Solve：复杂任务 → EnterPlanMode → 任务分解(TaskCreate)
    ↓
[基础层] ReAct循环：Thought → 选择工具 → Observation
    ↓
[安全层] Reflexion-like：评估风险 → 高风险？→ 请求用户确认
    ↓
[执行] 调用工具 / 生成回复
    ↓
[记忆层] 更新 Memory → 跨会话持久化
```

#### 各层详解

**1. 基础层：ReAct**
- 核心交互引擎：观察 → 思考 → 行动
- 工具被严格限定在内置集合（Read、Edit、Bash、Agent、Skill 等）

**2. 规划层：Plan-and-Solve**
- 显式计划模式（`EnterPlanMode` / `ExitPlanMode`）
- 任务分解与追踪（`TaskCreate` / `TaskList`）
- 系统提示明确要求"先探索理解，再动手修改"

**3. 安全与反思层：Reflexion-like**
- 执行前风险评估（"Consider reversibility and blast radius"）
- 破坏性操作确认（`git push --force`、`rm -rf` 等）
- 错误后纠正策略（如 Git hook 失败后创建新提交）

**4. 功能模块化：Multi-Agent Lite**
- `Skill` 工具：调用专门能力模块（如 `review`、`security-review`）
- `Agent` 工具：Spawn 子 Agent 处理复杂子任务
- `memory` 系统：文件化跨会话持久记忆

---

### 2.4 核心差异总结

| 维度 | LangChain ReAct Agent | Claude Code |
|------|------------------------|-------------|
| **模式纯度** | 通常是单一 ReAct 循环 | **ReAct + Plan + Reflexion + 模块化**的混合体 |
| **计划能力** | 依赖 LLM 即时推理 | 有专门的 Plan Mode 和任务追踪系统 |
| **安全机制** | 框架层面基本不提供 | 产品级内嵌安全校验和确认流 |
| **记忆** | 对话历史（短期） | 文件化 Memory + 跨会话长期记忆 |
| **子任务委派** | 需显式构建 Multi-Agent 系统 | 内置 Skill / Agent 工具，开箱即用 |

---

## 三、总结

- **ReAct 是 Agent 设计的"底层协议"**。理解了 ReAct，就掌握了 LangChain `AgentExecutor`、Claude Code 工具调用、甚至 GPT-4 Function Calling 的共同语言。
- **LangChain / LangGraph 是通用框架**，适合快速原型、灵活切换模型、复杂多 Agent 编排。
- **Claude Code 是面向单一模型深度优化的产品**，代表从学术原型（纯 ReAct）到工程化产品的演进路径：**单模式不够用，必须融合多种模式才能处理真实世界的复杂性和风险**。
- 两者不是互斥的，可以结合使用：例如用 Claude Code 开发基于 LangGraph 的 Agent 应用，或在 LangGraph 系统中通过 MCP 调用 Claude Code 的能力。
