# 02: 基于 Upgrade Assistant 推动直连客户端整改

**What to build:** 所有的外部 8.x 直连客户端配置了 `compatible-with=8` 请求头，且 Kibana Upgrade Assistant 中 0 报红，确保 9.x 升级后不引发业务阻断。

**Blocked by:** None (can start immediately)

**Status:** ready-for-agent

- [ ] 在现有的 Kibana 8.12.2 中运行 Upgrade Assistant 报告，导出红项记录。
- [ ] 要求所有直连 ES 的业务方代码注入 HTTP header: `Accept: application/vnd.elasticsearch+json;compatible-with=8`。
- [ ] 重新观测 24 小时，确保 Upgrade Assistant 审计不再有拦截项。
