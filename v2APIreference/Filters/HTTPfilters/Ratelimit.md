## 速率限制

速率限制[配置参考](../../../Configurationreference/HTTPfilters/Ratelimit.md)

### filter.http.RateLimit
[filter.http.RateLimit proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/rate_limit.proto#L11)

```
{
  "domain": "...",
  "stage": "...",
  "request_type": "...",
  "timeout": "{...}"
}
```
- **domain**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 需要调用速率限制服务时的域。

- **stage**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定要应用于的相应阶段的速率限制配置编号。如果未设置，则默认为0。

     注意：过滤器支持0到10的阶段编号。

- **request_type**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 过滤器应该适用的请求类型。支持内部的，外部的或两者同时。如果将`x-envoy-internal`设置为true，则将请求视为内部请求。如果`x-envoy-internal`未设置或为false，则请求被视为外部。过滤器默认为两者同时，它将应用于所有的请求类型。

- **timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 速率限制服务RPC的超时时间（以毫秒为单位）。如果未设置，则默认为20ms。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)

