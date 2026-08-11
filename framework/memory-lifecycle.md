# Memory 生命周期：短期、中期、长期

## 为什么按生命周期而不是按数据库分类

同一条事实可以存在于不同层级。例如「这次旅行不坐红眼航班」是短/中期约束；「我通常避免红眼航班」只有在多次出现或用户确认后，才应晋升为长期偏好。

层级由五个因素共同决定：

```text
作用范围 scope × 预期时长 TTL × 稳定性 stability
× 用户确认 confidence × 风险 sensitivity
```

## 短期记忆（Working / Session Memory）

**作用范围：** 当前会话或紧密连续的一组交互。
**典型 TTL：** 分钟至数小时，会话结束后默认丢弃或压缩。

内容包括：

- 当前搜索目标、明确约束与补充条件
- 已执行的查询、工具调用和结果摘要
- 已查看、收藏、比较或排除的候选
- 对话指代与临时状态

对 Agentic Search 的价值：

- 保证多轮查询改写一致
- 避免重复返回用户已排除的结果
- 在工具调用之间传递约束
- 支持局部修改，而非从头规划

主要风险：上下文污染、窗口溢出、错误状态持续放大。

## 中期记忆（Episodic / Task Memory）

**作用范围：** 一个跨会话项目、任务或近期兴趣阶段。
**典型 TTL：** 数天至数月，受任务状态和活跃度驱动。

内容包括：

- 一次旅行、租房、求职、装修或购买决策
- 项目目标、阶段、关键决策、待办与未解决问题
- 近期持续搜索主题和候选集合
- 长时程意图中的监控条件与变化事件

对 Agentic Search 的价值：

- 恢复上次任务，而不是重复访谈
- 将新结果与旧候选做差分
- 结合近期决策阶段改变排序目标
- 支持持续监控与主动提醒

主要风险：任务已经结束但仍影响新搜索；多个项目的偏好相互串扰。

## 长期记忆（Semantic / Profile Memory）

**作用范围：** 跨任务稳定存在的用户模型。
**典型 TTL：** 数月至长期，但必须支持衰减、复核和删除。

内容包括：

- 用户明确保存的偏好与禁忌
- 稳定身份、家庭关系和长期目标
- 经多次证据支持的习惯性选择
- 语言、格式、解释深度等交互偏好

对 Agentic Search 的价值：

- 补全默认约束，减少重复说明
- 做个性化查询扩展与候选重排
- 调整结果呈现和解释方式
- 主动发现与长期目标相关的信息

主要风险：错误画像、刻板化、敏感属性推断、过滤气泡和跨场景越权。

## 晋升、降级与遗忘

```text
短期 observation
  ├─ 用户明确保存 ───────────────→ 长期 confirmed memory
  ├─ 同一任务重复出现 ───────────→ 中期 task memory
  └─ 多任务重复 + 低敏感 + 高置信 → 长期 inferred preference

中/长期 memory
  ├─ 长期未命中 → confidence decay
  ├─ 与新行为冲突 → 降权并请求确认
  ├─ 任务完成 → 归档或删除
  └─ 用户删除/关闭 → 立即从在线和派生索引移除
```

## 建议的数据契约

每条 memory 至少包含：

```yaml
memory_id: string
user_id: string
type: fact | preference | goal | constraint | episode | exclusion
tier: short | medium | long
scope: session | task | domain | global
value: structured payload
source:
  kind: explicit | behavioral | inferred | imported
  event_ids: []
confidence: 0.0..1.0
sensitivity: normal | sensitive | restricted
created_at: timestamp
last_confirmed_at: timestamp?
last_used_at: timestamp?
expires_at: timestamp?
status: active | archived | disputed | deleted
```

## 读取规则

1. 先读取当前会话的明确约束。
2. 再读取当前任务/项目的中期状态。
3. 仅在相关且被允许时读取长期偏好。
4. 当前明确指令始终覆盖历史记忆。
5. 敏感或低置信推断不直接参与高影响决策。
6. 对结果产生实质影响时，应能展示「因何个性化」。
