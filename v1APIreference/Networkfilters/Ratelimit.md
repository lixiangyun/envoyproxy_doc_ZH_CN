### Rate limit
Rate limit configuration overview.

```
{
  "name": "ratelimit",
  "config": {
    "stat_prefix": "...",
    "domain": "...",
    "descriptors": [],
    "timeout_ms": "..."
  }
}
```
- **stat_prefix**</br>
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The prefix to use when emitting statistics.

- **domain**</br>
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The rate limit domain to use in the rate limit service request.

- **descriptors**</br>
	([required](#), array) The rate limit descriptor list to use in the rate limit service request. The descriptors are specified as in the following example:

```
[
  [{"key": "hello", "value": "world"}, {"key": "foo", "value": "bar"}],
  [{"key": "foo2", "value": "bar2"}]
]
```


- **timeout_ms**</br>
	([optional](#), integer) The timeout in milliseconds for the rate limit service RPC. If not set, this defaults to 20ms.

### Next  Previous

 

## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

