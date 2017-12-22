### Redis proxy
Redis proxy configuration overview.

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
- **cluster_name**</br>
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) Name of cluster from cluster manager. See the configuration section of the architecture overview for recommendations on configuring the backing cluster.

- **conn_pool**</br>
	([required](#), object) Connection pool configuration.

- **stat_prefix**</br>
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The prefix to use when emitting statistics.

### Connection pool configuration
```
{
  "op_timeout_ms": "...",
}
```
- **op_timeout_ms**</br>
	([required](#), integer) Per-operation timeout in milliseconds. The timer starts when the first command of a pipeline is written to the backend connection. Each response received from Redis resets the timer since it signifies that the next command is being processed by the backend. The only exception to this behavior is when a connection to a backend is not yet established. In that case, the connect timeout on the cluster will govern the timeout until the connection is ready.



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

