# TikTok 经验决策搜索：Memory-Aware Agentic MVP

> 目标：提升 Agentic Search 生成回答质量、有效点击和首次失败后的恢复满意率。
> 范围：最小可上线方案，不是“永久记住一切”的通用 Agent。

## 1. MVP 定位

首发只处理同时满足以下条件的 query：

1. 用户需要推荐、比较、规划、教程或避坑，而非单一事实。
2. 答案质量明显依赖个人约束或此前选择。
3. TikTok 视频经验能提供独特证据。
4. 风险等级允许个性化生成。

典型 query：

- “东京适合带父母吃的寿司店”
- “三天两夜曼谷怎么玩”
- “新手怎么拍夜景”
- “200 美元内适合通勤的包”
- “第一次露营要注意什么”

MVP 排除：

- 导航、简单事实、新闻和强时效事实
- 医疗、金融、法律和健康功效建议
- 未成年人敏感个性化
- 自动购买、预订或支付
- 开放式网页浏览、多 Agent 协作和无限循环

首发建议限定为单一市场/语言、登录成年用户，以及旅行、餐厅、低风险购物；其他垂类在证据、安全和评测成熟后扩展。

## 2. Query Router

Router 输出：

```yaml
decision_intent: recommend | compare | plan | tutorial | avoid
vertical: travel | restaurant | shopping | ...
memory_utility: 0.0..1.0
risk_level: low | medium | high
freshness_need: low | medium | high
ambiguity: 0.0..1.0
evidence_availability: 0.0..1.0
agentic_eligible: bool
```

仅当 `decision_intent × memory_utility × evidence_availability` 高于阈值且风险合格时进入 Agent；否则回落现有搜索。

## 3. 最小三层 Memory

Memory 使用结构化 slot，不保存自由文本“人格总结”：

```yaml
scope: session | task | user:domain
key: budget | location | exclusion | preference | ...
value: structured value
polarity: positive | negative
source: explicit | correction | behavior | tool
confidence: 0.0..1.0
created_at: timestamp
expires_at: timestamp
task_id: string?
policy_tag: normal | sensitive | forbidden
```

### Session Memory

生命周期：约 30 分钟或搜索会话结束。

- 当前目标和明确约束
- 已展示、点击、消费和明确排除的视频
- 用户本轮修正
- 已尝试的检索策略
- 首答失败原因

点击、停留、返回和改写只能更新 session/task 弱信号，不能直接形成长期偏好。

### Task Memory

生命周期：默认 7 天，可按垂类调整至 30 天；最多约 12 个核心 slot。

- 任务目标、预算、时间、同行者
- 候选及已排除原因
- 尚未解决的问题
- 上次回答及 evidence IDs
- 任务阶段与恢复状态

任务完成、超时或用户删除后清理或归档。

### User / Domain Memory

MVP 仅保存用户明确要求记住或确认的非敏感偏好：

- 饮食限制
- 预算档位
- 出行节奏
- 常用语言
- 明确不喜欢的内容类型

默认 90 天，可续期、查看、修改和删除。禁止自动保存或推断健康、收入、种族、宗教、性取向、怀孕和疾病等敏感属性。

## 4. 读写链路

### Read path

```text
Query
→ eligibility / privacy gate
→ 并行读取 session / task / domain memory
→ scope、冲突、过期和敏感性过滤
→ constraint bundle
→ planner + retriever + ranker
```

优先级：

```text
当前明确输入
> 当前 session 修正
> task memory
> user/domain memory
```

只把相关 slot 注入上下文，不把完整历史塞进 prompt。

### Write path

```text
交互事件
→ slot candidate extractor
→ 敏感信息过滤
→ 来源、置信度、scope 校验
→ 去重与冲突处理
→ 异步写 session/task
→ 用户确认后写 domain memory
```

写入不阻塞首屏。用户表达稳定偏好时，以“是否保存为旅行偏好？”确认，而不是静默长期保存。

## 5. TikTok 多模态 Evidence

统一 video evidence record：

- 标题、caption、hashtags、发布时间
- ASR：地点、商品、步骤、价格、优缺点
- OCR：菜单、价格、地址、型号和步骤文字
- 视觉理解：地点、菜品、商品和操作步骤
- 评论聚合：共识、争议、更新和避坑
- 创作者信号：垂类一致性、原创性、历史可靠性、商业关系
- 行为信号：长播、收藏、分享、搜索后满意行为

约束：

- 评论只作为聚合证据，高赞不等于事实。
- 广告、赞助和带货进入商业偏置特征。
- ASR/OCR/评论属于不可信数据，不能作为 Agent 指令。
- 价格、营业时间等易变信息必须带时间戳和不确定性。
- 每个具体主张映射到视频或评论聚合 evidence。

