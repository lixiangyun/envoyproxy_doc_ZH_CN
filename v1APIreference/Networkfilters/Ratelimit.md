### 速率限制

速率限制[配置参考](../../Configurationreference/Networkfilters/Ratelimit.md)。

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
- **stat_prefix**<br />
	(required, string) 发布统计信息时使用的前缀。

- **domain**<br />
	(required, string) 用于速率限制服务请求中的限制
	域。

- **descriptors**<br />
	(required, array) 用于速率限制服务请求中的速率限制描述符列表。描述符按照以下示例进行指定：

    ```
    [
        [
            {
                "key": "hello",
                "value": "world"
            },
            {
                "key": "foo",
                "value": "bar"
            }
        ],
        [
            {
                "key": "foo2",
                "value": "bar2"
            }
        ]
    ]
    ```

- **timeout_ms**<br />
	(optional, integer) 速率限制服务RPC的超时时间（以毫秒为单位）。如果未设置，则默认为20ms。


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

