## 速率限制
速率限制[配置概述](../../Configurationreference/HTTPfilters/Ratelimit.md)。

```
{
  "name": "rate_limit",
  "config": {
    "domain": "...",
    "stage": "...",
    "request_type": "...",
    "timeout_ms": "..."
  }
}
```
- **domain**<br />
	(required, string) 调用速率限制服务时使用的域。

- **stage**<br />
	(optional, integer) 指定要应用于相同阶段编号的速率限制配置。如果未设置，则默认阶段编号为0。

    注意:对于阶段编号，过滤器支持0-10的范围。

- **request_type**<br />
	(optional, string) 该过滤器适用的请求类型。支持的类型有`internal`，`external`或者`both`。如果将`x-envoy-internal`设置为`true`，则将请求视为内部请求。如果`x-envoy-internal`未设置或为`false`，则请求被视为外部。过滤器默认为`both`，它将应用于所有的请求类型。

- **timeout_ms**<br />
	(optional, integer) 速率限制服务的RPC超时时间（以毫秒为单位）。如果未设置，则默认为20ms。


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)
