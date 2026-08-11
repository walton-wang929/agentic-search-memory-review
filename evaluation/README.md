# 评测框架

个性化收益不能只看点击率。评测应同时覆盖记忆质量、搜索质量、任务结果、用户控制与长期伤害。

## 1. Memory 写入质量

- candidate extraction precision / recall
- scope、否定、时效与敏感性分类准确率
- 误写长期记忆率
- 用户纠正/删除率
- 冲突与重复检出率

## 2. Memory 读取质量

- relevant memory Recall@K / Precision@K
- stale / conflicted memory usage rate
- scope leakage rate
- sensitive memory unauthorized read rate
- memory service latency 与降级成功率

## 3. 个性化搜索质量

离线：

- nDCG / MRR / Recall@K
- hard constraint satisfaction
- personalized pairwise preference accuracy
- source quality、freshness 与 citation accuracy
- diversity / novelty / coverage

在线：

- reformulation rate 与 time-to-satisfactory-result
- save / compare / action completion
- repeated constraint rate（用户是否还需重复说明）
- long-term retention，但不以无意义时长为目标
- 「关闭个性化」和纠错操作率

## 4. Counterfactual 实验

至少比较：

1. 无 memory baseline
2. 仅短期 session memory
3. 短期 + 中期 task memory
4. 全部 memory，但只用于 rerank
5. 全部 memory，用于 query rewrite + rerank + generation

这样才能识别每层 memory 的真实增量，而不是把模型升级误当个性化收益。

## 5. 安全与长期指标

- filter bubble / viewpoint diversity
- unfair treatment across cohorts
- memory surprise score（用户是否意外系统知道这些）
- unauthorized persistence
- deletion completion SLA
- cross-user / cross-project leakage
- prompt injection memory exfiltration success rate

## 6. 建议评测集

- 旅行：家庭约束、预算、节奏偏好跨会话变化
- 购物：一次性需求与稳定偏好的区分
- 内容：近期兴趣漂移与长期兴趣冲突
- 事实问答：验证系统不会让偏好扭曲客观事实
- 高风险：医疗/财务中限制个性化和敏感记忆
- 多项目：工作、家庭、个人 scope 隔离
