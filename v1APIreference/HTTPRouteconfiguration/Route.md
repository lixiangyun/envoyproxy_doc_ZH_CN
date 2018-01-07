### 路由

路由指定请求如何匹配相应的路径以及接下来要做什么（例如，重定向，转发，重写等）的规范。

注意：Envoy支持通过匹配HTTP头中的方法进行路由。

```
{
  "prefix": "...",
  "path": "...",
  "regex": "...",
  "cluster": "...",
  "cluster_header": "...",
  "weighted_clusters" : "{...}",
  "host_redirect": "...",
  "path_redirect": "...",
  "prefix_rewrite": "...",
  "host_rewrite": "...",
  "auto_host_rewrite": "...",
  "case_sensitive": "...",
  "use_websocket": "...",
  "timeout_ms": "...",
  "runtime": "{...}",
  "retry_policy": "{...}",
  "shadow": "{...}",
  "priority": "...",
  "headers": [],
  "rate_limits": [],
  "include_vh_rate_limits" : "...",
  "hash_policy": "{...}",
  "request_headers_to_add" : [],
  "opaque_config": [],
  "cors": "{...}",
  "decorator" : "{...}"
}
```
- **prefix**<br />
	(sometimes required, string) 如果指定，则路由使用匹配前缀规则，这意味着前缀必须匹配路径头的开始部分。
	
	注意：必须指定`prefix`，`path`或`regex`其中之一。

- **path**<br />
	(sometimes required, string) 如果指定，则路由采用一个精确的路径匹配规则，这意味着一旦路径字符串匹配，则将会被移除，
	
    注意：必须指定`prefix`，`path`或`regex`其中之一。

