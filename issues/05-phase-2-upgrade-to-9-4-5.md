# 05: 执行大版本跳跃：从 8.latest 滚动升级至 9.4.5

**What to build:** Elasticsearch 4 节点与 Kibana 完全运行在 9.4.5，数据正常读写，外部客户端流量无感连接。

**Blocked by:** 04: 将 ES 与 Kibana 滚动升级至 8.x 最终稳定版本

**Status:** ready-for-agent

- [ ] 更新 StatefulSet，将 4 个 ES Node 分批滚动升级至 `9.4.5`，轮流验证恢复。
- [ ] 更新 Kibana 至 `9.4.5` 并验证 UI/仪表板可用性。
- [ ] 进行读写集成测试，确保外部系统的业务日志与搜索请求在 9.x 集群运作如常。
