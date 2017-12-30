## 集群&集群发现

- [Cluster](#cluster)
- [Cluster.EdsClusterConfig](#clusteredsclusterconfig)
- [Cluster.OutlierDetection](#clusteroutlierdetection)
- [Cluster.LbSubsetConfig](#clusterlbsubsetconfig)
- [Cluster.LbSubsetConfig.LbSubsetSelector](#clusterlbsubsetconfiglbsubsetselector)
- [Cluster.LbSubsetConfig.LbSubsetFallbackPolicy (Enum)](#clusterlbsubsetconfiglbsubsetfallbackpolicy-enum)
- [Cluster.RingHashLbConfig](#clusterringhashlbconfig)
- [Cluster.RingHashLbConfig.DeprecatedV](#clusterringhashlbconfigdeprecatedv)
- [Cluster.DiscoveryType (Enum)](#clusterdiscoverytype-enum)
- [Cluster.LbPolicy (Enum)](#clusterlbpolicy-enum)
- [Cluster.DnsLookupFamily (Enum)](#clusterdnslookupfamily-enum)
- [UpstreamBindConfig](#upstreambindconfig)
- [CircuitBreakers](#circuitbreakers)
- [CircuitBreakers.Thresholds](#circuitbreakersthresholds)

### Cluster

[Cluster proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L33)

```
{
  "name": "...",
  "type": "...",
  "eds_cluster_config": "{...}",
  "connect_timeout": "{...}",
  "per_connection_buffer_limit_bytes": "{...}",
  "lb_policy": "...",
  "hosts": [],
  "health_checks": [],
  "max_requests_per_connection": "{...}",
  "circuit_breakers": "{...}",
  "tls_context": "{...}",
  "http_protocol_options": "{...}",
  "http2_protocol_options": "{...}",
  "dns_refresh_rate": "{...}",
  "dns_lookup_family": "...",
  "dns_resolvers": [],
  "outlier_detection": "{...}",
  "cleanup_interval": "{...}",
  "upstream_bind_config": "{...}",
  "lb_subset_config": "{...}",
  "ring_hash_lb_config": "{...}",
  "transport_socket": "{...}"
}
```
- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 提供在所有群集中必须唯一的群集名称。在发布统计信息时，会使用集群名称。在发布任何统计信息时，集群名称将被转换为`_`。默认情况下，群集名称的最大长度限制为60个字符。可通过`--max-obj-name-len`命令行参数，提高此上限。

- **type**<br />
	([Cluster.DiscoveryType](#clusterdiscoverytype-enum)))  用于解析群集的服务发现类型。

- **eds_cluster_config**<br />
	([Cluster.EdsClusterConfig](#clusteredsclusterconfig)) 用于群集的EDS更新配置。

- **connect_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 连接到该群集中主机的超时时长。

- **per_connection_buffer_limit_bytes**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 连接集群的读写缓冲区大小。如果未指定，则使用默认值（1MB）。

- **lb_policy**<br />
	([Cluster.LbPolicy](#clusterlbpolicy-enum))) 在集群中选择主机时使用的负载平衡器类型。

- **hosts**<br />
	([Address](../v2APIreference/Networkaddresses.md)) 如果服务发现类型是`STATIC`，`STRICT_DNS`或`LOGICAL_DNS`，则需要配置。

- **health_checks**<br />
	([HealthCheck](../v2APIreference/Healthcheck.md)) 群集可选的健康检查配置。如果未指定配置，则不会执行健康检查，并且假定所有群集成员都将始终处于健康状态。

- **max_requests_per_connection**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 可选，单个上游连接的最大请求数。HTTP/1.1和HTTP/2连接池都遵守此参数。如果没有指定，则没有限制。若此参数设置为1将有效地禁用保活状态的连接。

- **circuit_breakers**<br />
	([CircuitBreakers](#circuitbreakers)) 可选，集群熔断配置。

- **tls_context**<br />
	([UpstreamTlsContext](../v2APIreference/CommonTLSconfiguration.md)) 连接到上游群集的TLS配置。如果没有指定TLS配置，则新连接不会使用TLS。

- **http_protocol_options**<br />
	([Http1ProtocolOptions](../v2APIreference/Protocoloptions.md)) 处理HTTP1请求时的其他选项。
	
    只能设置`http_protocol_options`和`http2_protocol_options`其中一个配置。

- **http2_protocol_options**<br />
	([Http2ProtocolOptions](../v2APIreference/Protocoloptions.md)) 即使需要默认的HTTP2协议选项，也必须设置此字段，以便Envoy将在创建新的HTTP连接池时，假定上游支持HTTP/2。目前，Envoy仅支持上游连接的先验证。即使TLS与ALPN一起使用，也必须指定`http2_protocol_options`。另外，这允许HTTP/2通过纯文本连接。
	
    只能设置`http_protocol_options`和`http2_protocol_options`其中一个配置。

- **dns_refresh_rate**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 指定DNS刷新率，在群集类型为`STRICT_DNS`或`LOGICAL_DNS`时，则将此值用作群集的DNS刷新率。如果未指定此设置，则此值默认为5000。对于`STRICT_DNS`和`LOGICAL_DNS`以外的群集类型，此设置将被忽略。

- **dns_lookup_family**<br />
	([Cluster.DnsLookupFamily](#clusterdnslookupfamily-enum))) DNS IP地址解析策略。 如果未指定此设置，则该值默认为`V4_ONLY`。

- **dns_resolvers**<br />
	([Address](../v2APIreference/Networkaddresses.md)) 如果指定了DNS解析程序，并且群集类型是`STRICT_DNS`或`LOGICAL_DNS`，则此值用于指定群集的dns解析程序。如果未指定此设置，则该值默认为使用`/etc/resolv.conf`配置的默认解析器。对于`STRICT_DNS`和`LOGICAL_DNS`以外的其他集群类型，此设置将被忽略。

- **outlier_detection**<br />
	([Cluster.OutlierDetection](#clusteroutlierdetection)) 如果指定，则会为此上游群集启用异常值检测。每个配置值都可以通过运行时配置覆盖。

- **cleanup_interval**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 从集群类型`ORIGINAL_DST`中删除过期主机的时间间隔。如果主机在这段时间内没有被用作上游目的地，则认为它们是陈旧的。随着新的连接重定向到Envoy，新的主机将按需添加到原始目标集群，从而导致集群中的主机数量随着时间而增长。没有陈旧的主机（它们被主动用作目的地）被保存在群集中，从而允许与它们的连接保持打开状态，从而节省了打开新连接所花费的等待时间。如果未指定此设置，则该值默认为5000毫秒。对于`ORIGINAL_DST`以外的其他群集类型，此设置将被忽略。

- **upstream_bind_config**<br />
	([BindConfig](../v2APIreference/Networkaddresses.md#BindConfig)) 用于绑定新建立的上游连接的可选配置。这将覆盖`bootstrap proto`中指定的任何`bind_config`。如果地址和端口是空的，则不执行绑定。

- **lb_subset_config**<br />
	([Cluster.LbSubsetConfig](#clusterlbsubsetconfig)) 配置负载平衡子集。

- **ring_hash_lb_config**<br />
	([Cluster.RingHashLbConfig](#clusterringhashlbconfig)) 可选，配置环哈希负载平衡策略。只能设置一个`ring_hash_lb_config`。

- **transport_socket**<br />
	([TransportSocket](../v2APIreference/Commontypes.md#TransportSocket)) 请参阅    [base.TransportSocket](../v2APIreference/Commontypes.md#TransportSocket)描述。


### Cluster.EdsClusterConfig
[Cluster.EdsClusterConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L74)

只有当集群发现类型是EDS时才有效。

```
{
  "eds_config": "{...}",
  "service_name": "..."
}
```
- **eds_config**<br />
	([ConfigSource](../v2APIreference/Commontypes.md#ConfigSource)) 此群集的EDS更新源的配置。

- **service_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 可选，替代集群的名称，提供给EDS服务。这与集群名称没有同样的限制，即它可以是任意的长度。

### Cluster.OutlierDetection
[Cluster.OutlierDetection proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L217)

有关异常值检测的更多信息，请参阅[架构概述](../Introduction/Architectureoverview/Outlierdetection.md)。

```
{
  "consecutive_5xx": "{...}",
  "interval": "{...}",
  "base_ejection_time": "{...}",
  "max_ejection_percent": "{...}",
  "enforcing_consecutive_5xx": "{...}",
  "enforcing_success_rate": "{...}",
  "success_rate_minimum_hosts": "{...}",
  "success_rate_request_volume": "{...}",
  "success_rate_stdev_factor": "{...}",
  "consecutive_gateway_failure": "{...}",
  "enforcing_consecutive_gateway_failure": "{...}"
}
```
- **consecutive_5xx**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 发生连续5xx逐出主机之前，连续5xx响应的数量。默认为5。

- **interval**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 每次异常值分析扫描的时间间隔，这可能导致新抛出异常以及主机被重新添加到服务集群。默认为10000ms或10s。

- **base_ejection_time**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 主机弹出的基准时间。实际时间等于基本时间乘以主机被逐出的次数。默认为30000ms或30s。

- **max_ejection_percent**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 由于异常检测而逐出的主机占上游群集的最大百分比。默认为10％。

- **enforcing_consecutive_5xx**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 当通过连续5xx检测到异常状态时，主机实际被逐出的几率百分比。这个设置可以用来禁止逐出或者缓慢地加速。默认为100。

- **enforcing_success_rate**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 通过成功率统计检测到异常状态时，主机实际被逐出的几率百分比。这个设置可以用来禁止逐出或者缓慢地加速。默认为100。

- **success_rate_minimum_hosts**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 必须具有足够的请求量来检测成功率异常值的群集中的主机数量。如果主机数量小于此设置，则不会为群集中的任何主机执行通过成功率统计信息的异常值检测。默认为5。

- **success_rate_request_volume**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 在一个时间间隔内（如上述定义的时间间隔）必须收集的最小请求总数，以便将此主机包含在基于成功率的异常值检测中。如果低于此设置，则不会为该主机执行通过成功率统计的异常值检测。默认为100。

- **success_rate_stdev_factor**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 这个因子被用来确定异常逐出成功率的阈值。逐出阈值是平均成功率与该因子与平均成功率的标准偏差的乘积之差：`mean - （stdev * success_rate_stdev_factor）`。 这个因素除以一千得到一个两位小数值。也就是说，如果期望的因子是1.9，运行时间值应该是1900，默认为1900。

- **consecutive_gateway_failure**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 逐出之前连续发生的连续网关故障数量，包括（502,503,504状态或连接错误，映射到其中一个状态代码）默认为5。

- **enforcing_consecutive_gateway_failure**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 当通过连续的网关故障检测到异常状态时，主机实际被逐出的几率百分比。这个设置可以用来禁止逐出或者缓慢地加速。默认为0。

### Cluster.LbSubsetConfig
[Cluster.LbSubsetConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L307)

（可选）将此群集中的端口划分为由端口元数据定义的子集，并按路由和加权群集元数据进行选择。

```
{
  "fallback_policy": "...",
  "default_subset": "{...}",
  "subset_selectors": []
}
```
- **fallback_policy**<br />
	([Cluster.LbSubsetConfig.LbSubsetFallbackPolicy](#clusterlbsubsetconfiglbsubsetfallbackpolicy)) 选定路由的元数据没有响应的端口子集匹配时使用的行为。该值默认为[NO_FALLBACK](#NO_FALLBACK)。

- **default_subset**<br />
	([Struct](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#struct)) 如果`fallback_policy`为`DEFAULT_SUBSET`，则指定在回退期间使用的端点的默认子集。将`default_subset`中的每个字段与`envoy.lb`命名空间下的匹配`LbEndpoint.Metadata进行比较。没有主机匹配是有效的，在这种情况下，行为与`NO_FALLBACK`的`fallback_policy`相同。

- **subset_selectors**<br />
	([Cluster.LbSubsetConfig.LbSubsetSelector](#clusterlbsubsetconfiglbsubsetselector)) 对于每个条目，遍历`LbEndpoint.Metadata`的`envoy.lb`命名空间，并为每个唯一的key和value组合创建一个子集。例如：

    ```
{ "subset_selectors": [
    { "keys": [ "version" ] },
    { "keys": [ "stage", "hardware_type" ] }
]}
    ```
    当来自所选路由和加权群集的元数据包含与子集的元数据相同的key和value时，匹配子集。相同的主机可能出现在多个子集中。
    

### Cluster.LbSubsetConfig.LbSubsetSelector
[Cluster.LbSubsetConfig.LbSubsetSelector proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L336)

子集的规格

```
{
  "keys": []
}
```

- **keys**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 与加权的群集元数据匹配的key列表。

### Cluster.LbSubsetConfig.LbSubsetFallbackPolicy (Enum)
[Cluster.LbSubsetConfig.LbSubsetFallbackPolicy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L314)

如果选择`NO_FALLBACK`，则会报告等效于没有健康主机的结果。如果选择了`ANY_ENDPOINT`，则可能会返回任何群集端点（取决于策略，健康检查等）。如果选择`DEFAULT_SUBSET`，则在匹配来自`default_subset`字段的值的端口上执行负载平衡。

- **NO_FALLBACK**<br />
	(DEFAULT)
- **ANY_ENDPOINT**
- **DEFAULT_SUBSET**

### Cluster.RingHashLbConfig
[Cluster.RingHashLbConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L363)

[RingHash](../Introduction/Architectureoverview/Loadbalancing.md)负载均衡策略的具体配置。

```
{
  "minimum_ring_size": "{...}",
  "deprecated_v1": "{...}"
}
```
- **minimum_ring_size**<br />
	([UInt64Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint64value)) 最小哈希环大小，即总的虚拟节点。更大的尺寸将提供更好的请求分布，因为集群中的每个主机将具有更多的虚拟节点。默认为1024.在主机总数大于最小值的情况下，每个主机将被分配一个虚拟节点。

- **deprecated_v1**<br />
	([Cluster.RingHashLbConfig.DeprecatedV1](#clusterringhashlbconfigdeprecatedv1)) 已弃用的v1配置。

### Cluster.RingHashLbConfig.DeprecatedV1
[Cluster.RingHashLbConfig.DeprecatedV1 proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L371)

```
{
  "use_std_hash": "{...}"
}
```
- **use_std_hash**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 默认为`true`，这意味着`std::hash`用于将主机散列到`ketama`环上。`std::hash`可能因平台而异。为此，Envoy默认最终会使用xxHash。该字段用于迁移目的，最终将被弃用。现在将其设置为`false`以使用`xxHash`。

### Cluster.DiscoveryType (Enum)
[Cluster.DiscoveryType proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L45)

有关每种类型的解释，请参阅[服务发现类型](../Introduction/Architectureoverview/Servicediscovery.md)。

- **STATIC**<br />
	(DEFAULT) 静态发现类型

- **STRICT_DNS**<br />
    严格的DNS发现类型

- **LOGICAL_DNS**<br />
    逻辑DNS发现类型

- **EDS**<br />
    服务发现类型

- **ORIGINAL_DST**<br />
    原始目标发现类型

### Cluster.LbPolicy (Enum)
[Cluster.LbPolicy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L95)

有关每种类型的信息，请参阅负载平衡器体系[架构概述](../Introduction/Architectureoverview/Loadbalancing.md)部分。

- **ROUND_ROBIN**<br />
	(DEFAULT) 轮循负载平衡策略

- **LEAST_REQUEST**<br />
    最小请求负载平衡策略

- **RING_HASH**<br />
    环形散列负载平衡策略

- **RANDOM**<br />
    随机负载平衡策略

- **ORIGINAL_DST_LB**<br />
    原始目标负载平衡策略


### Cluster.DnsLookupFamily (Enum)
[Cluster.DnsLookupFamily proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L192)

当选择V4_ONLY时，DNS解析器将仅执行IPv4系列中的地址查找。如果选择V6_ONLY，则DNS解析程序将仅执行IPv6系列中的地址查找。如果指定了AUTO，则DNS解析器将首先执行IPv6系列中的地址查找，然后回退到IPv4系列中的地址查找。对于`STRICT_DNS`和`LOGICAL_DNS`以外的集群类型，此设置将被忽略。

- **AUTO**<br />
	(DEFAULT) 
- **V4_ONLY**<br />
- **V6_ONLY**<br />

### UpstreamBindConfig
[UpstreamBindConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L410)

包含Envoy的可扩展地址结构，在与上游建立连接时绑定。

```
{
  "source_address": "{...}"
}
```
- **source_address**<br />
	([Address](../v2APIreference/Networkaddresses.md)) 建立上游连接时，Envoy应该绑定的地址。

### CircuitBreakers
[CircuitBreakers proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L417)

可以为每个定义的优先级单独指定断路设置。

```
{
  "thresholds": []
}
```
- **thresholds**<br />
	([CircuitBreakers.Thresholds](#circuitbreakersthresholds)) 如果使用相同的`RoutingPriority`定义多个阈值，则使用列表中的第一个阈值。如果给定的`RoutingPriority`没有定义`Thresholds`，则使用默认值。

### CircuitBreakers.Thresholds
[CircuitBreakers.Thresholds proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto#L421)

为RoutingPriority定义断路阈值设置。

```
{
  "priority": "...",
  "max_connections": "{...}",
  "max_pending_requests": "{...}",
  "max_requests": "{...}",
  "max_retries": "{...}"
}
```
- **priority**<br />
	([RoutingPriority](../v2APIreference/Commontypes.md#RoutingPriority)) 设置指定断路器的`RoutingPriority`。

- **max_connections**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Envoy将对上游群集进行的最大连接数。如果未指定，则默认值为1024。

- **max_pending_requests**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Envoy将允许上游集群的最大待处理请求数。如果未指定，则默认值为1024。

- **max_requests**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Envoy将对上游群集执行的最大并行请求数。如果未指定，则默认值为1024。

- **max_retries**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Envoy允许上游集群执行的最大并行重试次数。如果未指定，则默认值为3。



## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)