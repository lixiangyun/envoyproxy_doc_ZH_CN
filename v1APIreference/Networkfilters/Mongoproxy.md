### Mongo代理
MongoDB[配置参考](../../Configurationreference/Networkfilters/Mongoproxy.md)。

```
{
  "name": "mongo_proxy",
  "config": {
    "stat_prefix": "...",
    "access_log": "...",
    "fault": {}
  }
}
```
- **stat_prefix**<br />
	(required, string) 用于发布统计信息时所使用的前缀。

- **access_log**<br />
	(optional, string) 用于Mongo访问日志的文件系统路径。如果未指定访问日志的路径，则不会写入访问日志。请注意，访问日志也受运行时配置控制。

- **fault**<br />
	(optional, object) 如果指定，过滤器将根据`object`中的值注入故障。

### Fault configuration
MongoDB提供持续固定时间延迟的配置。适用于以下MongoDB操作：`Query`，`Insert`，`GetMore`和`KillCursors`。一旦配置延时并且生效，在定时器触发之前，所有输入数据的时间，也将成为延迟时间的一部分。

```
{
  "fixed_delay": {
    "percent": "...",
    "duration_ms": "..."
  }
}
```
- **percent**<br />
	(required, integer) 当没有故障时，正常的MongoDB操作的受到故障注入的影响的概率。有效值是[0，100]范围内的整数。

- **duration_ms**<br />
	(required, integer) 非负整数，持续延迟的时间（以毫秒为单位）。

## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

