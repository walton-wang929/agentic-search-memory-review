# 04 — 记忆检索与上下文装配

## Retrieval plan

记忆检索不应对每个 query 使用同一策略。先由 router 输出：

```yaml
need_personalization: true
allowed_scopes: [session, task:trip-japan, domain:travel]
memory_types: [constraint, preference, exclusion]
max_items: 12
allow_sensitive: false
```

## Hybrid recall

- Structured filters：user、scope、type、status、TTL
- Lexical：专有名词、否定条件、精确短语
- Dense：语义相似偏好和历史事件
- Graph：关系、项目、同行人、候选依赖
- Temporal：recency、任务阶段和事件顺序

## Memory reranking

建议特征：

```text
relevance × confidence × freshness × scope_match
× user_confirmation × source_quality
− sensitivity_risk − conflict_penalty − redundancy
```

## Context assembly

- 用结构化槽位传递 hard constraints
- 用简短摘要传递偏好和 episodes
- 同时附 memory_id、来源和时间
- 对冲突项保留两侧证据并请求澄清
- 控制 token budget，避免记忆淹没实时证据

## 安全降级

memory 服务超时或低置信时，返回非个性化结果；不能凭空补全用户画像。
