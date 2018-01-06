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
	(optional, boolean) 可选的bool类型，是否需要集群管理器验证路由表所引用的集群。如果设置为true，若路由表中引用了不存在的集群，则路由表将不会加载。如果设置为false，若路由引用不存在的集群，则路由表将加载，如果在运行时选择路由，则路由器过滤器将返回404。如果路由表是通过`route_config`选项静态定义的，则此设置默认为true。如果路由表是通过rds选项动态加载的，则此设置默认为false。用户可以在某些情况下修改默认行为（例如，在使用静态路由表时使用cds）。

- **virtual_hosts**<br />
	(required, array) 组成路由表的一组虚拟主机。

- **internal_only_headers**<br />
	(optional, array) （可选）指定仅为连接管理器内部使用的HTTP头部列表。如果在外部请求中找到它们，将在过滤器调用之前清除它们。有关更多信息，请参见[x-envoy-internal](../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-envoy-internal)。以下面的形式指定头部：
    ```
    ["header1", "header2"]
    ```

- **response_headers_to_add**<br />
	(optional, array) （可选）连接管理器在编码时需要添加到每个响应中的HTTP头部列表。以下面的方式指定：
    ```
    [
      {"key": "header1", "value": "value1"},
      {"key": "header2", "value": "value2"}
    ]
    ```

- **response_headers_to_remove**<br />
	(optional, array) （可选）连接管理器在编码每个响应时，需要删除的HTTP头部列表。以下面的形式指定：
    ```
    ["header1", "header2"]
    ```

- **request_headers_to_add**<br />
	(optional, array) HTTP连接管理器在转发每个请求时需要添加的HTTP头部列表，以下面的形式指定：
    ```
    [
      {"key": "header1", "value": "value1"},
      {"key": "header2", "value": "value2"}
    ]
    ```
    有关更多信息，请参阅[自定义请求头部](../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md)的文档。

- [虚拟主机](HTTPRouteconfiguration/Virtualhost.md)
- [路由](HTTPRouteconfiguration/Route.md)
- [虚拟集群](HTTPRouteconfiguration/Virtualcluster.md)
- [速率限制配置](HTTPRouteconfiguration/Ratelimitconfiguration.md)
- [路由发现服务](HTTPRouteconfiguration/RoutediscoveryserviceRDS.md)

## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

