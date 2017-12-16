## HTTP头部清理

出于安全原因，Envoy将根据请求传入的HTTP头（被"清理"的头部），来判断是内部还是外部请求。根据头部key进行清理，可能会导致添加，删除或修改头部。 最终，都是由`x-forwarded-for`标头，请求被认定是内部的还是外部的（请仔细阅读链接部分，因为关于Envoy填充头部过程复杂，并取决于[use_remote_address](../../v1APIreference/Networkfilters/HTTPconnectionmanager.md)设置）。

Envoy可能会对以下头部进行清理：

- [x-envoy-decorator-operation](../HTTPfilters/Router.md)
- [x-envoy-downstream-service-cluster](../HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-downstream-service-node](../HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-expected-rq-timeout-ms](../HTTPfilters/Router.md)
- [x-envoy-external-address](../HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-force-trace](../HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-internal](../HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-max-retries](../HTTPfilters/Router.md)
- [x-envoy-retry-grpc-on](../HTTPfilters/Router.md)
- [x-envoy-retry-on](../HTTPfilters/Router.md)
- [x-envoy-upstream-alt-stat-name](../HTTPfilters/Router.md)
- [x-envoy-upstream-rq-per-try-timeout-ms](../HTTPfilters/Router.md)
- [x-envoy-upstream-rq-timeout-alt-response](../HTTPfilters/Router.md)
- [x-envoy-upstream-rq-timeout-ms](../HTTPfilters/Router.md)
- [x-forwarded-client-cert](../HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-forwarded-for](../HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-forwarded-proto](../HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-request-id](../HTTPconnectionmanager/HTTPheadermanipulation.md)

## 返回
- [上一级](../HTTPconnectionmanager.md)
- [首页目录](../../README.md)