- **regex**<br />
	(sometimes required, string) 如果指定，则路由采用正则表达式匹配规则，这意味着一旦路径字符串匹配，则将会被移除，正则表达式必须匹配`:path`头。整个完整的路径（不含查询字符串）必须匹配正则表达式。如果只有`:path`头的子序列与正则表达式匹配，则规则上不匹配。这里定义了[正则表达式语法](http://en.cppreference.com/w/cpp/regex/ecmascript)。
	
    示例：
    - 正则表达式 /b[io]t 匹配路径 /bit
    - 正则表达式 /b[io]t 匹配路径 /bot
    - 正则表达式 /b[io]t 不匹配路径 /bite
    - 正则表达式 /b[io]t 不匹配路径 /bit/bot
    
    注意：必须指定`prefix`，`path`或`regex`其中之一。
- **cors**<br />
	(optional, object) 指定路由的CORS策略。

- **cluster**<br />
	(sometimes required, string) 如果不是重定向路由（未指定`host_redirect`或`path_redirect`），则必须指定`cluster`，`cluster_header`或`weighted_clusters`其中之一。指定群集时，其值指示请求应转发到的上游群集。

- **cluster_header**<br />
	(sometimes required, string) 如果不是重定向路由（未指定`host_redirect`或`path_redirect`），则必须指定`cluster`，`cluster_header`或`weighted_clusters`其中之一。当指定`cluster_header`时，Envoy将通过从请求头中读取由`cluster_header`命名的HTTP头的值，来明确要路由到的集群。如果相应的头没有找到，或者引用的群集不存在，Envoy将返回一个404响应。

    注意：在内部，Envoy始终使用HTTP/2的`:authority`头来表示HTTP/1主机头。因此，如果在试图匹配主机，应该匹配`:authority`头。

- **weighted_clusters**<br />
	(sometimes required, object) 如果不是重定向路由（未指定`host_redirect`或`path_redirect`），则必须指定`cluster`，`cluster_header`或`weighted_clusters`其中之一。使用`weighted_clusters`选项，可以为该路由指定多个上游群集。根据每个集群的权重，将请求转发到其中一个上游集群。请参阅[流量拆分](../../Configurationreference/HTTPconnectionmanager/TrafficShiftingSplitting.md)以获取相应的文档。

- **host_redirect**<br />
	(sometimes required, string) 表示该路由是重定向规则。如果匹配，则会发送301重定向的响应，该响应将使用该值交换URL的主机部分。也可以使用`path_redirect`选项一起指定。

- **path_redirect**<br />
	(sometimes required, string) 表示该路由是重定向规则。如果匹配，则会发送一个301重定向的响应，用这个值交换URL的路径部分。可以与`host_redirect`选项一起指定。路由器过滤器将在重写`x-envoy-original-path`头之前放置原始路径。

- **prefix_rewrite**<br />
	(optional, string) 表示在转发过程中，此值将替换所匹配的前缀（或路径）。当使用正则表达式路径匹配时，将替换整个路径（不包括查询字符串）。此选项允许应用程序的URL与反向代理层公开的路径不同。

- **host_rewrite**<br />
	(optional, string) 表示在转发过程中，此值将替换主机头。

- **auto_host_rewrite**<br />
	(optional, boolean) 表示在转发过程中，主机头将与群集管理器选择的上游主机的主机名进行交换。此选项仅适用于路由的目标群集的类型为`strict_dns`或`logical_dns`。对于其他群集类型，设置为`true`将无效。`auto_host_rewrite`和`host_rewrite`是互斥的选项。只能指定一个。

- **case_sensitive**<br />
	(optional, boolean) 表示前缀/路径匹配是否区分大小写。默认值是true。

- **use_websocket**<br />
	(optional, boolean) 表示是否允许与此路由连接的HTTP/1.1客户端升级到WebSocket连接。默认值是false。

    注意：如果设置为true，Envoy期望与此路由匹配的第一个请求包含WebSocket升级头。如果升级头不存在，连接将作为普通的HTTP/1.1连接进行处理。如果升级头存在，Envoy将在客户端和上游服务器之间建立纯TCP代理。因此，如果要拒绝WebSocket升级请求，上游服务器将要负责关闭相关的连接。在关闭连接之前，Envoy将持续从客户端代理数据到上游服务器。

    具有WebSocket升级头的请求不支持重定向，超时和重试。

- **timeout_ms**<br />
	(optional, integer) 指定路由的超时时间。如果未指定，则默认值为15秒。请注意，此超时包括所有重试。另请参阅[x-envoy-upstream-rq-timeout-ms](../../Configurationreference/HTTPfilters/Router.md#x-envoy-upstream-rq-timeout-ms)，[x-envoy-upstream-rq-per-try-timeout-ms](../../Configurationreference/HTTPfilters/Router.md#x-envoy-upstream-rq-per-try-timeout-ms)和[重试概述](../../Introduction/Architectureoverview/HTTProuting.md)。

- **runtime**<br />
	(optional, object) 可选，指定路由的[运行时](#Runtime)配置。

- **retry_policy**<br />
	(optional, object) 可选，表示该路由具有[重试策略](#Retry-policy)。

- **shadow**<br />
	(optional, object) 可选，表示路由具有[影子策略](#Shadow)。

- **priority**<br />
	(optional, string) 可选，指定路由[优先级](../../Introduction/Architectureoverview/HTTProuting.md)。

- **headers**<br />
	(optional, array) 指定与该路由匹配的一组[头](#Headers)列表。路由器将根据配置中指定头检查请求的头部。如果路由中的所有头都与请求中头的值相同（或者配置中没有指定相应的值，则认定存在），则将发生匹配。

- **request_headers_to_add**<br />
	(optional, array) 指定应添加到由此虚拟主机处理的每个请求的HTTP头列表。以下面的形式指定头部：
    ```
    [
      {"key": "header1", "value": "value1"},
      {"key": "header2", "value": "value2"}
    ]
    ```
    有关更多信息，请参阅[自定义请求头](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)的文档。

- **opaque_config**<br />
	(optional, array) 指定可由过滤器访问的一组可选[路由配置值](#Opaque-Config)。

- **rate_limits**<br />
	(optional, array) 指定可应用与该路由的一组[速率限制配置](../../v1APIreference/HTTPRouteconfiguration/Ratelimitconfiguration.md)。

- **include_vh_rate_limits**<br />
	(optional, boolean) 指定速率限制过滤器是否应包含虚拟主机的速率限制。默认情况下，如果路由配置的速率限制，虚拟主机`rate_limits`将不适用于请求。

- **hash_policy**<br />
	(optional, object) 如果上游群集使用哈希负载平衡器，则通过此选项指定路由的[哈希策略](../../Introduction/Architectureoverview/Loadbalancing.md)。

- **decorator**<br />
	(optional, object) 指定用于增强相关匹配的路由描述信息，用于信息上报。

### Runtime
可用于路由的[运行时](../../Introduction/Architectureoverview/Runtimeconfiguration.md)配置，开展路由的逐步变更，而无需部署完整的代码/配置。请参阅[流量转移文档](../../Configurationreference/HTTPconnectionmanager/TrafficShiftingSplitting.md)。

```
{
  "key": "...",
  "default": "..."
}
```
- **key**<br />
	(required, string) 指定运行时应查询的键的名称，以确定路由是否匹配。有关键名称如何映射到底层实现，请参阅[运行时文档](../../Operationsandadministration/Runtime.md)。

- **default**<br />
	(required, integer) 一个0-100之间的整数，每当路由匹配时，会选择0-99之间的随机数。若该值<=在该键中找到的值（优先检查），或者若该键不存在，则为默认值，该路由是匹配的（假定所有的都路由匹配）。

### Retry policy
HTTP重试[架构概述](../../Introduction/Architectureoverview/HTTProuting.md)。

```
{
  "retry_on": "...",
  "num_retries": "...",
  "per_try_timeout_ms" : "..."
}
```
- **retry_on**<br />
	(required, string) 指定重试的发生条件。这些是与[x-envoy-retry-on](../../Configurationreference/HTTPfilters/Router.md#x-envoy-retry-on)和[x-envoy-retry-grpc-on](../../Configurationreference/HTTPfilters/Router.md#x-envoy-retry-grpc-on)记录的条件相同。

- **num_retries**<br />
	(optional, integer) 指定允许的重试次数。此参数是可选的，默认值为1。这些与[x-envoy-max-retries](../../Configurationreference/HTTPfilters/Router.md#x-envoy-max-retries)记录的条件相同。

- **per_try_timeout_ms**<br />
	(optional, integer) 指定每个重试尝试的非零超时。该参数是可选的。与[x-envoy-upstream-rq-per-try-timeout-ms](../../Configurationreference/HTTPfilters/Router.md#x-envoy-upstream-rq-per-try-timeout-ms)记录的条件相同。

    注意：如果未指定，Envoy将使用全局路由请求超时。因此，当使用基于`5xx`的重试策略时，超时请求将不会被重试，因为总超时预算已经耗尽。

### Shadow

路由器能够实现将流量从一个集群映射到另一个集群。目前的实现是“fire and forget”，这意味着Envoy在返回来自主集群的响应之前不会等待影子集群作出响应。所有正常的统计数据都会被收集用于阴影集群，使得该功能对测试很有用。

在shadow期间，host/authority头被改变，使得`-shadow`被附加。这对于日志记录很有用。例如：`cluster1`变为`cluster1-shadow`。

```
{
  "cluster": "...",
  "runtime_key": "..."
}
```
- **cluster**<br />
	(required, string) 指定请求将被映射到的群集。群集必须存在于[群集管理器](../../Configurationreference/Clustermanager.md)配置中。

- **runtime_key**<br />
	(optional, string) 如果未指定，则对目标群集的所有请求都将被映射。如果指定，Envoy将查找运行时键，以获取请求的百分比。有效值从0到10000，允许0.01％的增幅。如果运行时键在配置中指定，但运行时不存在，则默认为0，因此请求的0％将被映射。

### Headers
```
{
  "name": "...",
  "value": "...",
  "regex": "..."
}
```
- **name**<br />
	(required, string) 指定请求中头部的名称。

- **value**<br />
	(optional, string) 指定头的值。如果值不存在，则具有头名的请求都将匹配，而不管头的值如何。

- **regex**<br />
	(optional, boolean) 指定头的值是否采用正则表达式。默认为`false`。整个请求头的值必须与正则表达式匹配。如果只有请求头值的子序列与正则表达式匹配，则规则不匹配。这里定义了值字段中使用的[正则表达式语法](http://en.cppreference.com/w/cpp/regex/ecmascript)。

    示例：
    - 正则表达式d{3}匹配123值
    - 正则表达式d{3}不匹配1234值
    - 正则表达式d{3}不匹配123.456值

    注意：在内部，Envoy始终使用HTTP/2的`:authority`来表示HTTP/1主机头。因此，如果试图匹配主机，则匹配`:authority`替代。

    注意：若要使用HTTP的`method`进行路由，请使用特殊的HTTP/2的`:method`头。这适用于HTTP/1和HTTP/2，因为Envoy标准化头。例如：

    ```
    {
      "name": ":method",
      "value": "POST"
    }
    ```

### Weighted Clusters
与指定单个上游群集作为请求的目标群集相比，`weighted_clusters`选项允许指定多个上游群集，以及指定要转发给每个群集的流量的百分比权重。路由器将根据权重选择上游集群。

```
{
  "clusters": [],
  "runtime_key_prefix" : "..."
}
```
- **clusters**<br />
	(required, array) 指定与路由器相关的一个或多个上游群集。
    
    ```
    {
      "name" : "...",
      "weight": "..."
    }
    ```
    - **name**<br />
    	(required, string) 上游群集的名称。群集必须存在于[群集管理器](../../Configurationreference/Clustermanager.md)配置中。
    
    - **weight**<br />
    	(required, integer) 一个0-100之间的整数，当请求与路由匹配时，选择上游集群的权重比。集群组中所有权重之和必须为100。

- **runtime_key_prefix**<br />
	(optional, string) 指定运行时每个群集相关的运行时键的前缀。当指定`runtime_key_prefix`时，路由器将在键为`runtime_key_prefix + "." + cluster[i].name`下查找与每个上游集群相关的权重。其中，`cluster[i]`表示集群组中的条目。如果群集的运行时键不存在，则配置文件中指定的值将用作默认权重。有关键名称如何映射到底层实现，请参阅[运行时](../../Operationsandadministration/Runtime.md)文档。

    注意：如果运行时权重之和超过100，则流量拆分的行为将未定义（尽管请求将被路由到其中一个集群）。

### Hash policy
如果上游群集使用哈希负载平衡器，则指定路由的哈希策略。

```
{
  "header_name": "..."
}
```
- **header_name**<br />
	(required, string) 获取的请求头部的名称作为哈希值。如果请求头不存在，负载均衡器将使用一个随机数作为哈希值，从而使得有效地使负载均衡策略变为随机策略。 

### Decorator
指定路由的修饰符。

```
{
  "operation": "..."
}
```
- **operation**<br />
	(required, string) 与此路由匹配的请求相关的操作名称。如果已启用跟踪，则将使用此信息作为为此请求跟踪记录的span名称。注意：对于入口（入站）请求或出口（出站）响应，此值可能会被`x-envoy-decorator-operation`头部覆盖。

### Opaque Config
可以通过“不透明配置”机制为过滤器提供额外的配置。在路由配置中指定属性列表。该配置不能未被Envoy解释，可以在用户定义的过滤器中访问。该配置是一个通用的字符串映射。不支持嵌套对象。

```
[
  {"...": "..."}
]
```
### Cors
设置路由的优先于虚拟主机的设置。

```
{
  "enabled": false,
  "allow_origin": ["http://foo.example"],
  "allow_methods": "POST, GET, OPTIONS",
  "allow_headers": "Content-Type",
  "allow_credentials": false,
  "expose_headers": "X-Custom-Header",
  "max_age": "86400"
}
```
- **enabled**<br />
	(optional, boolean) 默认为true。只有在路由上启用并设置为false，才会禁用此路由的CORS。该设置对虚拟主机没有影响。

- **allow_origin**<br />
	(optional, array) 将允许做CORS请求的来源。通配符“*”表示允许任何来源。

- **allow_methods**<br />
	(optional, string) `access-control-allow-methods`头的内容。以逗号分隔的HTTP方法列表。

- **allow_headers**<br />
	(optional, string) `access-control-allow-headers`头的内容。以逗号分隔的HTTP标题列表。

- **allow_credentials**<br />
	(optional, boolean) 资源是否允许凭证。

- **expose_headers**<br />
	(optional, string) `access-control-expose-headers`头的内容。以逗号分隔的HTTP标题列表。

- **max_age**<br />
	(optional, string) `access-control-max-age`头的内容。以秒为单位的值，可以缓存对预检请求的响应时间。

## 返回
- [上一级](../HTTPRouteconfiguration.md)
- [首页目录](../../README.md)