## 健康检查

健康检查[配置概述](../../../Configurationreference/HTTPfilters/Healthcheck.md)。

### filter.http.HealthCheck
[filter.http.HealthCheck proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/health_check.proto#L12)

```
{
  "pass_through_mode": "{...}",
  "endpoint": "...",
  "cache_time": "{...}"
}
```

- **pass_through_mode**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue), REQUIRED) 指定过滤器是否在传递模式下运行。

- **endpoint**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 传入应被视为健康检查的HTTP端口。例如`/healthcheck`。

- **cache_time**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 如果在传递模式下运行，则过滤器需要缓存上游响应的时间（以毫秒为单位）。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)

