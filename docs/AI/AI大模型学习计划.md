# AI大模型学习计划

## 学习大纲

方向一：模型应用

**核心能力**

- RAG 系统搭建
- Agent 架构设计
- 私有化部署（Ollama / vLLM）
- 工程能力（Java / Python / 后端）

方向二：基座模型

* 微调、模型原理方向

* 微调理解如何实现，调参需要使用的硬件、软件情况

目标：做到理论和实践都一致的状态

## 资料整理

| 网址                                                         | 介绍                     |
| ------------------------------------------------------------ | ------------------------ |
| [AI竞赛经验](https://swhl.github.io/AI-Competition-Collections/Others/) | 参加Ai竞赛               |
| https://www.datawhale.cn/home                                | Ai学习路线网址           |
| https://github.com/datawhalechina/llm-universe               | 动手学大模型             |
| https://www.promptingguide.ai/zh                             | 如何写prompt             |
| https://huggingface.co/docs                                  | hug 模型库+数据集+工具链 |
| https://github.com/LLMBook-zh/LLMBook-zh.github.io           | 大模型参考书籍           |
| https://prompt-engineering.xiniushu.com/                     | 吴恩达提示词工程参考书籍 |
| https://modelcontextprotocol.io                              | Map 协议                 |

## 学习规划

## 第 1 阶段（第 1 个月）：夯实 LLM & AIGC 基础

### 学习目标

- 真正理解 LLM 是怎么“工作的”
- 不再停留在“调 API”

### 必学内容

- Transformer 基本原理（不用推公式）
- Prompt Engineering
- Token / Context Window / 幻觉
- 常见模型对比（Qwen / Llama / DeepSeek）

### 必做实践

- 用 **Ollama** 跑：
  - Qwen2.5
  - Llama3
- 写 Prompt 对比实验

------

## 第 2 阶段（第 2 个月）：RAG 从 0 到 1（重中之重）

### 学习目标

👉 **你要能独立写一个 RAG 系统**

### 学习内容

- 向量数据库原理
- Embedding
- Chunk 策略
- RAG 的 5 种常见失败模式

### 技术栈建议

- Python
- LangChain / LlamaIndex
- Chroma / FAISS / Milvus
- Ollama 本地模型

### 必做项目（面试级）

✅ **企业知识库 RAG**

- PDF / Word / Markdown 导入
- 向量检索
- 命中片段 + LLM 生成回答

### 推荐资源

- 📘 https://docs.llamaindex.ai
- 📘 https://python.langchain.com
- 📘 https://github.com/langchain-ai/langchain

------

## 第 3 阶段（第 3 个月）：Agent 核心能力

### 学习目标

👉 从“问答”升级为“会干活的 AI”

### 学习内容

- Agent 架构
- ReAct
- Plan & Execute
- Tool Calling
- Memory

### 实战项目

✅ **多工具 Agent**

- 搜索
- 代码生成
- 文件处理
- 自动总结报告

### 推荐框架

- LangGraph（强烈推荐）
- AutoGen
- CrewAI

### 推荐资源

- 📘 https://langchain-ai.github.io/langgraph
- 📘 https://github.com/microsoft/autogen
- 📘 https://github.com/joaomdmoura/crewAI

------

## 第 4 阶段（第 4 个月）：MCP + 工程化

### 学习目标

👉 **Agent 工程化 & 可扩展**

### 学习内容

- MCP 协议思想
- Tool Server
- 权限 & 安全
- 多 Agent 协作

### 推荐资源

- 📘 
- 📘 Anthropic MCP 官方示例（GitHub）

------

## 第 5 阶段（第 5 个月）：真实业务项目（核心）

### 至少做 2 个完整项目

1️⃣ **企业级 RAG + 权限**
 2️⃣ **Agent 自动化系统**

👉 要能讲清楚：

- 架构
- 为什么这么设计
- 怎么优化效果