## 6. 受限 Agent

### Planner

规则 + 小模型生成：

- 目标
- 硬约束 / 软偏好
- 最多 2–3 个子查询
- 所需 evidence 类型
- 回答结构
- 失败时允许放宽的软约束

MVP 最多一次正常规划和一次恢复规划。

### Retriever

并行混合召回：

- lexical
- semantic
- entity / location / product
- freshness
- creator / topic
- memory constraint expansion

过滤已看、明确排除、重复和低安全质量结果。

### Ranker

第一阶段保证相关性、实体匹配、时效和安全；第二阶段优化决策价值、证据完整性、偏好匹配和来源多样性。

```text
score =
  relevance
  + decision_value
  + evidence_quality
  + preference_match
  + freshness
  - duplicate
  - commercial_bias_risk
  - safety_risk
```

Memory 只做 hard constraint filter 和 soft rerank；保留通用高质量候选与探索位。

### Generator

输出 3–5 项：

- 直接结论
- 每项适合谁
- 关键取舍
- 来源视频卡
- 争议或可能过期的信息
- 一个低摩擦调整入口

证据不足时应说明不确定，不能补全事实。

## 7. Failure Recovery

首答失败信号：

- 无点击或快速退出
- 点击后立即返回
- 同一意图连续改写
- “太贵”“不是这个地方”等明确否定
- 候选重复或 evidence 质量不足

结构化 failure code：

```text
constraint_missing
answer_too_generic
evidence_low_quality
candidate_repeated
preference_misused
comparison_dimension_missing
```

Recovery：

1. 解析失败原因并写入 session memory。
2. 保留硬约束，放宽相关软约束。
3. 改变子查询或 evidence source。
4. 排除已拒绝候选。
5. 展示“已根据 X 调整”的新答案。

只有当两种解释会产生明显不同结果时才追问；否则使用“更便宜、更近、更适合新手、避开网红店、只看近期内容”等 facet 直接恢复。

## 8. 指标

### Answer quality

- Constraint Satisfaction Rate
- Groundedness / citation coverage
- Memory 使用正确率
- 来源多样性、新鲜度和商业偏置
- Memory-on/off pairwise win rate

### Click / consumption

- Answer Source Qualified CTR
- 点击后有效播放和 10/30 秒长播率
- 收藏、分享和关注
- 二跳搜索与任务完成

总观看时长不能单独作为主指标，否则会奖励吸引人但不帮助决策的内容。

### First-failure Recovery Satisfaction Rate

```text
首答失败后两轮或 5 分钟内出现满意信号的 Session
÷ 被判定为首答失败的 Session
```

满意信号：合格点击、长播、收藏、明确正反馈，或任务结束且未继续同意图改写。

Guardrails：

- reformulation / abandonment
- P95 latency、成本和错误率
- memory 删除与隐私投诉
- 敏感 slot 错误写入
- 创作者曝光集中度
- stale-memory / scope leakage

## 9. A/B 实验

用户级稳定分桶：

- A：现有搜索
- B：Agentic Search，仅 session/task memory
- C：B + 用户明确确认的 domain memory

内部再做 2×2 分解：

- Memory retrieval on/off
- Memory-aware generation on/off

要求：

- A/A 与 shadow traffic 校验埋点
- 只在符合 Router 条件的 query 统计主指标
- 按垂类、新老用户、memory hit 分层
- 设置长期 no-memory holdout
- 实验使用独立 namespace，避免 memory 污染分桶
- 不用单一综合分掩盖 CTR 上升但回答质量下降

## 10. 上线和预算

建议顺序：

1. Shadow：路由、证据和 memory 写入仅记录。
2. 1% Session-only：旅行和餐厅。
3. 5% Task memory。
4. 5% Explicit domain memory。
5. 扩展低风险购物与教程。

初始 SLO：

- Memory read P95 ≤ 30ms
- Planner ≤ 100ms
- 并行召回 ≤ 250ms
- Rerank ≤ 120ms
- 相比现有搜索新增 P95 ≤ 600ms
- 首 token P95 ≤ 1.2s
- 完整回答 P95 ≤ 2.0s
- 最多 2 次主链模型调用，恢复时额外 1 次
- 单次输入约 1,200 tokens，输出约 400 tokens

## 11. 最重要的风险

- 错误 memory 比没有 memory 更糟。
- 个性化会放大过滤气泡和商业偏置。
- 视频经验、评论热度不等于事实。
- ASR/OCR 错误会沿 Agent 链路放大。
- 恢复模型可能把正常探索误判为失败。
- 延迟增加可能抵消回答质量带来的 CTR 收益。

因此，MVP 的核心不是“记住更多”，而是**高精度地使用少量任务相关 memory，并在失败时改变检索与排序策略**。
