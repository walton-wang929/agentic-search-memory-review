# 08 — 隐私、安全与用户控制

## Threat model

- 敏感信息被错误抽取为长期记忆
- Prompt injection 诱导 Agent 读取或泄露 private memory
- 跨用户、跨 workspace、跨项目的 memory 混淆
- 连接器数据超出原授权目的被二次使用
- 删除后 embedding、缓存或摘要仍保留
- 画像导致歧视、价格操纵或机会限制
- 共享对话/屏幕时暴露个人信息

## 控制面

用户应能：

- 全局开启/关闭 memory
- 选择临时聊天
- 查看「系统记得什么」
- 修改、删除、批量清空与导出
- 限制到某个项目、domain 或连接器
- 查看某个答案使用了哪些 memory

## 技术控制

- 强 tenant/user/workspace ACL，检索前强制 scope filter
- 敏感分类器 + restricted store，与普通 prompt context 隔离
- 工具读取采用最小权限与短时 token
- Memory provenance 与每次 read/write audit
- 对不可信网页内容做指令隔离，禁止其决定 memory 读取
- 加密、区域化存储、保留期限和删除队列
- 红队测试 memory exfiltration、cross-user leakage、indirect injection

## Policy defaults

- 医疗、财务、政治、宗教、性取向等敏感推断默认不长期保存
- 未成年人采用更严格默认值
- 共享/企业空间与个人空间严格分离
- 模型训练许可与产品个性化许可分开
- 个性化关闭时不得继续使用派生 profile
