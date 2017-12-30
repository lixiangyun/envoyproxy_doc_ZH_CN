## Mongo代理

MongoDB[配置参考](../../../Configurationreference/Networkfilters/Mongoproxy.md)。

### filter.network.MongoProxy
[filter.network.MongoProxy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/mongo_proxy.proto#L11)

```
{
  "stat_prefix": "...",
  "access_log": "...",
  "delay": "{...}"
}
```

- **stat_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 发布[统计](../../../Configurationreference/Networkfilters/Mongoproxy.md)信息时使用的前缀（提升易读性）。

- **access_log**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 可选路径，用于记录Mongo的访问日志。如果未指定访问日志路径，则不会写入访问日志。请注意，访问日志也受[运行时](../../../Configurationreference/Networkfilters/Mongoproxy.md)配置控制。

- **delay**<br />
	([filter.FaultDelay](../v2APIreference/Filters/Commonfaultinjectiontypes.md#filterfaultdelay)) 在代理Mongo操作之前注入一个固定的延迟。延迟适用于以下MongoDB操作：`Query`，`Insert`，`GetMore`和`KillCursors`。一旦进行一个有效延迟时，在定时器触发之前，在所有数据输入的时间也将作为延迟的一部分。


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

