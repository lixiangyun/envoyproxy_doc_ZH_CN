## TCP代理

### TCP Proxy
TCP Proxy configuration overview.

- **filter.network.TcpProxy**<br />
[filter.network.TcpProxy proto]()

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
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The prefix to use when emitting statistics.

- **cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The upstream cluster to connect to.


### Note

Once full filter chain matching is implemented in listeners, this field will become the only way to configure the target cluster. All other matching will be done via filter chain matching rules. For very simple configurations, this field can still be used to select the cluster when no other matching rules are required. Otherwise, a deprecated_v1 configuration is required to use more complex routing in the interim.

- **access_log**<br />
	([filter.accesslog.AccessLog](#)) Configuration for access logs emitted by the this tcp_proxy.

- **deprecated_v1**<br />
	([filter.network.TcpProxy.DeprecatedV1](#)) TCP Proxy filter configuration using deprecated V1 format. This is required for complex routing until filter chain matching in the listener is implemented.

- **max_connect_attempts**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The maximum number of unsuccessful connection attempts that will be made before giving up. If the parameter is not specified, 1 connection attempt will be made.

- **filter.network.TcpProxy.DeprecatedV1**<br />
[filter.network.TcpProxy.DeprecatedV1 proto]()

TCP Proxy filter configuration using V1 format, until Envoy gets the ability to match source/destination at the listener level (called filter chain match).

```
{
  "routes": []
}
```
- **routes**<br />
	([filter.network.TcpProxy.DeprecatedV1.TCPRoute](#), REQUIRED) The route table for the filter. All filter instances must have a route table, even if it is empty.

- **filter.network.TcpProxy.DeprecatedV1.TCPRoute**<br />
[filter.network.TcpProxy.DeprecatedV1.TCPRoute proto]()

A TCP proxy route consists of a set of optional L4 criteria and the name of a cluster. If a downstream connection matches all the specified criteria, the cluster in the route is used for the corresponding upstream connection. Routes are tried in the order specified until a match is found. If no match is found, the connection is closed. A route with no criteria is valid and always produces a match.

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
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The cluster to connect to when a the downstream network connection matches the specified criteria.

- **destination_ip_list**<br />
	([CidrRange](#)) An optional list of IP address subnets in the form “ip_address/xx”. The criteria is satisfied if the destination IP address of the downstream connection is contained in at least one of the specified subnets. If the parameter is not specified or the list is empty, the destination IP address is ignored. The destination IP address of the downstream connection might be different from the addresses on which the proxy is listening if the connection has been redirected.

- **destination_ports**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) An optional string containing a comma-separated list of port numbers or ranges. The criteria is satisfied if the destination port of the downstream connection is contained in at least one of the specified ranges. If the parameter is not specified, the destination port is ignored. The destination port address of the downstream connection might be different from the port on which the proxy is listening if the connection has been redirected.

- **source_ip_list**<br />
	([CidrRange](#)) An optional list of IP address subnets in the form “ip_address/xx”. The criteria is satisfied if the source IP address of the downstream connection is contained in at least one of the specified subnets. If the parameter is not specified or the list is empty, the source IP address is ignored.

- **source_ports**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) An optional string containing a comma-separated list of port numbers or ranges. The criteria is satisfied if the source port of the downstream connection is contained in at least one of the specified ranges. If the parameter is not specified, the source port is ignored.





## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

