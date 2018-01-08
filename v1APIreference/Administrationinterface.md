## 管理接口
管理接口[操作文档](../Operationsandadministration/Administrationinterface.md)。

```
{
  "access_log_path": "...",
  "profile_path": "...",
  "address": "..."
}
```
- **access_log_path**<br />
	(required, string) 管理服务器的访问日志的路径。如果不需要访问日志，则指定"/dev/null"。

- **profile_path**<br />
	(optional, string) 管理服务器的cpu分析器的文件路径。如果配置未指定，则默认路径为"/var/log/envoy/envoy.prof"。

- **address**<br />
	(required, string) 管理服务器将监听的TCP地址，例如"tcp://127.0.0.1:1234"。请注意，"tcp://0.0.0.0:1234"将匹配任何带有端口1234的IPv4地址。


## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

