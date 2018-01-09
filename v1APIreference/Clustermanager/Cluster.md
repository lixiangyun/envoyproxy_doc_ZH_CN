## 集群

```
{
  "name": "...",
  "type": "...",
  "connect_timeout_ms": "...",
  "per_connection_buffer_limit_bytes": "...",
  "lb_type": "...",
  "ring_hash_lb_config": "{...}",
  "hosts": [],
  "service_name": "...",
  "health_check": "{...}",
  "max_requests_per_connection": "...",
  "circuit_breakers": "{...}",
  "ssl_context": "{...}",
  "features": "...",
  "http2_settings": "{...}",
  "cleanup_interval_ms": "...",
  "dns_refresh_rate_ms": "...",
  "dns_lookup_family": "...",
  "dns_resolvers": [],
  "outlier_detection": "{...}"
}
```

- **name**<br />
	(required, string) 群集名称，所提供群集名称在所有群集中必须唯一。发布统计信息时会使用集群名称。默认情况下，群集名称的最大长度限制为60个字符。可通过`--max-obj-name-len`命令行参数设置为所需的值，以提高此限制。

- **type**<br />
	(required, string) 用于解析集群的[服务发现类型](../../Introduction/Architectureoverview/Servicediscovery.md)。可能的选项有`static`，`strict_dns`，`logical_dns`，`original_dst`和`sds`。

- **connect_timeout_ms**<br />
	(required, integer) 指定群集中主机网络连接超时时间，以毫秒为单位。

- **per_connection_buffer_limit_bytes**<br />
	(optional, integer) 集群连接的读写缓冲区大小限制。如果未指定，则默认值（1MB）。

- **lb_type**<br />
	(required, string) 在集群中选择主机时使用的[负载均衡器类型](../../Introduction/Architectureoverview/Loadbalancing.md)。可能的选项有`round_robin`，`least_request`，`ring_hash`，`random`和`original_dst_lb`。请注意，`original_dst_lb`必须与`original_dst`类型的集群一起使用，并且不能与其他任集群类型一起使用。

- **ring_hash_lb_config**<br />
	(optional, object) 可选的[环哈希负载均衡器](Cluster/Ringhashloadbalancerconfiguration.md)配置，当`lb_type`设置为`ring_hash`时使用。

- **hosts**<br />
	(sometimes required, array) 如果服务发现类型是`static`，`strict_dns`或`logical_dns`，则主机数组是必需的。主机数组不支持`original_dst`集群类型。指定方式由服务发现类型决于：

    - **static**<br />
    静态集群，必须使用完整解析主机，不需要DNS查找。支持TCP和unix域套接字（UDS）地址。

        一个TCP地址如下所示：<br />
        ```
        tcp://<ip>:<port>
        ```

        一个UDS地址如下所示：<br />
        ```
        unix://<file name>
        ```
        
        以下示例指定地址列表：<br />
        ```
        [{"url": "tcp://10.0.0.2:1234"}, {"url": "tcp://10.0.0.3:5678"}]
        ```
    
    - **strict_dns**<br />
    严格DNS群集类型，可以指定任意数量的主机名、端口组合。将使用DNS解析所有名称，并组合在一起形成最终的群集。如果相同的名字返回多个记录，则所有将被使用。例如：

        ```
        [{"url": "tcp://foo1.bar.com:1234"}, {"url": "tcp://foo2.bar.com:5678"}]
        ```
        
    - **logical_dns**<br />
    逻辑DNS群集类型，指定主机名称方式与严格DNS非常类似，但只有第一个主机将被使用。例如：

        ```
        [{"url": "tcp://foo1.bar.com:1234"}]
        ```

- **service_name**<br />
	(sometimes required, string) 如果服务发现类型是[sds](../Clustermanager/Servicediscoveryservice.md)，则此参数是必需的。当获取集群成员时，它将被传递给SDS API。

