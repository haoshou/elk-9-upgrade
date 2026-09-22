# 05: Phase 1.5 - 将所有 Logstash 升级至 8.x 最终稳定版本

**What to build:** 为防止 ES 跨 9.x 时出现兼容性断层，将所有 Logstash 提升到 8.latest，确保它们能合法向 9.x 写入数据。

**Blocked by:** 04

**Status:** ready-for-agent

- [ ] 将 OpenShift 上的 Logstash 镜像升级至 8.latest。
- [ ] 将 VPC 上的 Logstash 实例升级至 8.latest。
- [ ] 确保所有日志管道流动正常。
