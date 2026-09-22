# 06: 滚动重启并升级 OpenShift 上的 Logstash 实例至 9.4.5

**What to build:** OpenShift 区域内的日志管道完成版本统一，期间数据未断流。

**Blocked by:** 05: 执行大版本跳跃：从 8.latest 滚动升级至 9.4.5

**Status:** ready-for-agent

- [ ] 分批次替换 OpenShift 环境中 Logstash 的镜像 Tag 为 `9.4.5`。
- [ ] 确认重启后 Logstash Pipeline 成功建立连接并持续消费上游队列。
