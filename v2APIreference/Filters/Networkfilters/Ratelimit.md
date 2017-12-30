## 速率限制

速率限制[配置参考](../../../Configurationreference/Networkfilters/Ratelimit.md)。

### filter.network.RateLimit
[filter.network.RateLimit proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/rate_limit.proto#L12)

```
{
  "stat_prefix": "...",
  "domain": "...",
  "descriptors": [],
  "timeout": "{...}"
}
```

- **stat_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 发布统计信息时使用的前缀。

- **domain**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 用于速率限制服务请求中的域。

- **descriptors**<br />
	([RateLimitDescriptor](../v2APIreference/Commonratelimitcomponents.md#ratelimitdescriptor), REQUIRED) 用于速率限制服务请求中的速率限制描述符列表。

- **timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 速率限制服务RPC的超时时间（以毫秒为单位）。如果未设置，则默认为20ms。


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

