# 04: Phase 1 - 将核心 ES 与 Kibana 升级至 8.x 最终稳定版本

**What to build:** 核心集群底层元数据完成格式自动对齐，安全升至 8.x latest 状态，集群保持 Green。

**Blocked by:** 01, 02, 03

**Status:** ready-for-agent

- [ ] 按序滚动升级 4 个 Elasticsearch Node 至 8.latest，每次重启后验证集群转为 Green。
- [ ] 滚动升级完成后，更新 OpenShift 上的 Kibana 镜像并验证访问正常。
