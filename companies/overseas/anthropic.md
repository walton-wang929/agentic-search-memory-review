# Anthropic：Claude / Claude Code

## 产品机制

### Claude

- Claude 提供从历史聊天生成的 memory；新版以独立、分类的 entries 取代旧的每日 memory summary。
- 每个 Project 有独立 memory space 和 project summary，避免项目与非项目聊天串扰。
- Projects 将聊天、instructions 和 knowledge base 组织为中期任务空间；内容较大时自动使用 RAG 检索。
- 用户可启停 memory；Incognito chats 不进入 memory。Claude 还提供 profile instructions、project instructions 和 styles。

### Claude Code

- Claude Code 的持久上下文以 `CLAUDE.md` / rules、项目文件、会话与本地/项目 memory 为核心。
- 它展示了 domain-scoped memory 的强形态：规则与经验按用户、项目、仓库路径隔离，并直接影响代码搜索、修改和测试行动。

## Memory 分层映射

| 层级 | Claude | Claude Code |
|---|---|---|
| 短期 | 当前 chat 上下文 | 当前 coding session、tool outputs |
| 中期 | Project memory、project summary、knowledge RAG | repository / project scoped instructions 与经验 |
| 长期 | chat-derived entries、profile instructions | user-level rules / preferences |

## Agentic Search 注入点

- Projects RAG 与 Web Search / Research 组合，先检索项目知识再扩展到 Web。
- Memory entries 和 instructions 影响检索意图、回答风格与工具执行。
- Claude Code 将「个性化搜索」落到代码域：熟悉仓库结构、命令、规范和历史经验后再搜索与行动。

## 技术实现判断

**公开事实：**

- Project 之间 memory 隔离；Project knowledge 可自动切换到 RAG。
- 新版 memory 使用可读取和更新的独立分类条目。

**合理推断：**

- 分类 memory entry 便于细粒度 retrieval、更新和删除，优于单一 profile summary。
- Project scope 是解决 memory 冲突和隐私串扰的重要架构选择。

## 优势

- Project-scoped 中期记忆边界清晰。
- Claude Code 将 memory、search、tool use 和 action 做成完整闭环。
- Incognito、导入/导出和 instructions 提供较强控制。

## 风险与开放问题

- Chat memory、Projects、profile instructions 与 Claude Code files 的控制面较多。
- Web Search 的个性化 ranking 是否直接使用 memory 未公开。
- 企业与个人 memory 的 admin policy 和迁移语义需持续核验。

## Sources

- [Claude chat search and memory](https://support.anthropic.com/en/articles/11817273-use-claude-s-chat-search-and-memory-to-build-on-previous-context)
- [What are Projects?](https://support.anthropic.com/en/articles/9517075-what-are-projects)
- [Claude release notes](https://docs.anthropic.com/en/release-notes/claude-apps)
- [RAG for Projects](https://support.anthropic.com/en/articles/11473015-retrieval-)
- [Claude personalization features](https://support.anthropic.com/en/articles/10185728-understanding-claude-s-personalization-features)
