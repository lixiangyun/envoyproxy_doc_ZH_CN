## HTTP路由管理和路由发现（RDS）

### RouteConfiguration

[RouteConfiguration proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L35)

- [路由架构概述](../Introduction/Architectureoverview/HTTProuting.md)
- [HTTP路由过滤器](../Configurationreference/HTTPfilters/Router.md)

```
{
  "name": "...",
  "virtual_hosts": [],
  "internal_only_headers": [],
  "response_headers_to_add": [],
  "response_headers_to_remove": [],
  "request_headers_to_add": [],
  "validate_clusters": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 路由配置的名称。例如，它可能会匹配[filter.network.Rds](../v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)中的`route_config_name`。

- **virtual_hosts**<br />
	([VirtualHost](#virtualhost)) 一组虚拟主机组成的路由表。

- **internal_only_headers**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) （可选）指定做为内部使用的HTTP头部字段列表。如果在外部请求中找到它们，则会在过滤器调用之前清除它们。有关更多信息，请参见[x-envoy-internal](../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)。

- **response_headers_to_add**<br />
	([HeaderValueOption](../v2APIreference/Commontypes.md#headervalueoption)) 指定在连接管理器编码时，为每个响应的增加HTTP头部字段列表。在这个级别指定的头部将位于内部的[VirtualHost](#virtualhost)或[RouteAction](#routeaction)的头部之后。

- **response_headers_to_remove**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定在连接管理器编码每个响应中需要删除的HTTP头部字段列表。

- **request_headers_to_add**<br />
	([HeaderValueOption](../v2APIreference/Commontypes.md#headervalueoption)) 指定在HTTP连接管理器路由的每个请求时，需要添加的HTTP头部字段列表。在这个级别指定的头部将位于内部的[VirtualHost](#virtualhost)或[RouteAction](#routeaction)的头部之后。有关更多信息，请参阅自定义请求的头部字段[文档](../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)。

- **validate_clusters**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 可选的bool类型，指定路由表引用的集群是否由集群管理器验证。如果设置为true，并且路由引用了不存在的集群，则路由表将不会加载。如果设置为false，并且路由引用不存在的集群，则路由表将加载，如果在运行时选择路由，则路由器过滤器将返回404。如果路由表是通过[route_config](../v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)选项静态定义的，则此配置默认为true。如果路由表是通过[rds](../v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)选项动态加载的，则此设置默认为false。用户可以在某些情况下覆盖此默认行为（例如，当使用带有静态路由表的CDS时）。

### VirtualHost
[VirtualHost proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L87)

在路由配置的顶层选项有个虚拟主机。每个虚拟主机都有一个逻辑名称以及一组根据传入请求的主机头路由到它的域。这允许单个监听端口多个顶级域服务。一旦基于域选择了虚拟主机，就会根据顺序处理那些路由匹配到哪个上游集群，并且是否执行重定向。

```
{
  "name": "...",
  "domains": [],
  "routes": [],
  "require_tls": "...",
  "virtual_clusters": [],
  "rate_limits": [],
  "request_headers_to_add": [],
  "response_headers_to_add": [],
  "response_headers_to_remove": [],
  "cors": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 虚拟主机的逻辑名称。用于某些统计信息，但与路由无关。

- **domains**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 将与此虚拟主机相匹配的域（主机名）列表。支持通配符，例如：支持“.foo.com”或“-bar.foo.com”。

	注意：通配符将不匹配空字符串。例如“-bar.foo.com”将匹配“baz-bar.foo.com”，但不匹配“-bar.foo.com”。此外，还可以使用特殊的条目“”来匹配任何主机名。整个路由配置中只有一台虚拟主机可以匹配“*”。域名在所有虚拟主机中必须是唯一的，否则配置将无法加载。

- **routes**<br />
	([Route](#route)) 将按顺序匹配传入请求的路由列表。第一个匹配的路由将被使用。

- **require_tls**<br />
	([VirtualHost.TlsRequirementType](#virtualhosttlsrequirementtype-enum)) 指定虚拟主机所提供的TLS类型。如果未指定此选项，则虚拟主机不需要TLS。

- **virtual_clusters**<br />
	([VirtualCluster](#virtualcluster)) 为此虚拟主机定义的虚拟集群列表。虚拟集群用于进行其他统计信息收集。

- **rate_limits**<br />
	([RateLimit](#ratelimit)) 指定将应用于虚拟主机的一组限速配置。

- **request_headers_to_add**<br />
	([HeaderValueOption](../v2APIreference/Commontypes.md#headervalueoption)) 指定添加到由此虚拟主机处理的每个请求的HTTP头部列表。在此级别指定的头部将应用于内部的[RouteAction](#routeaction)头部之后和封装[RouteConfiguration](#routeconfiguration)的头部之前。有关更多信息，请参阅自定义请求头的[文档](../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)。

- **response_headers_to_add**<br />
	([HeaderValueOption](../v2APIreference/Commontypes.md#headervalueoption)) 指定添加到由此虚拟主机处理的每个响应的HTTP头部列表。在此级别指定的头部将应用于内部的[RouteAction](#routeaction)头部之后和封装[RouteConfiguration](#routeconfiguration)的头部之前。

- **response_headers_to_remove**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定由此虚拟主机处理的每个响应中删除的HTTP头部列表。

- **cors**<br />
	([CorsPolicy](#corspolicy)) 表示虚拟主机具有CORS策略。

### VirtualHost.TlsRequirementType (Enum)
[VirtualHost.TlsRequirementType proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L110)

- **NONE**<br />
	(DEFAULT) 虚拟主机没有TLS要求。

- **EXTERNAL_ONLY**<br />
    来自外部请求必须使用TLS。如果请求是来自外部的，并且没有使用TLS，则将发送301重定向，告诉客户端使用HTTPS。

- **ALL**<br />
    所有请求都必须使用TLS。 如果请求没有使用TLS，则会发送301重定向，通知客户端使用HTTPS。

### Route
[Route proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L167)

路由既包括匹配那些请求，以及接下来需要执行的行为（例如，重定向，转发，重写等）。

注意：Envoy通过头匹配支持HTTP方法的路由。

```
{
  "match": "{...}",
  "route": "{...}",
  "redirect": "{...}",
  "metadata": "{...}",
  "decorator": "{...}"
}
```

- **match**<br />
	([RouteMatch](#routematch), REQUIRED) 路由匹配参数。

- **route**<br />
	([RouteAction](#routeaction)) 将请求路由到某个上游群集。

- **redirect**<br />
	([RedirectAction](#redirectaction)) 返回一个重定向。
    
    注意：路由和重定向必须选择其中一个设置。

- **metadata**<br />
	([Metadata](../v2APIreference/Commontypes.md#metadata)) 元数据字段可用于提供有关路由的其他信息。 它可以用于配置，统计和日志记录。元数据应该在与之对应的滤器命名空间下。例如，如果此元数据用于路由器过滤器，则应将过滤器名称指定为`envoy.router`。

- **decorator**<br />
	([Decorator](#decorator)) 匹配路由的标识符。

### WeightedCluster
[WeightedCluster proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L202)

与指定单个上游群集作为请求目标的群集字段相比，`weighted_clusters`选项允许指定多个上游群集以及制定要转发给每个群集的流量百分比的权重。路由器将根据权重选择上游集群。

```
{
  "clusters": [],
  "runtime_key_prefix": "..."
}
```

- **clusters**<br />
	([WeightedCluster.ClusterWeight](#weightedclusterclusterweight), REQUIRED) 指定与路由关联的一个或多个上游群集。

- **runtime_key_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定应该用于构造与每个集群关联的运行时key/value前缀。当指定`runtime_key_prefix`时，路由器将在`runtime_key_prefix+"."+cluster[i].name`下查找与每个上游集群相关的权重，其中`cluster[i]`表示集群组的某个集群。如果群集的运行时key不存在，则配置文件中指定的值将用作默认权重。有关键名称如何映射到底层实现，请参阅[运行时文档](../Operationsandadministration/Runtime.md)。

### WeightedCluster.ClusterWeight
[WeightedCluster.ClusterWeight proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L203)

```
{
  "name": "...",
  "weight": "{...}",
  "metadata_match": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 上游群集的名称。群集必须存在于群集管理器配置中。

- **weight**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 一个0~100之间的整数，当请求与路由匹配时，选择上游集群的权重。集群组中所有集群的权重总和加起来必须为100。

- **metadata_match**<br />
	([Metadata](../v2APIreference/Commontypes.md#metadata)) （可选）端口元数据匹配条件，将仅考虑上游集群中具有与在`metadata_match`中设置的元数据匹配的端口，过滤器名称应该指定为`envoy.lb`。

### RouteMatch
[RouteMatch proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L233)

```
{
  "prefix": "...",
  "path": "...",
  "regex": "...",
  "case_sensitive": "{...}",
  "runtime": "{...}",
  "headers": []
}
```

- **prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 如果指定，则路由采用匹配前缀规则，这意味着前缀必须匹配`:path`头部分。

- **path**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 如果指定，路由是一个精确的路径规则，意味着一旦查询字符串被移除，路径必须与`:path`头完全匹配。

- **regex**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 如果指定，则路由采用正则表达式规则，这意味着一旦查询字符串被移除，正则表达式必须匹配`:path`头。整个路径（不含查询字符串）必须匹配正则表达式。如果只有`:path`头的部分与正则表达式匹配，则实际规则为不匹配。这里定义了正则表达式语法。

    实例:
    - 正则表达式`/b[io]t` 匹配路径 `/bit`
    - 正则表达式`/b[io]t` 匹配路径 `/bot`
    - 正则表达式`/b[io]t` 不匹配路径 `/bite`
    - 正则表达式`/b[io]t` 不匹配路径 `/bit/bot`

    注意：只能选择`prefix`, `path`, `regex`其中一个设置。

- **case_sensitive**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 表示前缀/路径匹配是否不区分大小写。默认值是true。

- **runtime**<br />
	([RuntimeUInt32](../v2APIreference/Commontypes.md#runtimeuint32)) 指定当前路由匹配另外运行时的key。设置一个0-100之间的整数，每当路由匹配时，将会产生一个0-99之间的随机数。如果该随机数<=当前设置的值（首先检查）或者该key不存在，则默认行为，则匹配该路径（假定所有路径都与路由匹配）。可以在运行时逐步进行路由的变更，而无需完整的部署配置。请参阅[流量转移](../Configurationreference/HTTPconnectionmanager/TrafficShiftingSplitting.md)文档以获取详细的说明。

- **headers**<br />
	([HeaderMatcher](#headermatcher)) 指定路由匹配的一组头部字段。路由器将检查请求的头域中与该配置中的头部字段。如果路由配置中的所有头部字段都在请求头部匹配相同的值（或者路由配置的key存在，而value没有明确），则匹配将发生。

### CorsPolicy
[CorsPolicy proto]()

```
{
  "allow_origin": [],
  "allow_methods": "...",
  "allow_headers": "...",
  "expose_headers": "...",
  "max_age": "...",
  "allow_credentials": "{...}",
  "enabled": "{...}"
}
```

- **allow_origin**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定将被允许执行CORS请求的来源。

- **allow_methods**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定`access-control-allow-methods`头部的内容。

- **allow_headers**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定`access-control-allow-headers`头部的内容。

- **expose_headers**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定`access-control-expose-headers`头部的内容。

- **max_age**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定`access-control-max-age`头部的内容。

- **allow_credentials**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 指定资源是否允许凭据。

- **enabled**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 指定是否启用CORS。默认为true。只在路由上有效。




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





### RouteAction.ClusterNotFoundResponseCode (Enum)
[RouteAction.ClusterNotFoundResponseCode proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L333)

- **SERVICE_UNAVAILABLE**<br />
	(DEFAULT) HTTP状态码 - 503服务不可用。

- **NOT_FOUND**<br />
    HTTP状态码 - 404未找到。

### RedirectAction
[RedirectAction proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L548)

```
{
  "host_redirect": "...",
  "path_redirect": "...",
  "response_code": "..."
}
```

- **host_redirect**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 该主机的URL部分将与此值交换。

- **path_redirect**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 路径的URL部分将与此值交换。

- **response_code**<br />
	([RedirectAction.RedirectResponseCode](#RedirectAction.RedirectResponseCode Enum)) 在重定向响应中使用的HTTP状态代码。默认响应码是MOVED_PERMANENTLY（301）。

### RedirectAction.RedirectResponseCode (Enum)
[RedirectAction.RedirectResponseCode proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L555)

- **MOVED_PERMANENTLY**<br />
	(DEFAULT) 永久转移的HTTP状态码 - 301。

- **FOUND**<br />
    Found HTTP状态码 - 302。

- **SEE_OTHER**<br />
    See Other HTTP状态码 - 303。

- **TEMPORARY_REDIRECT**<br />
    临时重定向HTTP状态码 - 307。

- **PERMANENT_REDIRECT**<br />
    永久重定向HTTP状态码 - 308。

### Decorator
[Decorator proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L577)

```
{
  "operation": "..."
}
```

- **operation**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 与此路由匹配的请求相关联的操作名称。如果已启用跟踪，则将使用此信息作为为此请求记录的span名称。

    注意：对于入口（入站）请求或出站（出站）响应，该值可能被`x-envoy-decorator-operation`头覆盖。

### VirtualCluster
[VirtualCluster proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L606)

虚拟集群是一种针对某些重要端口，指定正则表达式匹配规则的方法，例如为匹配的请求显式生成统计信息。当做前缀/路径匹配时这很有用，Envoy并不总是知道应用程序认为是一个端口。因此，Envoy不可能统一发送每个端口的统计数据。然而，系统往往具有高度关联的端口，他们希望获得“完美”的统计数据。虚拟集群统计是完美的，因为它们在下游散发，因此包含网络级别的故障。

虚拟群集统计信息的[文档](../Configurationreference/HTTPfilters/Router.md)。

注意：虚拟群集是一个有用的工具，但我们不建议为每个应用程序端口设置一个虚拟群集。 这既不容易维护，而且匹配和统计输出也是需要代价的。

```
{
  "pattern": "...",
  "name": "...",
  "method": "..."
}
```

- **pattern**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 指定用于匹配请求的正则表达式模式。 请求的整个路径必须匹配正则表达式。 所使用的正则表达式语法在这里定义。
    
    示例：

    - 正则表达式 `/rides/d+` 匹配 `/rides/0` 路径
    - 正则表达式 `/rides/d+` 匹配 `/rides/123` 路径
    - 正则表达式 `/rides/d+` 不匹配 `/rides/123/456` 路径

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 指定虚拟群集的名称。发布统计信息时会使用虚拟群集名称和虚拟主机名称。统计信息由路由器过滤器发出，并记录在[此处](../Configurationreference/HTTPfilters/Router.md#statistics)。

- **method**<br />
	([RequestMethod](../v2APIreference/Commontypes.md#requestmethod-enum))（可选）指定要匹配的HTTP方法。例如GET，PUT等

### RateLimit
[RateLimit proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L629)

全局速率限制[架构概述](../Introduction/Architectureoverview/Globalratelimiting.md)。

```
{
  "stage": "{...}",
  "disable_key": "...",
  "actions": []
}
```

- **stage**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 指在过滤器中设置的阶段。速率限制配置仅适用于具有相同阶段编号的过滤器。默认的阶段编号是0。

     注意：过滤器支持0~10范围的阶段编号。

- **disable_key**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 在运行时禁用此速率限制的key配置。

- **actions**<br />
	([RateLimit.Action](#rateLimitaction), REQUIRED) 此速率限制应用相关的操作列表。顺序很重要，因为按顺序处理操作的，描述符是通过在该顺序中附加描述符条目来组成的。如果某个操作无法添加描述符条目，则不会为该配置生成描述符。请参阅相应的操作[文档](../Configurationreference/HTTPfilters/Ratelimit.md)。

### RateLimit.Action
[RateLimit.Action proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L642)

```
{
  "source_cluster": "{...}",
  "destination_cluster": "{...}",
  "request_headers": "{...}",
  "remote_address": "{...}",
  "generic_key": "{...}",
  "header_value_match": "{...}"
}
```
- **source_cluster**<br />
	([RateLimit.Action.SourceCluster](#RateLimit.Action.SourceCluster)) 基于源群集的速率限制。

- **destination_cluster**<br />
	([RateLimit.Action.DestinationCluster](#RateLimit.Action.DestinationCluster)) 基于目标群集的速率限制。

- **request_headers**<br />
	([RateLimit.Action.RequestHeaders](#RateLimit.Action.RequestHeaders)) 基于请求头的速率限制。

- **remote_address**<br />
	([RateLimit.Action.RemoteAddress](#RateLimit.Action.RemoteAddress)) 基于远程地址的速率限制。

- **generic_key**<br />
	([RateLimit.Action.GenericKey](#RateLimit.Action.GenericKey)) 基于通用密钥的速率限制。

- **header_value_match**<br />
	([RateLimit.Action.HeaderValueMatch](#RateLimit.Action.HeaderValueMatch)) 请求头的内容匹配的速率限制。

    必须正确设置`source_cluster`，`destination_cluster`，`request_headers`，`remote_address`，`generic_key`，`header_value_match`其中的一个。


### RateLimit.Action.SourceCluster
[RateLimit.Action.SourceCluster proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L650)

以下描述符条目被追加到描述符中：

```
("source_cluster", "<local service cluster>")
```

其中`<local service cluster>`源自`--service-cluster`选项。

```
{}
```

### RateLimit.Action.DestinationCluster
[RateLimit.Action.DestinationCluster proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L669)

以下描述符条目被追加到描述符中：

```
("destination_cluster", "<routed target cluster>")
```

一旦请求与路由表规则匹配，则选择以下[路由表配置](../v2APIreference/HTTProutemanagementandRDS.md)设置之一路由集群：

- [cluster](#RouteAction)：表示要到达的上游集群。
- [weighted_clusters](#RouteAction)：从一组具有权重属性的集群组中随机选择一个集群。
- [cluster_header](#RouteAction)：指示请求的头中选择所包含目标群集。

```
{}
```

### RateLimit.Action.RequestHeaders
[RateLimit.Action.RequestHeaders proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L678)

当一个头包含一个与`header_name`匹配的关键字时，附加下面的描述符条目：

```
("<descriptor_key>", "<header_value_queried_from_header>")
```

```
{
  "header_name": "...",
  "descriptor_key": "..."
}
```

- **header_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 从匹配请求的头部名称。头的值用于填充`descriptor_key`的描述符条目的值。

- **descriptor_key**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 在描述符条目中使用的key。

### RateLimit.Action.RemoteAddress
[RateLimit.Action.RemoteAddress proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L694)

以下描述符条目被追加到描述符中，并使用来自[x-forwarded-for](../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-forwarded-for)的可信地址填充：

```
("remote_address", "<trusted address from x-forwarded-for>")
```

```
{}
```

### RateLimit.Action.GenericKey
[RateLimit.Action.GenericKey proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L702)

以下描述符条目被追加到描述符中：

```
("generic_key", "<descriptor_value>")
```

```
{
  "descriptor_value": "..."
}
```

- **descriptor_value**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 描述符条目中使用的值。

### RateLimit.Action.HeaderValueMatch
[RateLimit.Action.HeaderValueMatch proto]()

以下描述符条目被追加到描述符中：

```
("header_match", "<descriptor_value>")
```

```
{
  "descriptor_value": "...",
  "expect_match": "{...}",
  "headers": []
}
```

- **descriptor_value**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 描述符条目中使用的值。

- **expect_match**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 如果设置为true，则该操作将在请求与头部匹配时附加描述符条目。如果设置为false，则该操作将在请求不匹配头部时附加描述符条目。默认值是true。

- **headers**<br />
	([HeaderMatcher](#HeaderMatcher), REQUIRED) 指定速率限制操作应匹配的一组头部字段。该动作将检查请求的头部与配置中的所有指定头。如果配置中的所有头都存在于具有相同值的请求中（或者如果值字段不在配置中则基于存在），则匹配将发生。

### HeaderMatcher
[HeaderMatcher proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto#L778)

注意：

1. 在内部，Envoy始终使用HTTP/2的`:authority`头来表示HTTP/1`Host`头。因此，如果试图在主机上匹配，则匹配`:authority`。
2. 要使用HTTP方法进行路由，请使用HTTP/2特殊的`:method`头。这适用于HTTP/1和HTTP/2，做为Envoy标准化头。例如。

```
{
  "name": ":method",
  "value": "POST"
}
```

```
{
  "name": "...",
  "value": "...",
  "regex": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 指定请求的头部名称。

- **value**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定头部的值。如果值不存在，则将匹配具有头部的请求，而不关心具体的值。

- **regex**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 指定匹配头部值是否采用正则表达式。默认为false。整个请求头的值必须与正则表达式匹配。如果只有请求头的值部分与正则表达式相匹配，则规定不匹配。这里定义了值字段中使用的正则表达式语法。

    示例：
    
    - 正则表达式 `d{3}` 匹配值 `123`
    - 正则表达式 `d{3}` 不匹配值 `1234`
    - 正则表达式 `d{3}` 不匹配值 `123.456`

## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)
