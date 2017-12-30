## 缓存

缓存[配置参考](../../../Configurationreference/HTTPfilters/Buffer.md)。

### filter.http.Buffer
[filter.http.Buffer proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/buffer.proto#L12)

```
{
  "max_request_bytes": "{...}",
  "max_request_time": "{...}"
}
```

- **max_request_bytes**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 在连接管理器停止缓冲并返回413响应之前，过滤器将缓冲的最大请求大小。

- **max_request_time**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 过滤器在返回408响应之前，等待完整请求的最长秒数。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)

