### Buffer
缓冲区[配置概述](../../Configurationreference/HTTPfilters/Buffer.md)。

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
	(required, integer) 在连接管理器停止缓冲并返回413响应之前，过滤器将缓冲的最大请求大小。

- **max_request_time_s**<br />
	(required, integer) 过滤器在返回408响应之前等待完整请求的最大秒数。


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)