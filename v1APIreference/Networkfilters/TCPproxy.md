### TCP proxy
TCP proxy configuration overview.

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
- **route_config**</br>
	([required](#), object) The route table for the filter. All filter instances must have a route table, even if it is empty.

- **stat_prefix**</br>
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The prefix to use when emitting statistics.

- **access_log**</br>
	([optional](#), array) Configuration for access logs emitted by the this tcp_proxy.

### Route Configuration
```
{
  "routes": []
}
```
- **routes**</br>
	([required](#), array) An array of route entries that make up the route table.

### Route
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
- **cluster**</br>
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The cluster to connect to when a the downstream network connection matches the specified criteria.

- **destination_ip_list**</br>
	([optional](#), array) An optional list of IP address subnets in the form “ip_address/xx”. The criteria is satisfied if the destination IP address of the downstream connection is contained in at least one of the specified subnets. If the parameter is not specified or the list is empty, the destination IP address is ignored. The destination IP address of the downstream connection might be different from the addresses on which the proxy is listening if the connection has been redirected. Example:

```
[
  "192.168.3.0/24",
  "50.1.2.3/32",
  "10.15.0.0/16",
  "2001:abcd::/64"
]
```


- **destination_ports**</br>
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) An optional string containing a comma-separated list of port numbers or ranges. The criteria is satisfied if the destination port of the downstream connection is contained in at least one of the specified ranges. If the parameter is not specified, the destination port is ignored. The destination port address of the downstream connection might be different from the port on which the proxy is listening if the connection has been redirected. Example:

```
{
  "destination_ports": "1-1024,2048-4096,12345"
}
```
- **source_ip_list**</br>
	([optional](#), array) An optional list of IP address subnets in the form “ip_address/xx”. The criteria is satisfied if the source IP address of the downstream connection is contained in at least one of the specified subnets. If the parameter is not specified or the list is empty, the source IP address is ignored. Example:



```
[
  "192.168.3.0/24",
  "50.1.2.3/32",
  "10.15.0.0/16",
  "2001:abcd::/64"
]
```



- **source_ports**</br>
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) An optional string containing a comma-separated list of port numbers or ranges. The criteria is satisfied if the source port of the downstream connection is contained in at least one of the specified ranges. If the parameter is not specified, the source port is ignored. Example:

```
{
  "source_ports": "1-1024,2048-4096,12345"
}
```


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

