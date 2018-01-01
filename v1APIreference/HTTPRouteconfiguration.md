## HTTP路由配置

- [虚拟主机](HTTPRouteconfiguration/Virtualhost.md)
- [路由](HTTPRouteconfiguration/Route.md)
- [虚拟集群](HTTPRouteconfiguration/Virtualcluster.md)
- [速率限制配置](HTTPRouteconfiguration/Ratelimitconfiguration.md)
- [路由发现服务](HTTPRouteconfiguration/RoutediscoveryserviceRDS.md)

### HTTP Route configuration
### Routing architecture overview
### HTTP router filter
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
	(optional, boolean) An optional boolean that specifies whether the clusters that the route table refers to will be validated by the cluster manager. If set to true and a route refers to a non-existent cluster, the route table will not load. If set to false and a route refers to a non-existent cluster, the route table will load and the router filter will return a 404 if the route is selected at runtime. This setting defaults to true if the route table is statically defined via the route_config option. This setting default to false if the route table is loaded dynamically via the rds option. Users may which to override the default behavior in certain cases (for example when using cds with a static route table).

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


## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

