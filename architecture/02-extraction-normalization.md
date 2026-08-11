# 02 — 记忆抽取与规范化

## Pipeline

```text
raw events → candidate extraction → schema validation
→ entity resolution → dedupe/merge → sensitivity classification
→ confidence calibration → write-policy decision
```

## 抽取对象

- fact：用户明确事实
- preference：可排序的倾向
- constraint：必须满足或排除的条件
- goal：正在追求的结果
- episode：带时间与任务上下文的事件
- relation：人与人、人与项目、项目与候选的关系

## 关键实现

- LLM structured output + JSON Schema 做候选抽取
- 规则/分类器做敏感性、否定表达和 scope 校验
- Entity resolution 统一「父母」「爸妈」等引用
- 每条 memory 保存 provenance，不只保存摘要
- NLI/LLM judge 检测新旧 memory 的 entailment、duplicate、conflict

## 不应抽取

- 助手自己的猜测或生成内容
- 未被用户接受的候选方案
- 单次点击形成的长期人格判断
- 与产品目的无关的敏感信息

## 质量指标

- extraction precision / recall
- negation 与 scope 准确率
- duplicate / conflict 检出率
- calibrated confidence
- sensitive-memory false write rate
