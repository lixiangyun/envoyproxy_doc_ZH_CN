## TLS客户端身份认证

- [filter.network.ClientSSLAuth](#filternetworkclientsslauth)

TLS客户端身份认证[配置概述](../../../Configurationreference/Networkfilters/ClientTLSauthentication.md)

### filter.network.ClientSSLAuth
[filter.network.ClientSSLAuth proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/client_ssl_auth.proto#L13)

```
{
  "auth_api_cluster": "...",
  "stat_prefix": "...",
  "refresh_delay": "{...}",
  "ip_white_list": []
}
```

- **auth_api_cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 运行身份验证服务的[群集管理器](../../../Introduction/Architectureoverview/Clustermanager.md)群集。过滤器将每60秒连接到该服务以获取主体列表。该服务必须支持期望的[REST API](../../../Configurationreference/Networkfilters/ClientTLSauthentication.md)。

- **stat_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 发布[统计](../../../Configurationreference/Networkfilters/ClientTLSauthentication.md)信息时所使用的前缀。

- **refresh_delay**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 刷新验证服务主体时间间隔（以毫秒为单位）。默认是60000（60s）。实际的刷新时间是这个值加上0到`refresh_delay_ms`之间的随机抖动值。

- **ip_white_list**<br />
	([CidrRange](../../../v2APIreference/Networkaddresses.md#cidrrange)) 一个可选的IP地址和子网掩码列表，提供白名单列表给供过器使用。如果没有提供列表，则没有IP白名单。



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

