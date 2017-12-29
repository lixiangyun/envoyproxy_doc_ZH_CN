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
