# 10. 关键类与函数索引

本页是源码导航，不替代类型声明。签名省略了复杂泛型与 overload，只保留扩展时最重要的契约。

## Core 执行协议

| API | 简化签名 | 职责/扩展点 | 源码 |
| --- | --- | --- | --- |
| `Runnable` | `invoke(input, config?)` | 所有可组合组件的统一基类 | [`runnables/base.ts`](../../libs/langchain-core/src/runnables/base.ts) |
| `RunnableSequence` | `first -> middle[] -> last` | `.pipe()` 生成的顺序管道 | 同上 |
| `RunnableLambda` | `from(func)` | 普通函数/异步生成器适配 | 同上 |
| `RunnableConfig` | callbacks/tags/metadata/signal/... | 跨调用树配置 | [`runnables/config.ts`](../../libs/langchain-core/src/runnables/config.ts) |
| `CallbackManager` | `configure(...)` | 构建运行生命周期观察器 | [`callbacks/manager.ts`](../../libs/langchain-core/src/callbacks/manager.ts) |
| `Serializable` | `toJSON()` | 构造器、alias 与 secret 序列化 | [`load/serializable.ts`](../../libs/langchain-core/src/load/serializable.ts) |
| `load` | `load<T>(text, options?)` | 从序列化 JSON 恢复对象 | [`load/index.ts`](../../libs/langchain-core/src/load/index.ts) |

## 消息、Prompt 与模型

| API | 关键方法/字段 | 职责 | 源码 |
| --- | --- | --- | --- |
| `BaseMessage` | `content`, `response_metadata` | 统一消息基类 | [`messages/base.ts`](../../libs/langchain-core/src/messages/base.ts) |
| `AIMessage` | `tool_calls`, `usage_metadata` | 模型输出 | [`messages/ai.ts`](../../libs/langchain-core/src/messages/ai.ts) |
| `ToolMessage` | `tool_call_id`, `artifact` | 工具执行结果 | [`messages/tool.ts`](../../libs/langchain-core/src/messages/tool.ts) |
| `coerceMessageLikeToMessage` | `(messageLike) -> BaseMessage` | 标准化多种输入形态 | [`messages/utils.ts`](../../libs/langchain-core/src/messages/utils.ts) |
| `trimMessages` | `(messages, options)` | 按 token/角色裁剪上下文 | [`messages/transformers.ts`](../../libs/langchain-core/src/messages/transformers.ts) |
| `BasePromptTemplate` | `formatPromptValue`, `invoke` | Prompt 变量契约 | [`prompts/base.ts`](../../libs/langchain-core/src/prompts/base.ts) |
| `PromptTemplate` | `format(values)` | 字符串模板 | [`prompts/prompt.ts`](../../libs/langchain-core/src/prompts/prompt.ts) |
| `ChatPromptTemplate` | `formatMessages(values)` | 消息模板组合 | [`prompts/chat.ts`](../../libs/langchain-core/src/prompts/chat.ts) |
| `BaseChatModel` | `_generate`, `_streamResponseChunks`, `bindTools` | provider ChatModel 基类 | [`language_models/chat_models.ts`](../../libs/langchain-core/src/language_models/chat_models.ts) |
| `initChatModel` | `(model?, fields?)` | 动态选择/配置 provider 模型 | [`chat_models/universal.ts`](../../libs/langchain/src/chat_models/universal.ts) |
| `BaseOutputParser` | `parse`, `getFormatInstructions` | 文本到类型化输出 | [`output_parsers/base.ts`](../../libs/langchain-core/src/output_parsers/base.ts) |

## Tool 与 Agent

| API | 关键方法/参数 | 职责 | 源码 |
| --- | --- | --- | --- |
| `StructuredTool` | `schema`, `_call`, `responseFormat` | 结构化工具基类 | [`tools/index.ts`](../../libs/langchain-core/src/tools/index.ts) |
| `tool` | `(func, {name, description, schema})` | 创建动态 Tool | 同上及 [`tools/headless.ts`](../../libs/langchain/src/tools/headless.ts) |
| `createAgent` | `{model, tools, middleware, responseFormat...}` | 构造现代 ReAct Agent | [`agents/index.ts`](../../libs/langchain/src/agents/index.ts) |
| `ReactAgent` | `invoke`, `stream`, `streamEvents`, `getGraphAsync` | 编译并运行 Agent 图 | [`agents/ReactAgent.ts`](../../libs/langchain/src/agents/ReactAgent.ts) |
| `createMiddleware` | middleware definition | 类型安全创建生命周期 hook | [`agents/middleware.ts`](../../libs/langchain/src/agents/middleware.ts) |
| `ToolNode` | `invoke(state)` | 并行执行工具调用 | [`agents/nodes/ToolNode.ts`](../../libs/langchain/src/agents/nodes/ToolNode.ts) |
| `toolStrategy` | `(schema, options?)` | 用工具调用获得结构化响应 | [`agents/responses.ts`](../../libs/langchain/src/agents/responses.ts) |
| `providerStrategy` | `(schema)` | 使用 provider 原生结构化输出 | 同上 |

