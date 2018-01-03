### TCP代理
TCP代理[配置参考](../../Configurationreference/Networkfilters/TCPproxy.md)

```
{
  "name": "tcp_proxy",
  "config": {
    "stat_prefix": "...",
    "route_config": "{...}",
    "access_log": []
  }
}
```
- **route_config**<br />
	(required, object) 过滤器的路由表。所有的过滤器实例都必须有一个路由表，即使它是空的。

- **stat_prefix**<br />
	(required, string) 发布统计信息时使用的前缀。

- **access_log**<br />
	(optional, array) 由此`tcp_proxy`发出的访问日志配置。

### Route Configuration
```
{
  "routes": []
}
```
- **routes**<br />
	(required, array) 组成路由表的一组路由条目。

### Route
TCP代理路由由一组可选的L4标准和一个集群的名称组成。如果下游连接符合所有指定的条件，则路由中的集群将用于相应的上游连接。按照指定的顺序尝试路由，直到找到匹配项。如果找不到匹配，则连接关闭。没有标准的路线是有效的，并且总是产生一个匹配。

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
	(required, string) The cluster to connect to when a the downstream network connection matches the specified criteria.

- **destination_ip_list**<br />
	(optional, array) An optional list of IP address subnets in the form “ip_address/xx”. The criteria is satisfied if the destination IP address of the downstream connection is contained in at least one of the specified subnets. If the parameter is not specified or the list is empty, the destination IP address is ignored. The destination IP address of the downstream connection might be different from the addresses on which the proxy is listening if the connection has been redirected. Example:

```
[
  "192.168.3.0/24",
  "50.1.2.3/32",
  "10.15.0.0/16",
  "2001:abcd::/64"
]
```


- **destination_ports**<br />
	(optional, string) An optional string containing a comma-separated list of port numbers or ranges. The criteria is satisfied if the destination port of the downstream connection is contained in at least one of the specified ranges. If the parameter is not specified, the destination port is ignored. The destination port address of the downstream connection might be different from the port on which the proxy is listening if the connection has been redirected. Example:

```
{
  "destination_ports": "1-1024,2048-4096,12345"
}
```
- **source_ip_list**<br />
	(optional, array) An optional list of IP address subnets in the form “ip_address/xx”. The criteria is satisfied if the source IP address of the downstream connection is contained in at least one of the specified subnets. If the parameter is not specified or the list is empty, the source IP address is ignored. Example:



```
[
  "192.168.3.0/24",
  "50.1.2.3/32",
  "10.15.0.0/16",
  "2001:abcd::/64"
]
```



- **source_ports**<br />
	(optional, string) An optional string containing a comma-separated list of port numbers or ranges. The criteria is satisfied if the source port of the downstream connection is contained in at least one of the specified ranges. If the parameter is not specified, the source port is ignored. Example:

```
{
  "source_ports": "1-1024,2048-4096,12345"
}
```


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

