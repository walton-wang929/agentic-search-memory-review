# 05 — 个性化查询理解、召回与排序

## 四个注入点

### 1. Query understanding

解析当前明确约束，并判断哪些历史偏好相关。当前指令优先级最高。

### 2. Query rewrite / expansion

将稳定偏好变成软扩展，例如：

```text
原始：东京酒店
扩展：东京酒店 + 安静 + 近地铁 + 适合带父母
```

除用户明确禁忌外，不建议在召回前做过强过滤，以免形成漏召回。

### 3. Candidate retrieval

并行召回通用结果与个性化垂直结果，保留一定探索流量。

### 4. Personalized reranking

```text
score =
  w1 * query_relevance
  + w2 * constraint_satisfaction
  + w3 * preference_match
  + w4 * freshness_and_quality
  + w5 * novelty_or_diversity
  - w6 * risk
```

权重随意图而变：事实问答弱化 preference；旅行/购物决策增强 preference；高风险领域限制个性化。

## 可选技术

- Learning-to-rank / deep ranker 加用户、任务和候选特征
- Two-tower user-item retrieval 做大规模候选召回
- LLM reranker 对小规模候选做约束推理
- Contextual bandit 在安全边界内平衡 exploitation / exploration
- Counterfactual logging 评估个性化排序的增量收益

## 防过滤气泡

- 保留非个性化 baseline 候选
- 加多样性和探索约束
- 展示「为你排序」并允许切回通用结果
- 对重大选择提供反方/替代候选
- 不根据敏感属性做价格歧视或机会限制
