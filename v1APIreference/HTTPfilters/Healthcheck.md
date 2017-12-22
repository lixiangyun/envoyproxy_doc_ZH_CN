## Health check
Health check configuration overview.

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
- **pass_through_mode**</br>
	([required](#), boolean) Specifies whether the filter operates in pass through mode or not.

- **endpoint**</br>
	([required](#), string) Specifies the incoming HTTP endpoint that should be considered the health check endpoint. For example /healthcheck.

- **cache_time_ms**</br>
	([optional](#), integer) If operating in pass through mode, the amount of time in milliseconds that the filter should cache the upstream response.

### Next  Previous



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)