# OpenAI：ChatGPT Search / Agent / Memory

## 产品机制

### 已上线

- ChatGPT 将 **Saved Memories** 与 **Reference Chat History** 分开：前者是明确或自动保存、持续考虑的要点；后者从历史聊天中动态寻找相关信息。
- Memory 可使用聊天、文件和已连接应用的上下文；2026 年更新增加 **Memory Sources**，允许用户查看某次回答引用的 saved memory、历史聊天、自定义指令，以及部分账号的文件/Gmail。
- 用户可查看、修改、删除 memory，关闭 saved memory 或 chat history，并使用 Temporary Chat 避免读取和写入。
- ChatGPT Search / Deep Research / Agent 可与上述个性化上下文组合，用于推荐、研究和下一步行动。

## Memory 分层映射

| 层级 | 产品机制 | 证据 |
|---|---|---|
| 短期 | 当前对话上下文、Agent 工具状态 | 已上线 |
| 中期 | Reference Chat History、Projects/任务历史、文件与连接器相关上下文 | 已上线；具体内部结构未知 |
| 长期 | Saved Memories、自定义指令 | 已上线 |

## Agentic Search 注入点

- **Query understanding**：历史偏好和目标帮助消歧与补充约束。
- **Retrieval**：2026 release notes 明确提到更快搜索历史对话以找到正确上下文。
- **Synthesis**：memory、文件、邮件与实时 Web 证据共同影响推荐和 next steps。
- **Action**：ChatGPT Agent 可通过浏览器、连接器和工具执行任务；memory 可减少重复输入。

## 技术实现判断

**公开事实：**

- Saved Memories 与 chat history 是不同控制面；saved memory 与聊天记录分开保存。
- Memory Sources 展示回答个性化所用的部分来源。

**合理推断：**

- 存在 memory extraction / summary、历史对话 retrieval、相关性过滤与 context assembly。
- Search/Agent 应在执行前通过策略层决定是否引用 memory；官方未公开 ranking 特征或存储架构。

## 优势

- 显式记忆 + 动态历史检索双轨完整。
- Memory 与 Search、Deep Research、Agent、文件和连接器在一个产品中汇合。
- Temporary Chat、管理页与 sources 提供较成熟的控制和解释。

## 风险与开放问题

- 删除聊天不会自动删除从中生成的 saved memory，用户需要理解双重删除语义。
- Memory Sources 官方说明可能不展示所有影响因素，解释仍不完整。
- 个性化如何影响 Web 搜索 query、source selection 和 ranking 未公开。

## Sources

- [OpenAI Memory FAQ](https://help.openai.com/en/articles/8590148)
- [Reference saved memories / chat history](https://help.openai.com/en/articles/11146739-how-does-reference-saved-memories-work)
- [ChatGPT release notes](https://help.openai.com/en/articles/6825453-chatgpt-release-notes-2026-05-17-OpenAI)
- [Introducing ChatGPT agent](https://openai.com/index/introducing-chatgpt-agent/)
