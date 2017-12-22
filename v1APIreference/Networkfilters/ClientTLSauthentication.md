### Client TLS authentication
Client TLS authentication configuration overview.

```
{
  "name": "client_ssl_auth",
  "config": {
    "auth_api_cluster": "...",
    "stat_prefix": "...",
    "refresh_delay_ms": "...",
    "ip_white_list": []
  }
}
```
- **auth_api_cluster**</br>
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The cluster manager cluster that runs the authentication service. The filter will connect to the service every 60s to fetch the list of principals. The service must support the expected REST API.

- **stat_prefix**</br>
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The prefix to use when emitting statistics.

- **refresh_delay_ms**</br>
	([optional](#), integer) Time in milliseconds between principal refreshes from the authentication service. Default is 60000 (60s). The actual fetch time will be this value plus a random jittered value between 0-refresh_delay_ms milliseconds.

- **ip_white_list**</br>
	([optional](#), array) An optional list of IP address and subnet masks that should be white listed for access by the filter. If no list is provided, there is no IP white list. The list is specified as in the following example:


```
[
  "192.168.3.0/24",
  "50.1.2.3/32",
  "10.15.0.0/16",
  "2001:abcd::/64"
]
```



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

