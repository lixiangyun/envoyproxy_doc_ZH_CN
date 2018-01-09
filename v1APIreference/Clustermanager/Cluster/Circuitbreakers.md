### 熔断

- 熔断[架构概述](../../../Introduction/Architectureoverview/Circuitbreaking.md)。
- 优先级路由[架构概述](../../../Introduction/Architectureoverview/HTTProuting.md#优先级路由)。

可以为每个优先级的定义单独指定熔断设置。关于不同优先级如何使用，详见[配置指南]章节。

```
{
  "default": "{...}",
  "high": "{...}"
}
```

- **default**<br />
	(optional, object) 设置默认优先级的配置对象。

- **high**<br />
	(optional, object) 设置高优先级的配置对象。

### 优先级设置

```
{
  "max_connections": "...",
  "max_pending_requests": "...",
  "max_requests": "...",
  "max_retries": "...",
}
```

- **max_connections**<br />
	(optional, integer) Envoy将允许上游群集的最大连接数。如果未指定，则默认值为1024。

- **max_pending_requests**<br />
	(optional, integer) Envoy将允许上游集群的最大待处理请求数。如果未指定，则默认值为1024。

- **max_requests**<br />
	(optional, integer) Envoy将对上游群集执行的最大并行请求数。如果未指定，则默认值为1024。

- **max_retries**<br />
	(optional, integer) Envoy允许上游集群执行的最大并行重试次数。如果未指定，则默认值为3。

有关更多信息，请参阅[熔断概述](../../../Introduction/Architectureoverview/Circuitbreaking.md)。


## 返回
- [上一级](../Cluster.md)
- [首页目录](../../../README.md)
