
### RouteAction
[RouteAction proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L306)

```
{
  "cluster": "...",
  "cluster_header": "...",
  "weighted_clusters": "{...}",
  "cluster_not_found_response_code": "...",
  "metadata_match": "{...}",
  "prefix_rewrite": "...",
  "host_rewrite": "...",
  "auto_host_rewrite": "{...}",
  "timeout": "{...}",
  "retry_policy": "{...}",
  "request_mirror_policy": "{...}",
  "priority": "...",
  "request_headers_to_add": [],
  "response_headers_to_add": [],
  "response_headers_to_remove": [],
  "rate_limits": [],
  "include_vh_rate_limits": "{...}",
  "hash_policy": [],
  "use_websocket": "{...}",
  "cors": "{...}"
}
```

- **cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指示请求路由到的上游群集。

    注意：只能选择`cluster`, `cluster_header`, `weighted_clusters`其中一个设置。

- **cluster_header**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Envoy将通过从请求头中读取以`cluster_header`命名的HTTP头的值来明确要路由的群集。如果没有找到引用的群集，Envoy将返回一个404响应。

    注意：在内部，Envoy始终使用HTTP/2的`:authority`头来表示HTTP/1的`Host`头。因此，如果试图在使用`Host`匹配，则使用`:authority`替代。

    注意：只能选择`cluster`, `cluster_header`, `weighted_clusters`其中一个设置。

