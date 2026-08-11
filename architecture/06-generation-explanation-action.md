# 06 — 个性化生成、解释与行动

## 生成原则

- 先满足当前明确要求，再使用历史偏好
- 将实时来源与 memory provenance 分开
- 只在相关时提及个人上下文，避免「被监视感」
- 不把低置信推断表述为确定事实
- 提供替代方案，避免将用户锁死在既有模式

## 结果结构

```yaml
answer:
  candidates: [...]
  recommendation: ...
  rationale:
    current_constraints: [...]
    remembered_preferences: [...]
    live_evidence: [...]
  alternatives: [...]
  memory_controls:
    used_memory_ids: [...]
    correction_action: ...
```

## Tool / action 参数

记忆可为预订、购买、日历等工具补默认参数，但：

- 涉及支付、发送、提交或敏感数据时必须确认
- 地址、联系人、证件等 restricted memory 不进入普通生成上下文
- 工具执行前展示关键参数和来源

## 解释 UX

推荐轻量展示：

- 「根据你这次要求的预算与之前保存的安静偏好排序」
- 「不使用个性化」
- 「修改这个偏好」

解释应服务于控制和纠错，而不是暴露内部 chain-of-thought。
