## Lua

Lua[配置概述](../../../Configurationreference/HTTPfilters/Lua.md)

### filter.http.Lua
[filter.http.Lua proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/lua.proto#L9)

```
{
  "inline_code": "..."
}
```

- **inline_code**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Envoy将执行的Lua代码。这可以是一个非常小的脚本，如果需要，可以从磁盘进一步加载代码。请注意，如果使用JSON配置，则代码必须能正确转义。YAML配置可能更容易阅读，因为YAML支持多行字符串，所以复杂的脚本可以很容易地内嵌在配置中表示。


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)

