# 01: 执行 8.12.2 集群全局快照与回滚演练确认

**What to build:** 验证 S3/NFS snapshot 策略，手动触发全量快照，确保具备随时推倒重来回滚到 8.12.2 的底线能力。

**Blocked by:** None (can start immediately)

**Status:** ready-for-agent

- [ ] 验证后端存储库 (Repository) 状态正常。
- [ ] 成功执行一次全局 Snapshot 且日志无报错。
- [ ] 确认快照内容可读取，证明回滚防线建立完毕。
