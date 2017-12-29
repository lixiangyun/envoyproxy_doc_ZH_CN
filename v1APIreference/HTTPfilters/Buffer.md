### Buffer
Buffer configuration overview.

```
{
  "name": "buffer",
  "config": {
    "max_request_bytes": "...",
    "max_request_time_s": "..."
  }
}
```
- **max_request_bytes**<br />
	([required](#), integer) The maximum request size that the filter will buffer before the connection manager will stop buffering and return a 413 response.

- **max_request_time_s**<br />
	([required](#), integer) The maximum number of seconds that the filter will wait for a complete request before returning a 408 response.




## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)