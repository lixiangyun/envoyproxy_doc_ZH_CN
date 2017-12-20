## Lua
Lua configuration overview.

```
{
  "name": "lua",
  "config": {
    "inline_code": "..."
  }
}
```
- **inline_code**</br>
	([required](#), string) The Lua code that Envoy will execute. This can be a very small script that further loads code from disk if desired. Note that if JSON configuration is used, the code must be properly escaped. YAML configuration may be easier to read since YAML supports multi-line strings so complex scripts can be easily expressed inline in the configuration.


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)