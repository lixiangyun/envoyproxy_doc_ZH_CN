## 路由

路由过滤器实现了HTTP转发。几乎所有Envoy部署的HTTP代理场景都有使用。过滤器的主要工作是遵循路由表中配置的指令。除了转发和重定向之外，过滤器还处理重试，统计等。

- [v1 API 参考](../../v1APIreference/HTTPfilters/Router.md)
- [v2 API 参考](../../v2APIreference/Filters/HTTPfilters/Router.md)

### HTTP头部
路由器在出口/请求路径以及入口/响应路径上消耗和设置各种HTTP报头。它们在本节中有记录。

- [x-envoy-expected-rq-timeout-ms](#x-envoy-expected-rq-timeout-ms)
- [x-envoy-max-retries](#x-envoy-max-retries)
- [x-envoy-retry-on](#x-envoy-retry-on)
- [x-envoy-retry-grpc-on](#x-envoy-retry-grpc-on)
- [x-envoy-upstream-alt-stat-name](#x-envoy-upstream-alt-stat-name)
- [x-envoy-upstream-canary](#x-envoy-upstream-canary)
- [x-envoy-upstream-rq-timeout-alt-response](#x-envoy-upstream-rq-timeout-alt-response)
- [x-envoy-upstream-rq-timeout-ms](#)
- [x-envoy-upstream-rq-per-try-timeout-ms](#x-envoy-upstream-rq-timeout-ms)
- [x-envoy-upstream-service-time](#x-envoy-upstream-service-time)
- [x-envoy-original-path](#x-envoy-original-path)
- [x-envoy-immediate-health-check-fail](#x-envoy-immediate-health-check-fail)
- [x-envoy-overloaded](#x-envoy-overloaded)
- [x-envoy-decorator-operation](#x-envoy-decorator-operation)

#### x-envoy-expected-rq-timeout-ms
#### x-envoy-max-retries
#### x-envoy-retry-on
#### x-envoy-retry-grpc-on
#### x-envoy-upstream-alt-stat-name
#### x-envoy-upstream-canary
#### x-envoy-upstream-rq-timeout-alt-response
#### x-envoy-upstream-rq-timeout-ms
#### x-envoy-upstream-rq-per-try-timeout-ms
#### x-envoy-upstream-service-time
#### x-envoy-original-path
#### x-envoy-immediate-health-check-fail
#### x-envoy-overloaded
#### x-envoy-decorator-operation


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)