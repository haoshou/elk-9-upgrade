# 08: Phase 3 - 升级 VPC Logstash 至 9.4.5

**What to build:** 解决跨 VPC 环境的节点代差，宣告整个 ELK 栈跨版本重构任务闭环。

**Blocked by:** 06

**Status:** ready-for-agent

- [ ] 分批次更新 VPC 环境中 Logstash 实例及配置文件至 `9.4.5`。
- [ ] 验证 VPC Logstash 日志能够稳定写入 OpenShift ES 9.4.5 集群，完成收尾。
