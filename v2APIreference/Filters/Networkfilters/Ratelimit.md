## 速率限制

### Rate limit
Rate limit configuration overview.

- **filter.network.RateLimit**<br />
[filter.network.RateLimit proto]()

```
{
  "stat_prefix": "...",
  "domain": "...",
  "descriptors": [],
  "timeout": "{...}"
}
```
- **stat_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The prefix to use when emitting statistics.

- **domain**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The rate limit domain to use in the rate limit service request.

- **descriptors**<br />
	([RateLimitDescriptor](#), REQUIRED) The rate limit descriptor list to use in the rate limit service request.

- **timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The timeout in milliseconds for the rate limit service RPC. If not set, this defaults to 20ms.


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

