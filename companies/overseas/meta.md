# Meta：Meta AI

## 产品机制

### 已上线

- Meta AI 可记住用户在 WhatsApp、Messenger 等一对一聊天中分享的特定信息；用户可明确要求记住，系统也可从上下文提取重要细节。
- Meta AI App 延续跨设备/入口上下文，并可结合用户已在 Meta 产品中选择分享的 profile、likes 和 engagement 生成更相关回答。
- Meta 还将生成式 AI 交互作为 Feed、Reels 和广告推荐信号，并在 2026 年扩大其他企业活动数据对 AI responses 等非广告内容的个性化使用。
- 记忆可删除；功能与数据使用范围受地区和账号设置影响。

## Memory 分层映射

| 层级 | 产品机制 | 证据 |
|---|---|---|
| 短期 | 一对一对话上下文、图像编辑连续上下文 | 已上线 |
| 中期 | 跨 Meta AI 入口继续任务；近期 AI 交互进入推荐系统 | 已上线 |
| 长期 | 明确/上下文抽取的记忆、profile、likes、跨产品 engagement | 已上线 |

## Agentic Search 注入点

- **Answer personalization**：饮食、旅行、语言等偏好影响推荐。
- **Content retrieval / recommendation**：社交图谱、profile 与 engagement 是 Meta 的独特强项。
- **Cross-surface loop**：AI 交互影响 Feed/Reels；Feed 行为又可影响 AI 回答。

## 技术实现判断

**公开事实：**

- 一对一聊天记忆与社交平台画像共同用于个性化。
- 群聊不进入该记忆功能（至少 2025 初次发布范围）。

**合理推断：**

- Meta 可复用成熟推荐系统的 user embedding、兴趣图谱和实时行为特征。
- 显式 AI memory 与广告/内容推荐画像可能属于不同 store 与政策面；内部边界未公开。

## 优势

- 拥有最成熟的大规模内容推荐与兴趣建模基础。
- 社交关系、内容互动和 AI 对话形成强反馈闭环。
- 分发入口覆盖 WhatsApp、Instagram、Facebook、Messenger 与眼镜。

## 风险与开放问题

- AI 对话用于内容和广告个性化，用户需要清楚理解跨产品影响。
- 生成式助手 memory 与传统广告画像的控制可能分散。
- 个性化搜索容易与商业推荐目标混合。

## Sources

- [Building Toward a Smarter, More Personalized Assistant](https://about.fb.com/news/2025/01/building-toward-a-smarter-more-personalized-assistant/)
- [Introducing the Meta AI App](https://about.fb.com/news/2025/04/introducing-meta-ai-app-new-way-access-ai-assistant/)
- [Improving Recommendations With AI at Meta](https://about.fb.com/news/2025/10/improving-your-recommendations-apps-ai-meta/)
- [Better Personalization and Activity Controls](https://about.fb.com/news/2026/06/better-personalization-and-changes-to-controls-for-your-activity-from-other-businesses/)
