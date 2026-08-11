# Memory 模块的量化表现与 Benchmark Review

> 核验时间：2026-08-11。
> 注意：论文离线指标、厂商自报、第三方统一复测和真实线上 A/B 不能直接横向比较。

## 1. 证据等级

- **论文**：作者论文或技术报告中的离线实验
- **第三方**：非原系统作者、统一 harness 下的复测
- **线上 A/B**：生产流量实验，通常仍由公司作者披露
- **厂商自报**：产品页或厂商 benchmark，不等于同行评审或真实业务 A/B

结论：

1. 结构化 memory 相比 full-context 通常更准确、更省 token。
2. Retrieval、时间更新、冲突和 selective forgetting 仍是主要瓶颈。
3. LoCoMo 高分不能预测 Agent 最终决策成功率。
4. 短视频和推荐线上数据证明长期行为有价值，但不能直接外推到生成回答。
5. TikTok 最终选型必须依赖自建日志 replay 和线上 A/B。

## 2. 长期对话 Memory

### LoCoMo

[Paper](https://arxiv.org/html/2402.17753) · [ACL final](https://aclanthology.org/2024.acl-long.747.pdf) · [Code](https://github.com/snap-research/locomo)

- 公开版 10 段长对话，平均约 300 turns、9K tokens、最多 35 sessions。
- 覆盖 single-hop、multi-hop、temporal、open-domain、adversarial QA，以及事件总结和多模态生成。
- 早期论文数据：
  - Human overall F1：87.9
  - GPT-4-turbo 4K：32.1
  - GPT-3.5-turbo 16K：37.8
  - Observation-RAG top-5：41.4
- ACL 最终版模型更新后报告 GPT-4-turbo overall 51.6，但 adversarial 仅 15.7。

局限：只有 10 个 conversation；论文版本、judge、prompt 和是否包含 adversarial 会显著改变结果。适合作 smoke test，不适合作唯一选型榜。

### LongMemEval

[Paper](https://arxiv.org/html/2410.10813) · [ICLR 2025](https://proceedings.iclr.cc/paper_files/paper/2025/file/d813d324dbf0598bbdc9c8e79740ed01-Paper-Conference.pdf) · [Code](https://github.com/xiaowu0162/LongMemEval)

- 500 个问题。
- S：约 115K tokens、约 40 sessions。
- M：约 500 sessions、约 1.5M tokens。
- 测试 information extraction、cross-session、temporal、knowledge update 和 abstention。

关键数字：

- GPT-4o oracle evidence accuracy 0.870；读取全 115K history 为 0.606，下降 30.3%。
- Llama-3.1-70B：0.744 → 0.334。
- Facts key expansion：Recall@K 平均 +9.4%，QA accuracy +5.4%。
- Time-aware filtering：round-value recall +11.3%，session-value +6.8%。
- Chain-of-Note / 结构化读取最高约 +10 accuracy points。

适合验证 memory indexing、retrieval、reading、更新和时间过滤。

### BEAM

[ICLR 2026 Paper](https://proceedings.iclr.cc/paper_files/paper/2026/file/d7f0cfa0fe759b033d5262e1bb7d4065-Paper-Conference.pdf) · [Code](https://github.com/mohammadtavakoli78/BEAM)

- 100 段多域对话、2,000 个验证问题。
- 长度覆盖 128K、500K、1M、10M。
- 十类能力包括 preference following、update、temporal、multi-session、contradiction 和 abstention。
- LIGHT 相对最强 long-context/RAG baseline，按 backbone 平均提升 3.50%–12.69%。
- 1M 时 GPT-4.1-nano 相对提升 75.9%，Qwen2.5-32B +60.1%。

适合容量、成本和规模退化测试，不直接衡量 Agent 决策成功。

## 3. Agent Experience Memory

### Reflexion

[NeurIPS 2023](https://proceedings.neurips.cc/paper_files/paper/2023/file/1b44b878bb782e6954cd888628510e90-Paper-Conference.pdf)

使用失败后的语言反思作为 episodic memory：

- ALFWorld：相对强 baseline +22pt，最终约 130/134 成功。
- HotPotQA：约 32% → 53%。
- HumanEval Python pass@1：80.1% → 91.0%。
- WebShop 连续四轮没有改善。

WebShop 的失败对经验决策搜索很重要：开放式消费任务中，自由文本反思不一定能正确诊断失败。因此 MVP 应使用结构化 failure code，而不是只依赖 LLM 自我反思。

### ExpeL

[AAAI 2024](https://arxiv.org/html/2308.10144) · [Code](https://github.com/LeapLabTHU/ExpeL)

从成功/失败轨迹提炼通用 insight，并检索相似历史经验：

- HotPotQA：约 39%–40%；ReAct baseline 28%。
- ALFWorld：59%；ReAct baseline 40%。
- WebShop：约 37%–38%，reward 约 0.67。
- HotPotQA → FEVER transfer：Act 58、ReAct 63、ExpeL 无 demo 65、完整 ExpeL 70。

说明抽象经验与具体轨迹召回互补，但仍是小规模 Agent benchmark。

### MemGPT / Letta

[Paper](https://arxiv.org/abs/2310.08560)

- Deep Memory Retrieval：MemGPT + GPT-4-turbo 93.4% accuracy；recursive summarization 35.3%。
- Nested-KV：GPT-3.5 在 nesting=1 即降至 0%；MemGPT + GPT-4 在 0–4 层保持成功。

这些是构造任务，不代表真实搜索业务收益。

### MemoryBank

[Paper](https://arxiv.org/abs/2305.10250)

- 15 个虚拟用户、10 天对话、194 个 probing questions。
- 英文 SiliconFriend-ChatGPT：
  - retrieval accuracy 0.763
  - response correctness 0.716
  - coherence 0.912

缺少严格的同 backbone no-memory baseline，不能估算真实净增益。

## 4. 个性化生成与 Memory Selection

### LaMP

[Paper](https://arxiv.org/html/2304.11406v3) · [Code](https://github.com/LaMP-Benchmark/LaMP)

包含 7 个任务：citation、movie tag/rating、headline、scholarly title、email subject、tweet paraphrase。

Flan-T5-base user split 示例：

- Headline ROUGE-1：0.153 → 约 0.186。
- Email subject：0.379 → 0.587。
- Tweet：0.509 → 0.528。

后续统一研究报告：

- RAG 个性化平均 +14.92%。
- Per-user PEFT +1.07%。
- 两者组合 +15.98%。

适合验证个性化生成，但 ROUGE 不足以衡量决策帮助和用户满意度。

### LongLaMP

[Paper](https://arxiv.org/html/2407.11016v1) · [Data](https://huggingface.co/datasets/LongLaMP/LongLaMP)

- Email/user ROUGE-L：0.1381 → 0.2787，+101.8%。
- Email/temporal：0.1944 → 0.3619，+86.2%。
- Review/user METEOR：0.1030 → 0.1101，+6.9%。
- Topic/temporal ROUGE-1：0.1678 → 0.1853，+10.4%。
- Abstract 任务收益很小，且检索过多会下降。

关键结论：更多 memory 不必然更好。

### PEARL

[Paper](https://aclanthology.org/2024.customnlp4u-1.16.pdf)

Generation-calibrated retriever 直接优化最终生成质量：

- AITA Macro-F1：BM25 57.26/61.66；PEARL 61.21/65.34。
- WORKSM R2：UPR 0.3019；PEARL 0.3094。
- GPT-4o pairwise judge 中，PEARL 对 BM25 在四个设置赢三个。

说明 memory selection 应优化 downstream utility，而不只是 query-memory cosine similarity。

### RUMS

[Paper](https://arxiv.org/abs/2604.14473) · [OpenReview](https://openreview.net/forum?id=8IsxSZ4CRW)

基于 memory 对 response distribution 的 utility 选择：

- 判断是否需要个性化：真实数据 recall 92.3%、specificity 96.0%。
- 与人标 memory subset 对齐：synthetic F1 0.70–0.78；semantic similarity 0.14。
- 真实数据生成相对最佳 retrieval baseline win-rate +12%。
- 100 万请求/日成本估算：
  - RUMS-Binary：$6.9K/月
  - ReContriever：$18.6K/月
  - 全量 memory：$165.3K/月

这些是论文实验和成本模型，不是线上 A/B。

## 5. Memory Infrastructure

### Mem0

[Paper](https://arxiv.org/abs/2504.19413) · [Vendor evaluation](https://docs.mem0.ai/core-concepts/memory-evaluation)

原论文自报：

- LoCoMo LLM-as-judge 相对 OpenAI memory +26%。
- Graph memory 再约 +2%。
- 相对 full-context p95 latency -91%，token >-90%。

2026 厂商 managed stack 自报：

- LoCoMo 92.5，6,956 tokens/query，p50 0.88s。
- LongMemEval 94.4，6,787 tokens，p50 1.09s。
- BEAM-1M 64.1；BEAM-10M 48.6。

这些数字包含未开源优化，OSS 用户不能假定复现。

### MemoryAgentBench：第三方统一复测

[ICLR 2026 Paper](https://proceedings.iclr.cc/paper_files/paper/2026/file/fd1eff9dd295df50a41f2521942fa31d-Paper-Conference.pdf)

在统一协议与默认 GPT-4o-mini backbone 下，测试 retrieval、test-time learning、long-range understanding 和 selective forgetting：

- GPT-5-mini long context：60.6。
- Claude-3.7-Sonnet：49.6。
- BM25：41.5。
- HippoRAG-v2：41.6。
- MemGPT：28.3。
- MIRIX：26.2；换 GPT-4.1-mini 后 37.7。
- Mem0 OSS：21.1。

Selective forgetting multi-hop 最高仅 28%；Mem0 2%、MemGPT 3%。

这说明厂商 LoCoMo 高分不能直接转化为全面 memory 能力，backbone、ingestion、chunk 和 retrieval budget 影响很大。

## 6. 推荐、搜索与真实线上 A/B

这些系统多数是长期行为建模，不完全等同于 conversational memory，但对 TikTok 搜索点击更有业务参考价值。

### PR²：短视频个性化搜索

[Paper](https://arxiv.org/html/2409.11281)

快手线上部署同时个性化 retrieval 与 ranking：

- CTR@10 +10.2%。
- Watch time/query +20%。
- GSB +8.1%。
- Search DAU +1.6%。

这是最接近短视频搜索的公开线上证据之一，但未包含 LLM 生成回答。

### Douyin E-commerce Search LMN

[Paper](https://arxiv.org/html/2502.05558v2)

2024-08-30 至 09-05，20% 流量、超过 1.6 亿用户：

- Order/user +0.87%。
- Order/search +0.72%。
- Serving latency +0.38%。

业务场景最接近 TikTok 搜索，但仍是长期行为 memory/ranking，不是生成答案 memory。

### MIMN

[KDD 2019](https://ar5iv.labs.arxiv.org/html/1905.09248)

淘宝广告线上 A/B：

- CTR +7.5%。
- RPM +6%。
- Industrial AUC 0.6541 → 0.6644。

### SIM

[Paper](https://arxiv.org/abs/2006.05639)

从最长 54,000 条行为中先召回 query/candidate 相关子序列，再精排：

- CTR +7.1%。
- RPM +4.4%。

### Meta Memento

[Paper](https://arxiv.org/html/2605.24051v1)

Facebook Feed/Reels 对 365+ 天历史行为做检索：

- CTR +1.0%。
- Offsite CVR +1.2%。
- Retrieval <10ms。
- 资源效率为线性扩展的 5–10×。

### Kwai MARM

[Paper](https://arxiv.org/html/2411.09425v3)

- 综合 play-time/user +2.079%。
- Retrieval average watch time +0.489%。
- Ranking watch time +1.370%。
- 部分互动指标下降，如 comment -0.669%。

说明 Memory 可能提升主指标，同时伤害其他行为；必须设置多指标 guardrail。

## 7. TikTok 推荐 Benchmark Stack

### 1. MemoryArena：主 Benchmark

[Project](https://memoryarena.github.io/) · [Paper](https://arxiv.org/html/2602.16313v1)

- 256 个 progressive search tasks。
- 平均约 16 个 subtasks、122.4K trace。
- 包含 search、shopping 和约束规划。
- 统一 GPT-5.1-mini task agent：
  - Text-embedding RAG task SR 0.23
  - BM25 0.19
  - Long-context Claude 0.19
  - Letta 0.15
  - Mem0 0.14
- Travel-hard success rate 几乎全为 0。

最贴近“过去搜索、行动和反馈影响后续决策”。

### 2. MemoryAgentBench

重点使用 test-time learning、recommendation、EventQA、FactConsolidation 和 selective forgetting，验证用户偏好变化、旧经验失效与冲突规则更新。

### 3. BEAM

用于 1M/10M 历史下的 accuracy、tokens、latency、写入成本和退化曲线。

### 4. LongMemEval

用于 memory store、时间过滤、knowledge update 和 abstention 的组件回归。

### 5. LoCoMo

只作 single/multi/temporal/adversarial 快速 smoke test，不作为基础设施选型主榜。

### 6. TikTok Internal Replay + Online A/B

从匿名真实 session 构造：

- 历史 query
- 点击、跳过、长播和收藏
- 用户纠正与负反馈
- 最终决策或任务结束

离线指标：

- Experience Retrieval Recall/NDCG
- Provenance accuracy
- Constraint satisfaction
- Decision/task success
- Stale-memory / contradiction error
- Erroneous experience propagation
- Abstention / clarification quality
- P50/P95 latency、tokens、storage 和 write amplification

最终不能选择“LoCoMo 分数最高”的方案，而应选择在以下三个维度 Pareto 最优的系统：

1. MemoryArena / internal replay 的最终决策成功率。
2. MemoryAgentBench 的更新和遗忘能力。
3. BEAM 的规模、延迟和成本。

## 8. 业务解释边界

- PR²、LMN、MIMN、SIM、Memento 和 MARM 提供真实线上证据，但优化对象主要是 retrieval/ranking。
- Mem0、LongMemEval、LoCoMo、LaMP、ExpeL 等主要是离线 benchmark。
- 未找到 MemGPT/Letta、MemoryBank、Generative Agents、Reflexion、ExpeL、LaMP、LongLaMP、PEARL、RUMS 或 Mem0 conversational memory 的公开真实用户随机线上 A/B。
- 任何论文 uplift 都不能直接作为 TikTok Agentic Search 的收益承诺。
