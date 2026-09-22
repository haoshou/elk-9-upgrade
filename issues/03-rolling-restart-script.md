# 03: 产出基于原生 StatefulSet 的 ES 滚动维护脚本

**What to build:** 获得一套经过审查的运维脚本，实现：`暂停分片分配 -> Flush 刷盘 -> 替换镜像重启 -> 恢复分片分配 -> 轮询等待 Green` 的自动化闭环操作。

**Blocked by:** None (can start immediately)

**Status:** ready-for-agent

- [ ] 编写禁用 shard allocation 脚本 (`"cluster.routing.allocation.enable": "primaries"`).
- [ ] 编写执行 `POST /_flush/synced` (或对应替代) 同步刷盘操作脚本。
- [ ] 编写恢复 allocation 并轮询检查 `_cluster/health` 返回 `green` 且 `unassigned_shards` 为 0 的验证脚本。
