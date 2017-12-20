## Redis代理

### Redis Proxy
Redis Proxy configuration overview.

- **filter.network.RedisProxy**</br>
[filter.network.RedisProxy proto]()

```
{
  "stat_prefix": "...",
  "cluster": "...",
  "settings": "{...}"
}
```
- **stat_prefix**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The prefix to use when emitting statistics.

- **cluster**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Name of cluster from cluster manager. See the configuration section of the architecture overview for recommendations on configuring the backing cluster.

- **settings**</br>
	([filter.network.RedisProxy.ConnPoolSettings](#), REQUIRED) Network settings for the connection pool to the upstream cluster.

- **filter.network.RedisProxy.ConnPoolSettings**</br>
[filter.network.RedisProxy.ConnPoolSettings proto]()

Redis connection pool settings.

```
{
  "op_timeout": "{...}"
}
```
- **op_timeout**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Per-operation timeout in milliseconds. The timer starts when the first command of a pipeline is written to the backend connection. Each response received from Redis resets the timer since it signifies that the next command is being processed by the backend. The only exception to this behavior is when a connection to a backend is not yet established. In that case, the connect timeout on the cluster will govern the timeout until the connection is ready.


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)