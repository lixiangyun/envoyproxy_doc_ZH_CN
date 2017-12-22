## 健康检查

### Health check
Health check configuration overview.

- **filter.http.HealthCheck**</br>
[filter.http.HealthCheck proto]()

```
{
  "pass_through_mode": "{...}",
  "endpoint": "...",
  "cache_time": "{...}"
}
```
- **pass_through_mode**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue), REQUIRED) Specifies whether the filter operates in pass through mode or not.

- **endpoint**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Specifies the incoming HTTP endpoint that should be considered the health check endpoint. For example /healthcheck.

- **cache_time**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) If operating in pass through mode, the amount of time in milliseconds that the filter should cache the upstream response.



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)