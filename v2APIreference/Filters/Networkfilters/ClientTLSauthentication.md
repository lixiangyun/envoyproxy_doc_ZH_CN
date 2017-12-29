## TLS客户端身份认证

- [filter.network.ClientSSLAuth](#filternetworkclientsslauth)


### Client TLS authentication
Client TLS authentication configuration overview.

- **filter.network.ClientSSLAuth**<br />
[filter.network.ClientSSLAuth proto]()

```
{
  "auth_api_cluster": "...",
  "stat_prefix": "...",
  "refresh_delay": "{...}",
  "ip_white_list": []
}
```
- **auth_api_cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The cluster manager cluster that runs the authentication service. The filter will connect to the service every 60s to fetch the list of principals. The service must support the expected REST API.

- **stat_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The prefix to use when emitting statistics.

- **refresh_delay**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Time in milliseconds between principal refreshes from the authentication service. Default is 60000 (60s). The actual fetch time will be this value plus a random jittered value between 0-refresh_delay_ms milliseconds.

- **ip_white_list**<br />
	([CidrRange](#)) An optional list of IP address and subnet masks that should be white listed for access by the filter. If no list is provided, there is no IP white list.



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