- **health_check**<br />
	(optional, object) 可选的群集主动[健康检查配置](Cluster/Healthchecking.md)。如果未指定配置，则不会执行健康检查，并且所有群集成员都将始终处于健康状态。

- **max_requests_per_connection**<br />
	(optional, integer) 可选的单个上游连接的最大请求数。HTTP/1.1和HTTP/2连接池，都遵循此参数。如果没有指定，则没有限制。将此参数设置为1，将有效地禁用保活状态的请求。

- **circuit_breakers**<br />
	(optional, object) 可选的群集[熔断配置](Cluster/Circuitbreakers.md)。

- **ssl_context**<br />
	(optional, object) 上游群集的[TLS配置](Cluster/TLScontext.md)。如果没有指定TLS配置，则新的连接将不会使用TLS。

- **features**<br />
	(optional, string) 上游群集支持的功能，以逗号分隔的字符串列表。目前支持的功能为：

    - **http2**<br />
    如果指定了http2，Envoy将假定在建立新的HTTP连接时，支持HTTP/2。目前，Envoy仅支持上游连接的预知。即使TLS与ALPN一起使用，也必须指定http2。另外，这允许通过纯文本建立HTTP/2连接。

- **http2_settings**<br />
	(optional, object) 在启动HTTP连接池时，直接传递给HTTP/2编解码器的相关设置。这些与HTTP连接管理器[http2_settings](../../v1APIreference/Networkfilters/HTTPconnectionmanager.md)选项相同。

- **cleanup_interval_ms**<br />
	(optional, integer) 从`original_dst`集群类型中删除旧主机的时间间隔。如果主机在这段时间内，没有被用作上游目的地址，则认为它们是旧的。随着重定向到Envoy的新连接，新的主机被按需添加到原始目标集群中，从而导致集群中的主机数量随着时间而增长。若没有旧主机（它们被主动用作目的地址）被保存在群集中，从而允许与它们的连接保持打开状态，从而节省了打开新连接所花费的等待时间。如果未指定此设置，则默认为5000.对于`original_dst`以外的群集类型，此设置将被忽略。

- **dns_refresh_rate_ms**<br />
	(optional, integer) 如果指定了dns刷新率，并且群集类型为`strict_dns`或`logical_dns`，则将此值用作群集的dns刷新率。如果未指定此设置，则该值默认为5000。对于`strict_dns`和`logical_dns`以外的群集类型，此设置将被忽略。

- **dns_lookup_family**<br />
	(optional, string) DNS IP地址解析策略。选项为`v4_only`，`v6_only`和`auto`。如果未指定此设置，则该值默认为`v4_only`。当选择`v4_only`时，DNS解析器将仅执行IPv4系列的地址查找。如果选择`v6_only`，则DNS解析程序将仅执行IPv6系列的地址查找。如果指定了`auto`，则DNS解析器将首先执行IPv6系列的地址查找操作，并然会再回到IPv4系列的地址查找。对于`strict_dns`和`logical_dns`以外的集群类型，该设置将被忽略。

- **dns_resolvers**<br />
	(optional, array) 如果指定DNS解析程序，并且群集类型是`strict_dns`或`logical_dns`，则此值用于指定群集的dns解析程序。如果未指定此设置，则该值默认为使用`/etc/resolv.conf`配置的默认解析器。对于除`strict_dns`和`logical_dns`以外的集群类型，此设置将被忽略。

- **outlier_detection**<br />
	(optional, object) 如果指定，则会为此上游群集启用[异常值检测](Cluster/Outlierdetection.md)。有关异常值检测的更多信息，请参阅相应[架构概述](../../Introduction/Architectureoverview/Outlierdetection.md)。

### 子章节

- [健康检查](Cluster/Healthchecking.md)
- [熔断](Cluster/Circuitbreakers.md)
- [TLS上下文](Cluster/TLScontext.md)
- [异常值检测](Cluster/Outlierdetection.md)
- [环哈希负载均衡配置](Cluster/Ringhashloadbalancerconfiguration.md)

## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)
