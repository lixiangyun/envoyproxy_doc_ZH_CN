## 健康检查

- 健康检查[架构概述](../../Introduction/Architectureoverview/Healthchecking.md)
- [v1 API 参考](../../v1APIreference/HTTPfilters/Healthcheck.md)
- [v2 API 参考](../../v2APIreference/Filters/HTTPfilters/Healthcheck.md)

**请注意：**如果[/healthcheck/fail](../../Operationsandadministration/Administrationinterface.md)管理端口被调用，过滤器将自动运行失败健康检查并设置[x-envoy-immediate-health-check-fail](../../Configurationreference/HTTPfilters/Router.md)头部。（通过[/healthcheck/ok](../../Operationsandadministration/Administrationinterface.md)管理端口恢复此行为）。


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)