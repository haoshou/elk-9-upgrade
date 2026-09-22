# Elastic Official Upgrade Guidelines (官方升级指南摘要与原文)

本文档摘录自 Elastic 官方升级指南的 [Plan your upgrade](https://www.elastic.co/docs/deploy-manage/upgrade/plan-upgrade) 章节，作为我们执行升级策略的权威依据。

---

## 📌 核心摘要与解读 (Key Takeaways)

官方明确定义了 Elastic 生态系统的 **“版本向后兼容法则”** 和 **“木桶效应短板原理”**，这直接决定了我们为什么必须采用当前的顺序和步骤。

### 1. 严格的组件升级次序（Upgrade Order）
- **第一优先级**：Elasticsearch 永远是第一个升级的。因为它是核心存储和大脑，只要它升上去了，它就能兼容接收老版本组件发来的数据。
- **第二优先级**：Kibana 必须紧随其后。它的版本必须与 Elasticsearch 保持**绝对一致**。
- **第三优先级**：所有数据采集管道（Logstash、Beats、Elastic Agent），它们作为外围组件，升级永远放在最后。它们可以安全地连接比自己版本新的 ES。

### 2. 跨大版本的“跳板法则”（The Step-Stone Rule）
当你要进行**跨大版本升级**（比如 8.x 到 9.x）时：
- 不能直接把外围组件（如 Logstash）留在很老的版本（如 8.12）然后将 ES 升到 9.x。
- **强制要求**：在 ES 升级到 9.x 之前，必须先将 ES 和**所有的外围采集组件**统一升级到 8.x 的**最后一个最新稳定版本**（如 8.19 / 8.latest）。只有这个最新的 8.x 版本才具备与 9.x 通信的过渡兼容代码。这就是我们方案中加入 `Phase 1.5` 的出处。

---

## 📖 官方原文归档 (Official Verbatim Text)

### Upgrade order
When upgrading the Elastic Stack, the process begins with Elasticsearch, followed by Kibana, which must always be aligned in terms of versioning. Other components can remain on earlier versions as long as they are compatible with the target Elasticsearch version, though we recommend upgrading them as well to benefit from the latest features and fixes.

In general, you should upgrade the components of your Elastic Stack in the following order:

1. **Elasticsearch**
2. **Kibana** (must be kept aligned with the Elasticsearch version)
3. **Fleet Server and Elastic APM** (if used)
4. **Ingest tools** (Beats, Elastic Agent, Logstash, etc.) and Elasticsearch client libraries

If your deployment runs on Elastic Cloud Hosted or Elastic Cloud Enterprise, the platform handles the upgrade and component order automatically... You only need to upgrade any external ingest tools afterward.

### Example of an upgrade plan
Let's assume you are running all Elastic Stack components in version 8.14 and your main goal is to upgrade Elasticsearch and Kibana to the latest 9.5.4, without requiring to upgrade the ingest components (Beats, Elastic Agent, and Logstash) except when required by the upgrade paths.

The minimum steps your plan should include are:

1. Upgrade Elasticsearch and Kibana to the latest 8.19 version, as a requirement for the major upgrade to 9.5.4.
2. **Upgrade all ingest components to the latest 8.19 version, as otherwise they won't be compatible with Elasticsearch running 9.5.4.**
3. Follow all preparation steps and use the upgrade assistant to ensure the cluster is ready for a major upgrade.
4. Upgrade Elasticsearch and Kibana to 9.5.4.
