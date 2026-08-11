# 03 — 分层存储与生命周期

## 推荐存储平面

| Store | 内容 | 访问模式 |
|---|---|---|
| Session KV / state | 当前 turn、约束、工具结果 | 低延迟、严格 session scope |
| Episodic store | 项目、任务、事件时间线 | task/entity/time 查询 |
| Semantic profile store | 稳定事实与偏好 | structured filter + key lookup |
| Vector / lexical index | memory 摘要与原始证据 | 语义和关键词召回 |
| Relation graph | 人、目标、项目、对象关系 | 多跳约束与关系检索 |
| Consent / audit log | 授权、修改、删除 | 不可抵赖审计与合规 |

## Lifecycle policy

每类 memory 配置：

- 默认 TTL 与最大 retention
- 自动晋升所需证据数和置信度
- 是否要求显式确认
- 允许使用的产品、domain 和动作
- 衰减函数与重新确认周期
- 删除传播范围

## 晋升策略示例

```text
promotion_score =
  repeat_count × cross_task_consistency × explicitness
  × recency × (1 - sensitivity_penalty)
```

达到阈值不等于自动长期保存。对高影响或敏感偏好，应请求确认。

## 删除传播

删除必须覆盖：

1. 主结构化记录
2. dense / lexical 索引
3. 任务摘要与 profile 聚合
4. 在线缓存
5. 待处理训练/分析数据（按政策）

只删 UI 列表而保留派生 embedding，不算真正遗忘。
