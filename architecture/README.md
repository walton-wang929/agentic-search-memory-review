# 端到端技术架构

## 总览

```text
┌──────────────────── Experience / Agent Orchestrator ────────────────────┐
│ request → intent router → planner → search/tools → answer/action       │
└──────────────────────────────┬──────────────────────────────────────────┘
                               │ personalization policy
              ┌────────────────▼─────────────────┐
              │ Memory Read Path                │
              │ 1. scope & consent gate         │
              │ 2. short/medium/long retrieval  │
              │ 3. freshness/conflict filter    │
              │ 4. context budget & provenance  │
              └───────────────┬──────────────────┘
                              │
              ┌───────────────▼──────────────────┐
              │ Personalized Search              │
              │ query rewrite → recall → rerank  │
              │ → synthesis → explanation/action│
              └───────────────┬──────────────────┘
                              │ user/tool events
              ┌───────────────▼──────────────────┐
              │ Memory Write Path                │
              │ extract → normalize → dedupe     │
              │ → risk/consent → store/promote   │
              └───────────────┬──────────────────┘
                              │
       ┌──────────────────────▼─────────────────────────┐
       │ Governance: view/edit/delete, TTL, audit, eval │
       └────────────────────────────────────────────────┘
```

## 九个核心模块

| # | 模块 | 关键问题 |
|---|---|---|
| 01 | [采集与授权](./01-capture-consent.md) | 哪些信号允许进入记忆候选？ |
| 02 | [抽取与规范化](./02-extraction-normalization.md) | 如何从交互中得到结构化、带来源的 memory？ |
| 03 | [分层存储与生命周期](./03-tiered-storage-lifecycle.md) | 如何按 scope/TTL/稳定性管理？ |
| 04 | [检索与上下文装配](./04-retrieval-context.md) | 何时读哪类记忆，怎样避免污染？ |
| 05 | [个性化搜索与排序](./05-personalized-search-ranking.md) | 记忆如何影响 query、召回和 ranking？ |
| 06 | [生成、解释与行动](./06-generation-explanation-action.md) | 如何生成个性化但不固化用户？ |
| 07 | [反馈、冲突与遗忘](./07-learning-forgetting.md) | 如何学习、纠错、衰减和删除？ |
| 08 | [隐私、安全与控制](./08-privacy-safety-control.md) | 如何防止越权、泄露和敏感推断？ |
| 09 | [评测与实验](../evaluation/README.md) | 如何证明收益大于伤害？ |

## 读取路径

1. **Personalization router** 判断本次请求是否需要个性化以及允许使用的 scope。
2. 并行召回 session、task/project、profile memory。
3. 使用相关性、置信度、时效、敏感性、冲突和用户授权做过滤。
4. 在 token / latency budget 内装配最小必要上下文。
5. 分别作用于 query rewrite、搜索过滤、候选重排、生成和工具参数。
6. 输出 provenance，支持 UI 解释「使用了哪些偏好」。

### Personalization permission levels

Router 应同时输出意图和个性化许可：

- `P0`：禁用 memory，只返回通用结果；适合部分敏感或合规场景。
- `P1`：只使用当前会话。
- `P2`：允许当前 task/project memory。
- `P3`：允许长期偏好，但不得改变客观事实。
- `P4`：用户明确授权的跨应用深度个性化。

事实型、法律、医疗、安全等请求默认降低级别；用户当前明确选择可覆盖系统默认。

## 写入路径

1. 捕获显式陈述、行为反馈和任务结果。
2. LLM/规则抽取候选 memory，绑定证据事件。
3. 去重、合并、冲突检测和敏感分类。
4. 按政策决定：仅会话使用、写入项目、请求确认后长期保存，或拒绝写入。
5. 写结构化 store，并更新向量/倒排/图索引。
6. 定期执行 TTL、衰减、复核和删除传播。

## 关键设计选择

### Memory before search vs after search

- **Search 前**：改写 query、补约束、选择数据源；收益大但容易收窄召回。
- **Search 后**：对广泛候选做个性化重排；更安全，但计算更贵。
- 推荐双阶段：轻量长期偏好用于 query expansion，不做硬过滤；明确约束和任务记忆用于 hard filter；完整个性化在 rerank 阶段完成。

### Shared embedding vs dedicated memory retriever

- 通用 embedding 简单，但偏好、事件、否定约束的语义不同。
- 推荐多索引：结构化过滤 + lexical + dense + graph，再由 memory reranker 融合。

### Prompt-only vs model-level personalization

- Prompt/RAG 易控制、可删除、可解释，适合大多数产品。
- Adapter/finetune 能降低延迟但难以单用户更新与彻底遗忘。
- 推荐把用户事实保留在外部 memory plane；模型级学习只吸收匿名、聚合的通用规律。

### Source of truth vs derived indexes

- 关系型/事件存储保存版本、权限、双时态与原始 evidence，是权威数据源。
- 向量、倒排、图索引和 profile summary 都是可重建的派生物。
- 删除和 schema/embedding 升级应从权威记录向所有派生索引传播。

## 非功能目标

- 读取延迟：不显著拖慢首个搜索请求
- 可用性：memory 服务故障时退化为非个性化搜索
- 一致性：删除能传播到缓存、向量索引和派生摘要
- 可审计：任何个性化结果可追溯到 memory 与源事件
- 可移植：用户可导出关键 profile 和项目状态
