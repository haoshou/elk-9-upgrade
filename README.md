# ELK 9.4.5 升级架构与技术规范 (Upgrade Spec)

## Problem Statement

当前基础设施部署了 Elasticsearch 8.12.2 (4 节点，1 集群，运行于 OpenShift)、Kibana (OpenShift) 以及分布在 OpenShift 和 VPC 上的多个 Logstash 实例。上游与下游存在直接连接 ES 的 8.x 客户端。目标是将整个 ELK 栈平滑升级至 9.4.5 大版本。由于大版本升级涉及不可逆的数据结构变更和废弃 API 移除，需要一套严密的升级路径、客户端兼容性治理方案以及兜底的回滚策略（Rollback Plan）。

## Solution

采用“前置排查 + 两步走滚动升级”的策略，放弃高资源消耗的蓝绿部署，在现有 OpenShift 资源上通过原生的 StatefulSet 进行就地滚动升级（In-place Rolling Upgrade）。外围组件（Logstash）延后升级以保证数据不中断。

## Operations / User Stories

1. 作为平台工程师，我需要先将集群从 8.12.2 升级到 8.x 的最后一个稳定版本，以便在进入 9.x 之前完成所有内部元数据的自动对齐与转换。
2. 作为业务研发，我需要一份基于 Upgrade Assistant 的准确报告，以便在升级前修改我的 8.x 客户端配置（添加 `compatible-with=8` 请求头）或修复废弃 API 调用。
3. 作为运维人员，我需要一套安全的手动驱逐和重启脚本，以便在没有 ECK Operator 的情况下，通过控制 StatefulSet 逐个重启 4 个 ES 节点而不丢失分片。
4. 作为系统管理员，我希望即使遇到最极端的 9.4.5 启动失败，我也能通过快照机制执行回滚，以保证数据的最终安全性。
5. 作为日志平台的消费者，我希望在核心 ES 升级期间，外围的 Logstash 依然能利用向下兼容性继续发送数据，从而保证日志管道不产生断流。

## Implementation Decisions

以下是我们在架构访谈中达成的关键决策，作为整个升级计划的 Single Source of Truth：

- **升级路径 (Upgrade Path)**:
  - Phase 1: `8.12.2` -> `8.latest` (例如 8.17.x)
  - Phase 2: `8.latest` -> `9.4.5`
- **节点运维控制 (Node Orchestration)**:
  - 编排工具：OpenShift 原生 StatefulSet。
  - 操作逻辑：对于每一个 ES Pod，执行以下循环：暂停分片自动分配 (`cluster.routing.allocation.enable=primaries`) -> 执行 Flush 同步刷盘 -> 隔离并修改该 Pod 镜像 Tag -> 启动加入集群 -> 恢复分片分配 (`all`) -> 等待 `_cluster/health` 变为 green。
- **客户端兼容性管控 (Client Compatibility Gate)**:
  - **强制前置条件**：必须在 Kibana Upgrade Assistant 中清除所有 Error 和 Warning 级别的不兼容调用。
  - **兼容性降维**：要求直连的 8.x 客户端在 HTTP 客户端层面强制注入请求头 `Accept: application/vnd.elasticsearch+json;compatible-with=8`。
- **回滚底线策略 (Rollback Plan)**:
  - 升级前触发一次全局的 S3/NFS Snapshot。
  - 发生灾难时的回滚路径：停止所有流量直连 -> 缩容所有 ES 节点至 0 -> 删除/清空全部现存 PV (Persistent Volume) 数据 -> 重新以 8.12.2 镜像扩容 -> 调用 Restore API 从最后快照恢复数据。
- **Logstash 管道时序 (Logstash Sequencing)**:
  - ES 核心集群两步升级期间，所有的 Logstash (OpenShift & VPC) 保持 8.x 版本运行。
  - ES 9.4.5 运行稳定后，择期分批滚动重启 Logstash 至 9.4.5。

## Testing Decisions (Seams)

为了保障升级过程的安全，需要在以下“接缝 (Seams)”进行严格验证：

- **Seam 1: Upgrade Assistant 报告 (Pre-flight)**
  - 验收标准：在 Kibana 中查看，没有任何红色的废弃 API 调用阻挡升级。
- **Seam 2: 集群健康检查 (During Rollout)**
  - 验收标准：每个节点升级重启后，`GET /_cluster/health` 必须恢复为 `green`，且 `unassigned_shards` 必须为 0 才能进入下一个节点。
- **Seam 3: 兼容模式验证 (Staging Client Test)**
  - 验收标准：要求一个高频访问的 8.x 客户端在测试环境挂载 `compatible-with=8` 后，成功执行读写 9.x 集群的操作。

## Out of Scope

- Logstash 的内部 Filter/Grok 规则重构。
- 扩容/缩容现有的 4 节点架构拓扑。
- 修改 Elasticsearch 的 JVM 内存大小配置（保持现状）。
