### TLS客户端身份认证

TLS客户端身份认证[配置参考](../../Configurationreference/Networkfilters/ClientTLSauthentication.md)。

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

- **auth_api_cluster**<br />
	(required, string) 所运行的身份验证服务的群集名称。过滤器将每60秒连接到该服务以获取身份信息。该服务必须支持预期的[REST API](../../Configurationreference/Networkfilters/ClientTLSauthentication.md)。

- **stat_prefix**<br />
	(required, string) 发布统计信息时使用的前缀。

- **refresh_delay_ms**<br />
	(optional, integer) 验证服务的身份信息刷新间隔（以毫秒为单位）。默认是60000（60s）。实际的提取时间将是这个值加0到`refresh_delay_ms`毫秒之间的随机抖动值。

- **ip_white_list**<br />
	(optional, array) 一个可选的IP地址和子网掩码列表，提供白名单列表给过滤器使用。如果没有提供列表，则没有IP白名单。该列表如下例所示：

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

