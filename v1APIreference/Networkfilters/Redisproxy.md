### Redis代理
Redis代理[配置参考](../../Configurationreference/Networkfilters/Redisproxy.md)。

```
{
  "name": "redis_proxy",
  "config": {
    "cluster_name": "...",
    "conn_pool": "{...}",
    "stat_prefix": "..."
  }
}
```
- **cluster_name**<br />
	(required, string) 对应集群管理器的集群名称。有关群集的配置建议，请参阅[架构概述](../../Introduction/Architectureoverview/Redis.md)的配置部分。

- **conn_pool**<br />
	(required, object) 连接池配置。

- **stat_prefix**<br />
	(required, string) 发布统计信息时使用的前缀。

### Connection pool configuration
```
{
  "op_timeout_ms": "...",
}
```
- **op_timeout_ms**<br />
	(required, integer) 每操作的超时时间（单位：毫秒）。定时器是在管道的第一个命令写入后端连接时启动。从Redis收到的每个响应都会重置定时器，因为它表示下一个命令由后端处理的时间。这种行为的唯一例外是到后端的连接尚未建立。在这种情况下，将由群集上的连接超时控制，直到连接准备就绪。

## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

