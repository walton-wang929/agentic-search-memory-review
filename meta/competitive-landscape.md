# 竞争格局与差异

> 0–3 为研究初评，不是性能 benchmark。分数表示公开可见产品完整度与技术证据，不代表内部能力。

| 玩家 | 短期 | 中期 | 长期 | Search 融合 | Action | 用户控制 |
|---|---:|---:|---:|---:|---:|---:|
| OpenAI / ChatGPT | 3 | 3 | 3 | 3 | 3 | 3 |
| Google / Gemini | 3 | 3 | 2.5 | 3 | 3 | 2.5 |
| Meta AI | 3 | 2.5 | 3 | 2 | 2 | 2 |
| Claude / Claude Code | 3 | 3 | 3 | 2.5 | 3 | 3 |
| 小红书 / 点点 | 3 | 2 | 1.5 | 3 | 1.5 | 1.5 |
| 豆包 / 抖音 | 3 | 2.5 | 3 | 2.5 | 2.5 | 2.5 |
| 微信 / 元宝 | 3 | 2 | 1.5 | 2.5 | 3 | 1.5 |
| 千问 / 夸克 / 淘宝 | 3 | 3 | 2.5 | 3 | 3 | 2 |

## 四种产品路线

### 1. 显式 Memory Assistant

代表：ChatGPT、Claude、豆包。
核心：独立 memory entries / summary、管理页、历史聊天检索和临时会话。

### 2. Connected Personal Intelligence

代表：Google。
核心：不只依赖对话历史，而是按需连接 Gmail、Photos、Search 等个人数据源。

### 3. Recommendation Graph → Assistant

代表：Meta、小红书、抖音。
核心：将成熟兴趣图谱和内容推荐信号延伸到生成式回答；最大风险是兴趣画像与用户确认 memory 混淆。

### 4. Transactional Task Memory

代表：阿里、微信生态。
核心：订单、物流、旅行、服务和支付状态形成中期 memory，搜索后可直接行动。

## 最值得借鉴的组合

- **长期 memory 控制**：ChatGPT / Claude
- **跨应用个人上下文**：Google Personal Intelligence
- **兴趣实时更新**：Meta / 抖音 / 小红书推荐系统
- **任务与交易闭环**：阿里 / 微信
- **Project scope 隔离**：Claude Projects / Claude Code
- **大规模 memory infra**：阿里公开的结构化 + 向量统一路线

## 核心判断

最终领先者不会只拥有最大 memory，而会同时做到：

1. 在正确时机读取最小必要 memory；
2. 用实时检索纠正过时画像；
3. 将个性化影响清楚展示给用户；
4. 让用户按 session、project、domain 和全局控制；
5. 把选择与行动结果写回任务，而不是盲目写入永久 profile。
