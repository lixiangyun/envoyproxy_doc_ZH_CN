## Redis代理

Redis代理的[配置参考](../../../Configurationreference/Networkfilters/Redisproxy.md)。

- [filter.network.RedisProxy](#filternetworkredisproxy)
- [filter.network.RedisProxy.ConnPoolSettings](#filternetworkredisproxyconnpoolsettings)


### filter.network.RedisProxy
[filter.network.RedisProxy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/redis_proxy.proto#L11)

```
{
  "stat_prefix": "...",
  "cluster": "...",
  "settings": "{...}"
}
```
- **stat_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 发布统计信息时使用的前缀。

- **cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 集群管理器的集群名称。有关配置支持群集的建议，请参阅架构概述的[配置](../../../Introduction/Architectureoverview/Redis.md#配置)部分。

- **settings**<br />
	([filter.network.RedisProxy.ConnPoolSettings](#filternetworkredisproxyconnpoolsettings), REQUIRED) 连接到上游集群的网络连接池设置。

### filter.network.RedisProxy.ConnPoolSettings
[filter.network.RedisProxy.ConnPoolSettings proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/redis_proxy.proto#L21)

Redis连接池设置。

```
{
  "op_timeout": "{...}"
}
```
- **op_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 每个操作的超时时长（单位：毫秒）。在第一个命令写入后端连接时启动定时器。从Redis收到的每个响应都会重置定时器，因为它表示下一个命令正在由后端处理。这种行为的唯一例外是到后端的连接尚未建立。在这种情况下，群集上的连接超时将得到控制，直到连接准备就绪。


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

