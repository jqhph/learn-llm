# 学习记录：L15 — 系统监控与可观测性

## 核心收获

1. **可观测性三层体系**：
   - 基础设施层（CPU/内存/磁盘/网络）—— Node Exporter + cAdvisor
   - 应用层（QPS/延迟/错误率）—— Prometheus Client + Grafana
   - LLM 层（token 消耗/输入输出/质量评分）—— Langfuse
   - 三个层次缺一不可，且需要能互相关联排查

2. **Prometheus 指标埋点实战**：
   - 四种指标类型（Counter/Gauge/Histogram/Summary）在 RAG 场景下的具体应用
   - FastAPI 中间件自动记录请求数和延迟，业务代码中手动记录检索分和 token 消耗
   - 命名规范：`rag_<metric>_<unit>`，Counter 加 `_total`，Histogram 的桶/计数/总和
   - PromQL 四大核心模式：`rate()` 转换 Counter、`histogram_quantile()` 计算分位数、`sum by()` 聚合、阈值比较

3. **Grafana 仪表盘设计**：
   - Dashboards as Code：通过 Provisioning 自动加载数据源和仪表盘，全部版本控制
   - RAG 专属面板：QPS、P50/P95/P99延迟、错误率、检索结果数分布、检索平均分、token 消耗、CPU/内存
   - "指标 → 日志 → 追踪"的关联工作流——从 Grafana 仪表盘一个异常点开始，下钻到 Loki 日志，再到 Langfuse 追踪

4. **Langfuse LLM 调用链追踪**：
   - 核心概念：Trace（完整请求）/ Span（子阶段）/ Generation（LLM 调用）/ Score（评分）/ Session（会话组）
   - 延迟初始化：Langfuse 未配置时优雅跳过，不影响业务
   - 树形追踪结构：一次 /ask 请求 = Trace → retrieval Span + llm_call Generation，一眼看穿瓶颈在检索还是生成

5. **Loki + Promtail 日志聚合**：
   - Loki vs ELK：Loki 只索引 label 更轻量，适合 Docker 结构化日志场景
   - Promtail 通过 Docker Socket 自动发现容器，按容器名标注 label
   - LogQL 语法：`{service="rag-api"} |= "ERROR" | json | latency_ms > 5000`

6. **Alertmanager 告警体系**：
   - 分组（Grouping）避免告警风暴、抑制（Inhibition）高级别抑制低级别、路由（Routing）按严重级别分发
   - 飞书/钉钉/企业微信三平台 webhook 统一集成
   - 关键告警规则：错误率 > 5%、P95 > 10s、检索分数 < 0.3、token 消耗突增 3x

7. **检索质量退化自动检测**：
   - Golden Test Set 设计（5+ 个有确定答案的查询）
   - 三项检查：检索平均分 vs 基线、Recall@K、Embedding 一致性
   - 相对变化率（比基线下降 20% 告警）而非绝对值——避免假告警
   - cron 或 GitHub Actions 定时运行，失败自动通知

## 理解转变

- 过去以为监控就是"看 CPU 和内存"，现在理解 RAG 系统的可观测性需要 **三层同时覆盖**——基础设施、应用、LLM——任何一层缺失都会导致排查盲区
- 指标、日志、追踪三者不是"三选二"的关系，而是 **递进排查链**：指标发现异常 → 日志查看详情 → 追踪定位原因。缺一个环节，排查时间从 5 分钟变成 1 小时
- 检索质量退化的自动检测比想象中更重要：LLM 的回答质量下降往往是"慢性病"，不会触发 500 错误，但"回答变差了"这个事实对用户信任的伤害比系统挂了还大
- "Dashboards as Code"（仪表盘即代码）是比"在 UI 里拖面板"更正确的做法——改面板也要走 Git 审查，避免"改了但不知道是谁改的"

## 后续关联

- L16（RAG 安全策略与权限体系）：监控体系可用于安全审计——记录谁、什么时候、查了什么
- L17（成本控制与性能优化）：本课的 token 消耗指标（rag_tokens_total）是成本分析的原始数据来源
- L18（人工兜底）：质量巡检的告警可以作为人工介入的触发条件
- L22（Agentic RAG）：Langfuse 追踪对 Agent 的多步推理调试至关重要——每一步 agent 的思考、工具调用、结果评估都可以作为 trace 中的 span

## 为什么学这个

没有可观测性的 RAG 系统等于在"裸奔"。本课之前，你对系统中发生的一切都靠猜测——用户说慢了，你不知道慢在哪；出错了，你不知道错在哪。有了这套监控体系，从"用户投诉了才知道"变成"系统自动告警"，从"猜哪里慢了"变成"一眼看到 P95 延迟曲线"，从"不知道回答质量"变成"每天自动巡检评分"。这是 RAG 系统从"可用"到"可信任"的关键一跃。
