## Rate limit
Rate limit configuration overview.

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
	([required](#), string) The rate limit domain to use when calling the rate limit service.

- **stage**<br />
	([optional](#), integer) Specifies the rate limit configurations to be applied with the same stage number. If not set, the default stage number is 0.


NOTE: The filter supports a range of 0 - 10 inclusively for stage numbers.

- **request_type**<br />
	([optional](#), string) The type of requests the filter should apply to. The supported types are internal, external or both. A request is considered internal if x-envoy-internal is set to true. If x-envoy-internal is not set or false, a request is considered external. The filter defaults to both, and it will apply to all request types.

- **timeout_ms**<br />
	([optional](#), integer) The timeout in milliseconds for the rate limit service RPC. If not set, this defaults to 20ms.




## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)