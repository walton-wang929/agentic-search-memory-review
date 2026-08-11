# Google：Search AI Mode / Gemini / Personal Intelligence

## 产品机制

### 已上线 / 灰度扩展

- **Personal Intelligence** 将 Gemini 或 Search AI Mode 与 Gmail、Google Photos、YouTube、Search 等 Google 应用连接，生成与用户独特上下文相关的回答。
- 功能默认关闭，用户选择开启并控制连接哪些应用；不同地区、账号和套餐的可用范围不同。
- Google 给出的 Search 场景包括：根据购买记录找鞋、结合酒店确认和过去旅行记忆规划家庭旅行。
- Gemini 还支持导入其他 AI 服务的 memory 与聊天历史，降低迁移成本。

## Memory 分层映射

| 层级 | 产品机制 | 证据 |
|---|---|---|
| 短期 | Gemini / AI Mode 当前对话与多模态上下文 | 已上线 |
| 中期 | Gmail 行程/订单、Photos 事件、Search/YouTube 近期活动 | 已上线，按连接器读取 |
| 长期 | 跨 Google 应用形成的长期个人上下文与偏好 | 产品效果已上线；具体持久化画像未知 |

## Agentic Search 注入点

- **Personal query understanding**：无需重复输入订单、家庭或旅行背景。
- **Search synthesis**：将全球 Web 信息与个人应用数据连接。
- **Action**：与 Gemini、Chrome、Search agent / booking 能力结合后，可从个人背景进入下一步执行。
- **Proactive potential**：邮件、日历、搜索历史和 Photos 提供时间与事件信号；具体主动推送策略需单独授权。

## 技术实现判断

**公开事实：**

- Connected Apps 是 opt-in，可逐个关闭。
- Google 表示不会直接使用 Gmail inbox 或 Photos library 训练模型；会使用有限信息如特定 prompts 和 responses 改进功能。

**合理推断：**

- Google 更像「按需联邦读取个人数据」而非单一 memory 表：先路由到应用，再检索相关实体/事件，最后与 Web Search 融合。
- Search ranking 内部如何使用个人上下文未公开。

## 优势

- 个人数据源最广：Search、Gmail、Photos、YouTube、Chrome 与地图/预订生态。
- 中期 episodic memory 尤其强：行程、订单、照片事件天然带时间和实体。
- AI Mode 直接把个人上下文放进搜索主路径。

## 风险与开放问题

- 跨应用组合会放大隐私与「被系统了解过多」的感受。
- Connected Apps 控制不等同于每条 memory 的细粒度查看/删除。
- 必须区分数据检索、派生画像和模型训练三种不同授权。

## Sources

- [Personal Intelligence: Connecting Gemini to Google apps](https://blog.google/innovation-and-ai/products/gemini-app/personal-intelligence/)
- [Personal Intelligence in AI Mode and Gemini expands](https://blog.google/products-and-platforms/products/search/personal-intelligence-expansion/)
- [Personal Intelligence in AI Mode in Search](https://blog.google/products-and-platforms/products/search/personal-intelligence-ai-mode-search/)
- [Gemini Drops, March 2026](https://blog.google/innovation-and-ai/products/gemini-app/gemini-drop-updates-march-2026/)
