## 速率限制

### Rate limit
Rate limit configuration overview.

- **filter.http.RateLimit**</br>
[filter.http.RateLimit proto]()

```
{
  "domain": "...",
  "stage": "...",
  "request_type": "...",
  "timeout": "{...}"
}
```
- **domain**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The rate limit domain to use when calling the rate limit service.

- **stage**</br>
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the rate limit configurations to be applied with the same stage number. If not set, the default stage number is 0.


### Note

The filter supports a range of 0 - 10 inclusively for stage numbers.

- **request_type**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The type of requests the filter should apply to. The supported types are internal, external or both. A request is considered internal if x-envoy-internal is set to true. If x-envoy-internal is not set or false, a request is considered external. The filter defaults to both, and it will apply to all request types.

- **timeout**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The timeout in milliseconds for the rate limit service RPC. If not set, this defaults to 20ms.

### Next  Previous



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)