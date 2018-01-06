### Virtual host

虚拟主机是路由配置中的顶层配置。每个虚拟主机都有一个逻辑名称以及一组域列表，会根据传入请求的主机头路由到对应的域。这允许为单个监听器配置多个顶级域的路径树。一旦基于域选择了虚拟主机，就会进行路由处理，以便查找并路由到相应上游集群或者是否执行重定向。

```
{
  "name": "...",
  "domains": [],
  "routes": [],
  "require_ssl": "...",
  "virtual_clusters": [],
  "rate_limits": [],
  "request_headers_to_add": []
}
```
- **name**<br />
	(required, string) 虚拟主机的逻辑名称。这在发送某些统计信息时使用，但与转发无关。默认情况下，名称的最大长度限制为60个字符。可通过`--max-obj-name-len`命令行参数设置为所需的值，以提高此限制。

- **domains**<br />
	(required, array) 将与此虚拟主机相匹配的域（主机/机构头）的列表。支持通配符匹配主机，如“*.foo.com”或“*-bar.foo.com”。请注意，通配符不匹配空字符串。例如“*-bar.foo.com”将匹配“baz-bar.foo.com”，但不匹配“-bar.foo.com”。另外，允许一个特殊的“*”匹配任何主机/机构头。整个路由配置中只有一台虚拟主机可以匹配“*”。域名在所有虚拟主机中必须是唯一的，否则配置将无法加载。

- **routes**<br />
	(required, array) 路由列表，将按配置顺序匹配传入请求。第一条匹配的路由将被使用。

- **cors**<br />
	(optional, object) 指定虚拟主机的CORS策略。

- **require_ssl**<br />
	(optional, string) 指定虚拟主机所期望的TLS认证的配置类型。可能的值有：
    - **all**<br />
    所有请求都必须使用TLS。如果请求不使用TLS，则会发送302重定向，通知客户端使用HTTPS。
    - **external_only**<br />
    来自外部请求必须使用TLS。如果请求是来自外部的，并且它没有使用TLS，则会发送302重定向，通知客户端使用HTTPS。

    如果未指定此选项，则虚拟主机没有TLS要求。

- **virtual_clusters**<br />
	(optional, array) 为此虚拟主机定义的虚拟集群列表。虚拟集群用于进行其他统计信息收集。

- **rate_limits**<br />
	(optional, array) 应用于虚拟主机的一组速率限制配置。

- **request_headers_to_add**<br />
	(optional, array) 指定此虚拟主机要添加到每个请求的HTTP头部列表。以下面的形式指定：
    ```
    [
      {"key": "header1", "value": "value1"},
      {"key": "header2", "value": "value2"}
    ]
    ```
有关更多信息，请参阅[自定义请求头部](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)的文档。


## 返回
- [上一级](../HTTPRouteconfiguration.md)
- [首页目录](../../README.md)