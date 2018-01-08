## Lua
Lua[配置概述](../../Configurationreference/HTTPfilters/Lua.md)。

```
{
  "name": "lua",
  "config": {
    "inline_code": "..."
  }
}
```
- **inline_code**<br />
	(required, string) Envoy将执行的Lua代码。这可以是一个非常小的脚本，如果需要，可以从磁盘进一步加载代码。请注意，如果使用JSON配置，则代码必须能够正确转义。YAML配置可能更容易阅读，因为YAML支持多行字符串，可以很容易地在配置中内联表示复杂的脚本。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)
