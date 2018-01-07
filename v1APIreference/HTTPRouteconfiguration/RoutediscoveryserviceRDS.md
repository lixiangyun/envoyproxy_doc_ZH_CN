### 路由发现服务(RDS)
```
{
  "cluster": "...",
  "route_config_name": "...",
  "refresh_delay_ms": "..."
}
```
- **cluster**<br />
	(required, string) 承载路由发现服务的上游群集的名称。群集必须实现和运行RDS HTTP API的REST服务。注：这是在群集管理器配置中定义的群集的名称，而不是群集的完整定义，如SDS和CDS的情况。

- **route_config_name**<br />
	(required, string) 路由配置的名称。这个名字将被传递给RDS HTTP API。这允许具有多个HTTP监听器（和关联的HTTP连接管理器过滤器）的Envoy配置使用不同的路由配置。默认情况下，名称的最大长度限制为60个字符。通过`--max-obj-name-len`命令行参数设置为所需的值，可以提高此限制。

- **refresh_delay_ms**<br />
	(optional, integer) 每次从RDS API提取的时间间隔（以毫秒为单位）。Envoy将在0和`refresh_delay_ms`之间的增加一个额外的随机抖动。因此，最长可能的刷新间隔是`2*refresh_delay_ms`。默认值是30000ms（30秒）。

### REST API
```
GET /v1/routes/(string: route_config_name)/(string: service_cluster)/(string: service_node)
```

请求路由发现服务返回特定`route_config_name`，`service_cluster`和`service_node`的路由配置。`route_config_name`对应于上面的RDS配置参数。`service_cluster` 对应于`--service-cluster` 命令行选项。`service_node`对应于`--service-node` 命令行选项。响应是单个JSON对象，其中包含路由配置文档中定义的[路由配置](../../v1APIreference/HTTPRouteconfiguration.md)。

新的路由配置将被优雅地交换，使得现有的请求不受影响。这意味着当一个请求开始时，它会看到一个一致的快照，在请求的持续时间内不会改变路由配置。因此，例如，如果更新更改超时值，则只有新的请求将使用更新后的值。

作为性能优化，Envoy对从RDS API接收的路由配置进行哈希散列处理，并且只有在哈希值发生变化时才会执行完整的重新加载。

注意：通过RDS加载的路由配置不会检查所引用的集群是否已经在集群管理器加载。RDS API被设计为与CDS API一起工作，使得Envoy最终采取一致的更新。如果一个路由引用一个未知的集群，路由器过滤器将返回一个404响应。

## 返回
- [上一级](../HTTPRouteconfiguration.md)
- [首页目录](../../README.md)