- **weighted_clusters**<br />
	([WeightedCluster](#WeightedCluster)) 可以为该路由指定多个上游群集。根据每个群集的权重，将请求路由到其中一个上游群集。请参阅[流量拆分](../Configurationreference/HTTPconnectionmanager/TrafficShiftingSplitting.md)以获取详细说明。

    注意：只能选择`cluster`, `cluster_header`, `weighted_clusters`其中一个设置。

- **cluster_not_found_response_code**<br />
	([RouteAction.ClusterNotFoundResponseCode](#RouteAction.ClusterNotFoundResponseCode-Enum)) 未找到配置的群集时使用的HTTP状态码。默认响应码是503，服务不可用。

- **metadata_match**<br />
	([Metadata](../v2APIreference/Commontypes.md#metadata))（可选）端口元数据匹配条件，将仅考虑上游集群中具有与在`metadata_match`中设置的元数据匹配的端口，过滤器名称应该指定为`envoy.lb`。

- **prefix_rewrite**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 表示在转发过程中，匹配的前缀（或路径）重写的值。此选项允许应用程序的URLs使用不同的路径（通过反向代理层公开的路径）。

- **host_rewrite**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 表示在转发过程中，主机头将重写的值。

    注意：只能选择`host_rewrite`, `auto_host_rewrite`其中一个设置。

- **auto_host_rewrite**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 表示在转发过程中，主机头将与群集管理器选择的上游主机的主机名交换。此选项仅适用于目标群集的类型为`strict_dns`或`logical_dns`。对于其他群集类型设置为true将无效。

    注意：只能选择`host_rewrite`, `auto_host_rewrite`其中一个设置。

- **timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 指定路由的超时时间。如果未指定，则默认值为15秒。

    注意：该超时包括所有重试。 另请参阅[x-envoy-upstream-rq-timeout-ms](../Configurationreference/HTTPfilters/Router.md#x-envoy-upstream-rq-timeout-ms)，[x-envoy-upstream-rq-per-try-timeout-ms](../Configurationreference/HTTPfilters/Router.md#x-envoy-upstream-rq-per-try-timeout-ms)和[重试概述](../Introduction/Architectureoverview/HTTProuting.md)。

- **retry_policy**<br />
	([RouteAction.RetryPolicy](#RouteAction.RetryPolicy)) 表示该路由具有重试策略。

- **request_mirror_policy**<br />
	([RouteAction.RequestMirrorPolicy](#RouteAction.RequestMirrorPolicy)) 表示路由有请求镜像策略。

- **priority**<br />
	([RoutingPriority](#RoutingPriority)) 可选，指定路由的优先级。

- **request_headers_to_add**<br />
	([HeaderValueOption](../v2APIreference/Commontypes.md#headervalueoption)) 指定将被添加到匹配此路由请求的一组头。在这个级别指定的头部将添加在`VirtualHost`和`RouteConfiguration`的头部之前。有关更多信息，请参阅自定义请求头的[文档](../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)。

- **response_headers_to_add**<br />
	([HeaderValueOption](../v2APIreference/Commontypes.md#headervalueoption)) 指定一组头，这些头将被添加到匹配此路由的请求响应中。在这个级别指定的头部将添加在`VirtualHost`和`RouteConfiguration`的头部之前。

- **response_headers_to_remove**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定一个HTTP头的列表，将从匹配该路由的请求响应中删除。

- **rate_limits**<br />
	([RateLimit](#ratelimit)) 指定可应用于该路由的一组速率限制配置。

- **include_vh_rate_limits**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 指定速率限制过滤器是否应包含虚拟主机速率限制。默认情况下，如果路由配置的速率限制，虚拟主机的`rate_limits`不适用于请求。

- **hash_policy**<br />
	([RouteAction.HashPolicy](#routeactionhashpolicy)) 指定用于环哈希负载平衡的哈希策略列表。每个散列策略都是单独评估的，并且组合结果用于路由请求。组合的方法是确定性的，使得相同的散列策略列表将产生相同的散列。由于散列策略检查请求的特定部分，因此可能无法产生散列（即散列头不存在）。如果（且仅当）所有配置的散列策略未能生成散列，则不会为该路由生成散列。在这种情况下的行为与没有指定哈希策略（即环哈希负载平衡器将随机选择后端）相同。

- **use_websocket**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 表示是否允许连接到该路由的HTTP/1.1客户端升级到WebSocket连接。默认值是false。

    注意：如果设置为true，Envoy会希望匹配这个路由的第一个请求包含WebSocket升级头。如果头不存在，连接将被拒绝。如果设置为true，Envoy将在客户端和上游服务器之间设置纯TCP代理。因此，拒绝WebSocket升级请求的上游服务器也需要负责关闭相应的连接。在此之前，Envoy将继续将数据从客户端代理到上游服务器。若该路由允许websocket升级，则不支持重定向，超时和重试。

- **cors**<br />
	([CorsPolicy](#corspolicy)) 表示该路由具有CORS策略。

### RouteAction.RetryPolicy
[RouteAction.RetryPolicy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L380)

HTTP重试[架构概述](../Introduction/Architectureoverview/HTTProuting.md)。

```
{
  "retry_on": "...",
  "num_retries": "{...}",
  "per_try_timeout": "{...}"
}
```

- **retry_on**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定重试触发的条件。这些与`x-envoy-retry-on`和`x-envoy-retry-grpc-on`记录的具有相同效果。

- **num_retries**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 指定允许的重试次数。此参数是可选的，默认值为1。这些与`x-envoy-max-retries`记录的具有相同效果。

- **per_try_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 指定每个重试尝试的超时时间，值为非零。该参数是可选的。为`x-envoy-upstream-rq-per-try-timeout-ms`记录的相同条件适用。

    注意：如果未指定，Envoy将使用全局路由超时请求。因此，当使用基于5xx的重试策略时，超时请求将不会被重试，因为总超时时长已经耗尽。


### RouteAction.RequestMirrorPolicy
[RouteAction.RequestMirrorPolicy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L415)

路由器能够将流量从一个集群映射到另一个集群。目前的实现是“fire and forget”，这意味着在返回来自主集群的响应之前，Envoy不会等待`shadow`集群作出响应。所有正常的统计数据都会被收集用于`shadow`集群，使得该功能对测试有用。

在遮蔽期间，`host/authority`头部被改变，以便附加`-shadow`。这对于日志记录很有用。例如，`cluster1`变为`cluster1-shadow`。

```
{
  "cluster": "...",
  "runtime_key": "..."
}
```

- **cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 指定请求将被镜像到的群集。群集必须存在于群集管理器配置中。

- **runtime_key**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 如果未指定，则将对目标群集的所有请求进行镜像。如果指定，Envoy将查找运行时key以获取请求的镜像百分比。有效值为0到10000，支持0.01％的增幅。如果在配置中指定了运行时的key，但在运行过程中不存在，则默认为0，因此请求的0％将被镜像。

### RouteAction.HashPolicy
[RouteAction.HashPolicy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L463)

如果上游群集使用散列负载平衡器，则指定路由的散列策略。

```
{
  "header": "{...}",
  "cookie": "{...}",
  "connection_properties": "{...}"
}
```

- **header**<br />
	([RouteAction.HashPolicy.Header](#RouteAction.HashPolicy.Header)) Header哈希策略。

- **cookie**<br />
	([RouteAction.HashPolicy.Cookie](#RouteAction.HashPolicy.Cookie)) Cookie哈希策略。

- **connection_properties**<br />
	([RouteAction.HashPolicy.ConnectionProperties](#RouteAction.HashPolicy.ConnectionProperties)) 连接属性的哈希策略。

    注意：只能选择`header`, `cookie`, `connection_properties`其中一个设置。

### RouteAction.HashPolicy.Header
[RouteAction.HashPolicy.Header proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L484)

```
{
  "header_name": "..."
}
```

- **header_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 将用于获取哈希key的请求头部名称。如果请求头不存在，则不会产生散列。

### RouteAction.HashPolicy.Cookie
[RouteAction.HashPolicy.Cookie proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L484)

Envoy支持两种类型的Cookie关联：

1. 被动：Envoy需要一个存在于cookie头部的cookie，并对其值进行哈希处理。
2. 产生：根据请求发送到的端口，Envoy会根据客户端响应中的第一个请求生成并设置一个有效期（TTL）的cookie。然后，客户端将在随后的所有请求中进行携带这个散列以确保这些请求被发送到相同的端口。Cookie是通过散列源、目标端口和地址生成的，以便同一连接上的多个独立的HTTP2流将独立地接收相同的cookie，即使它们同时到达Enovy。

```
{
  "name": "...",
  "ttl": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 将用于获取散列key的cookie的名称。 如果cookie不存在，并且下面的ttl没有设置，则不会产生散列。

- **ttl**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 若指定，在cookie不存在的情况下，会生成一个带有TTL的cookie。

### RouteAction.HashPolicy.ConnectionProperties
[RouteAction.HashPolicy.ConnectionProperties proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L495)

```
{
  "source_ip": "..."
}
```

- **source_ip**<br />
	([bool](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 哈希源IP地址。
