# 百度：文小言 / 百度搜索 AI 伙伴

## 产品机制

- 文小言提供记忆簿，可保存用户要求、订阅与提醒，并让用户查看、创建、编辑和删除。
- 自动帮我记忆、回答时调用记忆可分别控制，适合区分 write consent 与 read consent。
- 官方说明未经事先许可，不将记忆簿内容用于大模型训练。
- 百度搜索和推荐还可能使用搜索、浏览、账号与使用记录；应与助手记忆簿分开理解。

## Memory 分层

| 层级 | 产品机制 | 证据 |
|---|---|---|
| 短期 | 当前会话、多模态搜索上下文 | 已上线 |
| 中期 | 订阅、提醒、持续任务与历史要求 | 已上线 |
| 长期 | 自动/手动创建的事实、偏好和画像 | 已上线 |

## 技术实现线索

百度千帆公开用户记忆 CRUD API，并区分：

- `explicit`：用户显式记忆
- `implicit`：模型隐式抽取
- `profile`：用户画像

这套分类与独立的 Create / Modify / Describe / Delete 接口，适合作为可审计 memory control plane 的参考；但云 API 不等于所有消费产品内部实现完全相同。

## 优势

- 自动写入与回答调用可分开关闭。
- 记忆簿逐条管理和训练隔离说明较清晰。
- 记忆类型与 CRUD API 的公开程度高。

## 风险与开放问题

- 搜索推荐画像和文小言记忆簿是否共享、如何共享未完全公开。
- 隐式记忆的确认、置信度、TTL 与删除传播需要进一步实测。
- 订阅提醒属于中期任务 memory，不应自动晋升为全局长期偏好。

## Sources

- [文小言记忆簿及训练隔离规则](https://yiyan.baidu.com/docUrl/EB118_infoprotect/llf9tqa4r)
- [ERNIE 用户记忆机制与 CRUD API](https://ai.baidu.com/ai-doc/WENXINWORKSHOP/Km7aaevzl)
- [用户记忆查询与 explicit / implicit / profile 分类](https://ai.baidu.com/ai-doc/WENXINWORKSHOP/5m7a47isq)
- [百度搜索 AI 伙伴用户协议](https://chat.baidu.com/page/servicesagreement.html)