## 文档与检索

| API | 关键方法 | 职责 | 源码 |
| --- | --- | --- | --- |
| `Document` | `pageContent`, `metadata`, `id?` | 文档数据单元 | [`documents/document.ts`](../../libs/langchain-core/src/documents/document.ts) |
| `TextSplitter` | `splitText`, `splitDocuments`, `mergeSplits` | 文本切分基类 | [`text_splitter.ts`](../../libs/langchain-textsplitters/src/text_splitter.ts) |
| `RecursiveCharacterTextSplitter` | `fromLanguage`, `_splitText` | 多级 separator 递归切分 | 同上 |
| `Embeddings` | `embedDocuments`, `embedQuery` | 向量模型基类 | [`embeddings.ts`](../../libs/langchain-core/src/embeddings.ts) |
| `VectorStore` | `addDocuments`, `similaritySearch...` | 向量存储基类 | [`vectorstores.ts`](../../libs/langchain-core/src/vectorstores.ts) |
| `BaseRetriever` | `_getRelevantDocuments`, `invoke` | 检索器基类 | [`retrievers/index.ts`](../../libs/langchain-core/src/retrievers/index.ts) |
| `VectorStoreRetriever` | similarity/MMR | VectorStore 的 Retriever 包装 | [`vectorstores.ts`](../../libs/langchain-core/src/vectorstores.ts) |
| `index` | `{docsSource, recordManager, vectorStore, options}` | 去重、写入与清理协调器 | [`indexing/base.ts`](../../libs/langchain-core/src/indexing/base.ts) |
| `RecordManager` | `exists/update/listKeys/deleteKeys/getTime` | 跟踪索引版本 | [`indexing/record_manager.ts`](../../libs/langchain-core/src/indexing/record_manager.ts) |

## Store 与 Classic

| API | 职责 | 源码 |
| --- | --- | --- |
| `BaseStore` / `InMemoryStore` | 通用批量 K/V 存储 | [`stores.ts`](../../libs/langchain-core/src/stores.ts) |
| `BaseChatMessageHistory` | 对话消息持久化协议 | [`chat_history.ts`](../../libs/langchain-core/src/chat_history.ts) |
| `EncoderBackedStore` | 类型与底层字节 Store 转换 | [`storage/encoder_backed.ts`](../../libs/langchain/src/storage/encoder_backed.ts) |
| `BaseChain` | 传统 input/output key 编排 | [`chains/base.ts`](../../libs/langchain-classic/src/chains/base.ts) |
| `LLMChain` | 传统 Prompt + LLM + Parser | [`chains/llm_chain.ts`](../../libs/langchain-classic/src/chains/llm_chain.ts) |
| `AgentExecutor` | 旧 AgentAction/observation 循环 | [`agents/executor.ts`](../../libs/langchain-classic/src/agents/executor.ts) |
| `BufferMemory` | 全量对话 buffer | [`memory/buffer_memory.ts`](../../libs/langchain-classic/src/memory/buffer_memory.ts) |
| `MemoryVectorStore` | 内存向量存储参考实现 | [`vectorstores/memory.ts`](../../libs/langchain-classic/src/vectorstores/memory.ts) |

## MCP

| API | 职责 | 源码 |
| --- | --- | --- |
| `MultiServerMCPClient` | 管理多 server、工具、资源与 Prompt | [`client.ts`](../../libs/langchain-mcp-adapters/src/client.ts) |
| `ConnectionManager` | stdio/http/sse 连接池 | [`connection.ts`](../../libs/langchain-mcp-adapters/src/connection.ts) |
| `loadMcpTools` | MCP Tool -> DynamicStructuredTool | [`tools.ts`](../../libs/langchain-mcp-adapters/src/tools.ts) |
| `ToolException` | MCP 工具错误分类 | 同上 |

## 最小扩展示例

```ts
import { BaseRetriever } from "@langchain/core/retrievers";
import type { DocumentInterface } from "@langchain/core/documents";
import type { CallbackManagerForRetrieverRun } from "@langchain/core/callbacks/manager";

class MyRetriever extends BaseRetriever {
  async _getRelevantDocuments(
    query: string,
    runManager?: CallbackManagerForRetrieverRun
  ): Promise<DocumentInterface[]> {
    const child = runManager?.getChild("my-backend");
    void child;
    return searchMyBackend(query);
  }
}
```

实现自定义组件时优先继承最接近的 Core 抽象，不要在上层 Agent 中加入 provider 特判。

