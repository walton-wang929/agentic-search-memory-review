# Agentic Search × 个性化 Memory 产品与技术 Review

[English](./README.md) | 中文

调研 Agentic Search（以 ChatGPT Search / Agent 为代表）如何接入用户个性化记忆，在保证可控、可解释与隐私安全的前提下，返回更相关的搜索结果、方案与行动建议。

> 范围：短期 / 中期 / 长期记忆 × 检索 / 排序 / 生成 / 行动
> 覆盖：小红书、抖音/豆包、微信/元宝、阿里/夸克/千问、Google Search/Gemini、Meta AI、ChatGPT、Claude/Claude Code 等
> 状态：持续更新，事实与推断分开标记

## 核心结论

个性化 Agentic Search 不是简单地把「聊天记录」塞进提示词，而是一条受策略控制的读写链路：

```text
当前请求 + 会话状态
  → 意图与个性化需求判断
  → 分层记忆召回（短 / 中 / 长期）
  → 权限、时效、冲突与敏感性过滤
  → 搜索查询改写 + 候选召回
  → 个性化重排与方案生成
  → 解释「为什么适合你」
  → 用户反馈、记忆写回、衰减或删除
```

最重要的产品原则：

1. **先判断是否需要个性化**：事实查询不应被偏好扭曲。
2. **短中长期不是三张表，而是三种生命周期与使用契约**。
3. **记忆只作为证据之一**：必须与实时搜索结果、明确约束共同决策。
4. **区分“模型推断”与“用户确认”**：高影响偏好应让用户查看、修正和删除。
5. **个性化必须可解释、可关闭、可遗忘**。

## Memory 分层

| 层级 | 典型时长 | 内容 | 主要用途 |
|---|---|---|---|
| [短期记忆](./framework/memory-lifecycle.md#短期记忆-working--session-memory) | 当前会话至数小时 | 当前 query、约束、已看/已排除候选、工具结果 | 保持多轮一致性，避免重复搜索 |
| [中期记忆](./framework/memory-lifecycle.md#中期记忆-episodic--task-memory) | 数天至数月 | 项目、旅行、购物决策、持续任务、近期兴趣 | 跨会话恢复任务，持续优化与监控 |
| [长期记忆](./framework/memory-lifecycle.md#长期记忆-semantic--profile-memory) | 数月至长期 | 稳定偏好、身份关系、习惯、长期目标 | 查询改写、排序、默认值与主动建议 |

完整定义见 [Memory 生命周期框架](./framework/memory-lifecycle.md)。

产品创新方案见 [Memory-Aware Agentic Search 蓝图](./framework/product-blueprint.md)：包含 Search Memory Lens、Project Memory Capsule、Memory Promotion Inbox 和 dual ranking。

## 核心技术模块

1. [记忆采集与授权](./architecture/01-capture-consent.md)
2. [记忆抽取与规范化](./architecture/02-extraction-normalization.md)
3. [分层存储与生命周期](./architecture/03-tiered-storage-lifecycle.md)
4. [记忆检索与上下文装配](./architecture/04-retrieval-context.md)
5. [个性化查询理解、召回与排序](./architecture/05-personalized-search-ranking.md)
6. [个性化生成、解释与行动](./architecture/06-generation-explanation-action.md)
7. [反馈学习、冲突、衰减与遗忘](./architecture/07-learning-forgetting.md)
8. [隐私、安全与用户控制](./architecture/08-privacy-safety-control.md)
9. [评测与实验](./evaluation/README.md)

系统总览：[architecture/README.md](./architecture/README.md)。

## 公司 Review

### 国内

- [小红书：点点 / 问一问 / 搜索](./companies/domestic/xiaohongshu.md)
- [字节跳动：抖音 / 豆包](./companies/domestic/bytedance.md)
- [腾讯：微信搜一搜 / 元宝](./companies/domestic/tencent.md)
- [阿里：夸克 / 千问 / 淘宝](./companies/domestic/alibaba.md)
- [百度及其他](./companies/domestic/others.md)

### 海外

- [OpenAI：ChatGPT Search / Agent / Memory](./companies/overseas/openai.md)
- [Google：Search AI Mode / Gemini / Personal Intelligence](./companies/overseas/google.md)
- [Meta：Meta AI](./companies/overseas/meta.md)
- [Anthropic：Claude / Claude Code](./companies/overseas/anthropic.md)
- [Perplexity、Microsoft 及其他](./companies/overseas/others.md)

横向结论见 [竞争格局与差异](./meta/competitive-landscape.md)。

## 推荐阅读路径

1. [Memory 生命周期](./framework/memory-lifecycle.md)
2. [产品创新蓝图](./framework/product-blueprint.md)
3. [端到端架构](./architecture/README.md)
4. [ChatGPT 个性化 Agentic Search 案例](./case-studies/chatgpt-personalized-search.md)
5. 各公司产品页
6. [评测框架](./evaluation/README.md)
7. [来源与证据等级](./meta/sources.md)

## 事实标记

- **已上线**：官方产品或文档明确可用
- **实验/灰度**：官方宣布但范围受限
- **公开线索**：论文、演讲、招聘或可靠报道可支持
- **推断**：根据产品行为或通用架构推演，不代表公司官方实现
- **未知**：无足够公开证据

## 仓库结构

```text
agentic-search-memory-review/
├── framework/       # Memory 定义、生命周期与产品原则
├── architecture/    # 核心技术模块与端到端架构
├── companies/       # 国内外产品与技术 Review
├── case-studies/    # ChatGPT 等案例
├── evaluation/      # 离线、在线、安全与隐私评测
├── templates/       # 公司和功能 Review 模板
└── meta/            # 竞争格局、来源、术语、研究状态
```

## Disclaimer

本仓库用于产品与技术研究。除明确引用的官方信息外，架构描述可能包含推断；请勿将其视为公司内部实现披露。
