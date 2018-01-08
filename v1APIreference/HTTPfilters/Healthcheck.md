## 健康检查
健康检查[配置概述](../../Configurationreference/HTTPfilters/Healthcheck.md)。

```
{
  "name": "health_check",
  "config": {
    "pass_through_mode": "...",
    "endpoint": "...",
    "cache_time_ms": "..."
   }
}
```
- **pass_through_mode**<br />
	(required, boolean) 指定过滤器是否在通过模式下运行。

- **endpoint**<br />
	(required, string) 指定健康检查的HTTP API端口。例如`/healthcheck`。

- **cache_time_ms**<br />
	(optional, integer) 如果在通过模式下运行，则过滤器将缓存上游响应的时间（以毫秒为单位）。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)
