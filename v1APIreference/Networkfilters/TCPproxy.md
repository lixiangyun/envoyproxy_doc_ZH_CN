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
TCP代理的路由是由一组可选的标准L4和一个集群的名称组成。如果下游连接符合所有指定的条件，则路由中的集群将用于相应的上游连接。按照指定的顺序进行路由尝试，直到找到匹配的路由。如果找不到匹配，则连接关闭。并不是始终有相应的路由，以及对应的匹配。

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
	(required, string) 符合指定条件时要连接的下游群集。

- **destination_ip_list**<br />
	(optional, array) 可选，包含IP地址子网的列表，格式为“ip_address/xx”。如果下游连接的目标IP地址包含在至少一个指定的子网中，则条件匹配。如果未指定参数或列表为空，则将忽略目标IP地址。如果连接已被重定向，则下游连接的目标IP地址可能与代理正在监听的地址不同。例：

    ```
    [
      "192.168.3.0/24",
      "50.1.2.3/32",
      "10.15.0.0/16",
      "2001:abcd::/64"
    ]
    ```


- **destination_ports**<br />
	(optional, string) 包含`.`分隔符的端口号或端口范围列表。如果下游连接的目标端口至少包含在一个指定范围内，则条件匹配。如果未指定参数，则将忽略目标端口。如果连接已被重定向，下游连接的目标端口地址可能与代理正在监听的端口不同。例：

    ```
    {
      "destination_ports": "1-1024,2048-4096,12345"
    }
    ```

- **source_ip_list**<br />
	(optional, array) 可选，包含IP地址子网的列表，格式为“ip_address/xx”。如果下游连接的源IP地址包含在至少一个指定的子网中，则条件匹配。如果未指定参数或列表为空，则将忽略源IP地址。例：

    ```
    [
      "192.168.3.0/24",
      "50.1.2.3/32",
      "10.15.0.0/16",
      "2001:abcd::/64"
    ]
    ```
- **source_ports**<br />
	(optional, string) 可选，包含`.`分隔的端口号或端口范围列表。如果下游连接的源端口包含在至少一个指定范围内，则条件匹配。如果未指定参数，则源端口将被忽略。例：
    
    ```
    {
      "source_ports": "1-1024,2048-4096,12345"
    }
    ```


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

