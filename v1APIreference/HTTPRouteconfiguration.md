## HTTP路由配置

- HTTP路由[架构概述](../Introduction/Architectureoverview/HTTProuting.md)
- HTTP路由[过滤器](../Configurationreference/HTTPfilters/Router.md)


```
{
  "validate_clusters": "...",
  "virtual_hosts": [],
  "internal_only_headers": [],
  "response_headers_to_add": [],
  "response_headers_to_remove": [],
  "request_headers_to_add": []
}
```
- **validate_clusters**<br />
	(optional, boolean) 一个可选的布尔值，指定路由表引用的集群是否由集群管理器验证。如果设置为true，并且路由引用了不存在的集群，则路由表将不会加载。如果设置为false，并且路由引用不存在的集群，则路由表将加载，如果在运行时选择路由，则路由器过滤器将返回404。如果路由表是通过route_config选项静态定义的，则此设置默认为true。如果路由表是通过rds选项动态加载的，则此设置默认为false。用户可以在某些情况下重写默认行为（例如，当使用静态路由表时使用cds）。

- **virtual_hosts**<br />
	(required, array) An array of virtual hosts that make up the route table.

- **internal_only_headers**<br />
	(optional, array) Optionally specifies a list of HTTP headers that the connection manager will consider to be internal only. If they are found on external requests they will be cleaned prior to filter invocation. See x-envoy-internal for more information. Headers are specified in the following form:


```
["header1", "header2"]
```
- **response_headers_to_add**<br />
	(optional, array) Optionally specifies a list of HTTP headers that should be added to each response that the connection manager encodes. Headers are specified in the following form:


```
[
  {"key": "header1", "value": "value1"},
  {"key": "header2", "value": "value2"}
]
```
- **response_headers_to_remove**<br />
	(optional, array) Optionally specifies a list of HTTP headers that should be removed from each response that the connection manager encodes. Headers are specified in the following form:


```
["header1", "header2"]
```
- **request_headers_to_add**<br />
	(optional, array) Specifies a list of HTTP headers that should be added to each request forwarded by the HTTP connection manager. Headers are specified in the following form:


```
[
  {"key": "header1", "value": "value1"},
  {"key": "header2", "value": "value2"}
]
```
For more information see the documentation on custom request headers.


- [虚拟主机](HTTPRouteconfiguration/Virtualhost.md)
- [路由](HTTPRouteconfiguration/Route.md)
- [虚拟集群](HTTPRouteconfiguration/Virtualcluster.md)
- [速率限制配置](HTTPRouteconfiguration/Ratelimitconfiguration.md)
- [路由发现服务](HTTPRouteconfiguration/RoutediscoveryserviceRDS.md)


## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

