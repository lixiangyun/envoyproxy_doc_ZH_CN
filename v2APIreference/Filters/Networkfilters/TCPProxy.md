## TCP代理

TCP代理[配置概述](../../../Configurationreference/Networkfilters/TCPproxy.md)。

- [filter.network.TcpProxy](#filternetworktcpproxy)
- [filter.network.TcpProxy.DeprecatedV1](#filternetworktcpproxydeprecatedv1)
- [filter.network.TcpProxy.DeprecatedV1.TCPRoute](#filternetworktcpproxydeprecatedv1tcproute)


### filter.network.TcpProxy
[filter.network.TcpProxy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/tcp_proxy.proto#L16)

```
{
  "stat_prefix": "...",
  "cluster": "...",
  "access_log": [],
  "deprecated_v1": "{...}",
  "max_connect_attempts": "{...}"
}
```

- **stat_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 发布统计信息时使用的前缀。

- **cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 要连接到的上游群集。

    注意：一旦在监听器中实现了完整的过滤器链匹配，此字段将成为配置目标集群的唯一方法。所有其他匹配将通过过[滤器链匹配](../v2APIreference/ListenersandLDS.md#filterchainmatch)规则完成。当不需要其他匹配规则时，此配置非常简单，该字段仍可用于选择群集。否则，需要配置[deprecated_v1](#deprecated_v1)才能在此期间使用更复杂的路由。

- **access_log**<br />
	([filter.accesslog.AccessLog](../../../v2APIreference/Filters/Commonaccesslogtypes.md#filteraccesslogaccessLog)) 由此TCP代理发出的访问日志的配置。

- **deprecated_v1**<br />
	([filter.network.TcpProxy.DeprecatedV1](#filternetworktcpproxydeprecatedv1)) 使用不推荐使用v1格式的TCP代理过滤器配置。这是复杂路由所必需的，直到实现了监听器中的过滤器链匹配为止。

- **max_connect_attempts**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 在放弃建连之前将尝试的最大失败连接数。如果没有指定参数，则将尝试1次连接。

### filter.network.TcpProxy.DeprecatedV1
[filter.network.TcpProxy.DeprecatedV1 proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/tcp_proxy.proto#L52)

使用v1格式的TCP代理过滤器配置，直到Envoy能够在监听器级别匹配源/目的地（称为过滤器链匹配）。

```
{
  "routes": []
}
```

- **routes**<br />
	([filter.network.TcpProxy.DeprecatedV1.TCPRoute](#filternetworktcpproxydeprecatedv1tcproute), REQUIRED) 过滤器的路由表。所有的过滤器实例都必须有一个路由表，即使它是空的。

### filter.network.TcpProxy.DeprecatedV1.TCPRoute
[filter.network.TcpProxy.DeprecatedV1.TCPRoute proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/tcp_proxy.proto#L60)

TCP代理的路由是由一组可选的标准L4和一个集群的名称组成。如果下游连接符合所有指定的条件，则路由中的集群将用于相应的上游连接。按照指定的顺序尝试路由，直到找到匹配项。如果找不到匹配，则连接关闭。并没有规定路由始终有效的并且总是产生一个匹配。

```
{
  "cluster": "...",
  "destination_ip_list": [],
  "destination_ports": "...",
  "source_ip_list": [],
  "source_ports": "..."
}
```

- **cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 下游网络连接符合匹配条件时需要连接的群集。

- **destination_ip_list**<br />
	([CidrRange](../v2APIreference/Networkaddresses.md#cidrrange)) 可选的IP地址子网列表，格式为“ip_address/xx”。如果下游连接的目标IP地址，至少被包含在一个指定的子网中，则符合匹配条件。如果未指定参数或列表为空，则将忽略目标IP地址。如果连接已被重定向，则下游连接的目标IP地址可能与代理正在监听的地址不同。

- **destination_ports**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 可选的字符串，包含通过逗号分隔的端口号列表，或者端口范围。如果下游连接的目标端口，至少一个被包含在指定范围内，则符合匹配条件。如果未指定参数，则将忽略目标端口。如果连接已被重定向，下游连接的目标端口地址可能与代理正在监听的端口不同。

- **source_ip_list**<br />
	([CidrRange](../v2APIreference/Networkaddresses.md#cidrrange)) 可选的IP地址子网列表，格式为“ip_address/xx”。如果下游连接的源IP地址，至少一个被包含在指定的子网中，则符合匹配条件。如果未指定参数或列表为空，则将忽略源IP地址。

- **source_ports**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 可选字符串，包含以逗号分隔的端口号列表，或者端口范围。如果下游连接的源端口，至少一个被包含在指定范围内，则满足匹配条件。如果未指定参数，则源端口将被忽略。

## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

