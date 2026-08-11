# 产品创新蓝图：Memory-Aware Agentic Search

## 产品定位

不是「更懂你的聊天机器人」，而是：

> 在每次搜索中，仅使用与当前任务相关、用户允许且仍然有效的记忆，帮助用户更快完成决策，同时能解释、纠正和遗忘。

## 三个核心界面

### 1. Search Memory Lens

每次结果页提供轻量 Lens：

```text
本次个性化使用：
✓ 当前预算
✓ 东京旅行项目
✓ 已保存「安静、近地铁」

[编辑] [本次不用] [查看通用排序]
```

价值：让个性化从不可见黑盒变成可调的搜索参数。

### 2. Project Memory Capsule

把中期 memory 做成项目胶囊，而不是散落聊天记录：

- 当前目标和阶段
- 硬约束与软偏好
- 已看 / 已排除 / 已选择
- 关键决策及其理由
- 监控任务与截止时间
- 可归档、分享、复制或结束

价值：适合旅行、租房、求职、装修、购买等长任务；避免污染全局 profile。

### 3. Memory Promotion Inbox

系统不静默把行为写成永久人格，而是定期提出少量候选：

```text
你在三个旅行项目中都选择了安静酒店。
是否保存为长期旅行偏好？

[保存到旅行] [仅本次] [不是我的偏好]
```

价值：用确认换取高质量长期 memory，同时降低惊讶和错误画像。

## Search ranking 创新

### Dual ranking

同时维护：

- Universal ranking：对所有用户的质量/相关性排序
- Personal ranking：结合当前任务与 profile 的排序

UI 允许快速切换和解释差异，系统用两者的 rank delta 发现个性化是否过强。

### Memory budget

每次请求不是「能读多少读多少」，而是分配预算：

```yaml
session: 50%
task: 35%
long_term: 15%
```

事实查询可把长期预算降为 0；复杂购物/旅行再提高。

### Contradiction-first retrieval

除了找支持历史偏好的证据，还主动找：

- 新指令与旧偏好的冲突
- 实时事实使旧偏好失效
- 高质量但不符合旧习惯的探索项

价值：防止个性化系统自我强化。

## 主动 Agent 设计

只有中期项目中显式开启的 tracker 才可主动运行：

- 订阅对象、条件、频率与截止日期可见
- 提醒包含「发生了什么变化」和「为什么与你相关」
- 变化后进入新的搜索/比较/行动循环
- 项目结束时自动询问归档或删除

## MVP

### Phase 1

- Session memory + 项目 Capsule
- 显式保存长期偏好
- Search Memory Lens
- 个性化 rerank，不做召回硬过滤

### Phase 2

- 自动 memory 候选与 Promotion Inbox
- 多源个人数据连接器
- 反事实/通用排序对照
- 项目级 tracker

### Phase 3

- 跨任务偏好晋升与动态衰减
- 多人/家庭 memory scope
- 端侧敏感 memory 与隐私计算
- 可移植 memory import/export

## North-star metric

**Personalized Task Success Lift**：

```text
个性化任务完成率 − 同用户非个性化 baseline 完成率
```

同时设置 guardrails：错误 memory 使用率、关闭/删除率、惊讶度、结果多样性和 scope leakage。
