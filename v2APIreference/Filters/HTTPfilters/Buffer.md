## 缓存

### Buffer
Buffer configuration overview.

- **filter.http.Buffer**</br>
[filter.http.Buffer proto]()

```
{
  "max_request_bytes": "{...}",
  "max_request_time": "{...}"
}
```
- **max_request_bytes**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The maximum request size that the filter will buffer before the connection manager will stop buffering and return a 413 response.

- **max_request_time**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The maximum number of seconds that the filter will wait for a complete request before returning a 408 response.



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)