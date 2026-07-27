# LangChain.js Code Wiki

> 基线：官方仓库 `langchain-ai/langchainjs`，提交 `710d74f3fc0ff98d5088c8e0c86752829cfaaa2c`（2026-07-25）。
>
> 本 Wiki 面向需要阅读、扩展或维护 LangChain.js 源码的工程师。它描述当前源码，而不是旧版教程中的 API。

## 阅读路线

| 目标 | 推荐章节 |
| --- | --- |
| 快速建立全局认识 | [01. 项目架构](01-architecture.md) |
| 理解所有组件共同的执行协议 | [02. Core 运行时](02-core-runtime.md) |
| 理解消息、Prompt、模型和解析器 | [03. 模型调用栈](03-models-prompts-messages.md) |
| 理解新版 Agent、工具和中间件 | [04. Agent 系统](04-agents-tools-middleware.md) |
| 理解文档摄取、切分、索引和检索 | [05. RAG 数据链路](05-rag-data-pipeline.md) |
| 维护旧式 Chain、Memory 和 Retriever | [06. Classic 兼容层](06-storage-memory-classic.md) |
| 新增或排查模型/数据库集成 | [07. Provider 集成](07-integrations.md) |
| 将 MCP Server 接入 Agent | [08. MCP 适配器](08-mcp-adapters.md) |
| 安装、构建、测试与发布 | [09. 工程运行方式](09-build-run-test.md) |
| 查关键类、函数和扩展点 | [10. 关键 API 索引](10-api-reference.md) |
| 查包级依赖与变更影响 | [11. 依赖关系](11-dependency-map.md) |

## 一句话架构

LangChain.js 是 pnpm + Turborepo 管理的 TypeScript monorepo：`@langchain/core` 定义稳定协议，`langchain` 用 LangGraph 组合现代 Agent，`@langchain/classic` 保存传统 Chain/Memory/Retriever，独立 provider 包把外部 SDK 映射到 Core 抽象，`@langchain/mcp-adapters` 把 MCP 能力转换为 LangChain Tool。

## 需求覆盖矩阵

| 交付要求 | 对应内容 |
| --- | --- |
| 项目整体架构 | [项目架构](01-architecture.md) 的分层、顶层数据流与跨包时序 |
| 主要模块职责 | 第 02-09 章的模块职责、边界、流程图和时序图 |
| 关键类与函数说明 | [关键 API 索引](10-api-reference.md) 及各模块的实现表 |
| 依赖关系 | [包依赖关系与改动影响](11-dependency-map.md) |
| 项目运行方式 | [安装、构建、测试与发布](09-build-run-test.md) |
| 每个对应模块的流程图和时序图 | 第 01-09 章；参考附录不重复绘制运行时图 |

```mermaid
flowchart LR
    App[应用代码] --> LC[langchain]
    App --> Classic[@langchain/classic]
    App --> Provider[Provider 包]
    App --> MCP[@langchain/mcp-adapters]
    LC --> Core[@langchain/core]
    LC --> LG[@langchain/langgraph]
    Classic --> Core
    Provider --> Core
    MCP --> Core
    MCP --> LG
    Provider --> SDK[外部模型或数据库 SDK]
```

## 范围与“完整”的含义

仓库当前包含 2,537 个受控文件、40 余个工作区包。本文档的完整性以以下层级为边界：

- 覆盖全部一级工作区包及所有 provider 包。
- 解释主要公开抽象、关键实现、依赖方向和典型调用链。
- 为每个主要运行时模块提供流程图和时序图。
- 对重复的 provider 实现按统一适配模式说明，并用能力矩阵覆盖每个包。
- 测试文件和示例不逐文件解释，但说明其组织规则与执行方式。

## 源码入口

- 仓库规则：[AGENTS.md](../../AGENTS.md)
- 根工作区：[pnpm-workspace.yaml](../../pnpm-workspace.yaml)
- 任务图：[turbo.json](../../turbo.json)
- Core 包：[libs/langchain-core](../../libs/langchain-core)
- 主包：[libs/langchain](../../libs/langchain)
- Classic 包：[libs/langchain-classic](../../libs/langchain-classic)
- Provider 包：[libs/providers](../../libs/providers)
- MCP：[libs/langchain-mcp-adapters](../../libs/langchain-mcp-adapters)

## 版本注意事项

- 新应用优先从 `langchain` 和独立 provider 包导入。
- `@langchain/classic` 是兼容与迁移层，不等于底层 Core。
- 当前 `langchain` Agent 由 LangGraph 状态图驱动；旧资料中的 `AgentExecutor` 位于 Classic。
- `BaseChatModel` 同时保留传统 chunk streaming 和新的 content-block event streaming。
- 包均为 ESM 优先，同时由构建工具生成 CJS 与类型声明产物。
