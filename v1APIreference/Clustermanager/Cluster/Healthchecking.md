### 健康检查

- 健康检查[架构概述](../../../Introduction/Architectureoverview/Healthchecking.md)。
- 如果为集群配置了健康检查，则会发出相应的统计信息。并且记录在[这里](../../../Configurationreference/Clustermanager/Statistics.md)。

```
{
  "type": "...",
  "timeout_ms": "...",
  "interval_ms": "...",
  "unhealthy_threshold": "...",
  "healthy_threshold": "...",
  "path": "...",
  "send": [],
  "receive": [],
  "interval_jitter_ms": "...",
  "service_name": "..."
}
```

- **type**<br />
	(required, string) 健康检查的类型。目前支持的类型有`http`，`redis`和`tcp`。请参阅[架构概述](../../../Introduction/Architectureoverview/Healthchecking.md)以获取更多信息。

- **timeout_ms**<br />
	(required, integer) 等待健康检查响应的时间（以毫秒为单位）。如果达到超时时间，则该健康检查将被视为失败。

- **interval_ms**<br />
	(required, integer) 每次健康检查的时间间隔，以毫秒为单位。

- **unhealthy_threshold**<br />
	(required, integer) 在主机被标记为不健康之前，需要进行健康检查次数。请注意，对于`http`健康检查类型，如果主机响应503，则此阈值将被忽略，并且主机立即被视为不健康。

- **healthy_threshold**<br />
	(required, integer) 在主机被标记为健康之前，需要进行健康检查次数。请注意，在启动过程中，只需要一次成功的健康检查即可将主机标记为健康状态。

- **path**<br />
	(sometimes required, string) 如果是`http`类型，则此参数是必需的。它会在健康检查过程中，请求的`HTTP`路径。例如`/healthcheck`。

- **send**<br />
	(sometimes required, array) 如果是`tcp`类型，则此参数是必需的。它指定了为健康检查请求发送的字节。如下例所示，它是一个十六进制字符串数组：

    ```
    [
      {"binary": "01"},
      {"binary": "000000FF"}
    ]
    ```

    在"connect only"健康检查的情况下，数组允许为空。

- **receive**<br />
	(sometimes required, array) 如果是tcp类型，则此参数是必需的。它指定了成功的健康检查响应中预期的字节。它是一个与`send`参数指定类似的十六进制字符串数组。 在"connect only"健康检查的情况下，数组允许为空。

- **interval_jitter_ms**<br />
	(optional, integer) 可选的抖动量（以毫秒为单位）。如果指定的话，Enovy在每个间隔内，都会添加0到`interval_jitter_ms`的等待时间。

- **service_name**<br />
	(optional, string) 可选的服务名称参数，用于验证健康检查的群集的身份。请参阅[架构概述](../../../Introduction/Architectureoverview/Healthchecking.md)以获取更多信息。


## 返回
- [上一级](../Cluster.md)
- [首页目录](../../../README.md)
