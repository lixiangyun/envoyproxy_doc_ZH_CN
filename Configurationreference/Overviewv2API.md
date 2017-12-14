# V2 API概述

Envoy的v2 API采用[Protobuf3](https://developers.google.com/protocol-buffers/)作为数据面API集。由现有[v1 API](Overviewv1API.md)概念演变而来的：

- 基于gRPC流传输[xDS](https://github.com/envoyproxy/data-plane-api/blob/master/XDS_PROTOCOL.md) API。减少对资源开销和更低的延迟。
- 基于REST-JSON API，其中JSON/YAML格式需要符合[proto3-JSON规范](https://developers.google.com/protocol-buffers/docs/proto3#json)。
- 通过文件系统，REST-JSON 或者 gRPC端口进行更新。
- 通过高级负载均衡API端口，向管理服务上报负载和资源利用率信息。
- 更强的一致性和时序属性。V2 API仍然能保持最终一致性模型。

有关Envoy与管理服务之间，消息交互方面的更多详细信息，请参阅[xDS协议](https://github.com/envoyproxy/data-plane-api/blob/master/XDS_PROTOCOL.md)说明。


## 引导配置
若要使用v2 API，需要提供配置文件启动程序。这提供了静态服务配置，并配置Envoy以在需要时访问动态配置。与v1的JSON/YAML配置一样，这通过在命令行上提供`-c`标志，即：

```
./envoy -c <path to config>.{json,yaml,pb,pb_text} --v2-config-only
```

其中扩展标志表示仅支持v2配置。`--v2-config-only`标志不是必选的，因为Envoy会尝试自动检测配置文件的版本，当配置解析失败时，这个选项可以增强调试能力。

[引导](../v2APIreference/Bootstrap.md)是根配置。引导的一个关键概念是静态和动态资源之间的区别。`Listener`或`Cluster`等资源可以在`static_resources`中静态配置，也可以在`dynamic_resources`中配置[LDS](Listeners.md)或[CDS](Clustermanager.md)之类的动态xDS服务。


## 例子
下面我们将使用YAML配置原型，表示从`127.0.0.1:10000`到`127.0.0.2:1234`的HTTP服务代理的运行示例。

### 全静态
下面提供了一个全静态最小引导配置：

```
admin:
  access_log_path: /tmp/admin_access.log
  address:
    socket_address: { address: 127.0.0.1, port_value: 9901 }

static_resources:
  listeners:
  - name: listener_0
    address:
      socket_address: { address: 127.0.0.1, port_value: 10000 }
    filter_chains:
    - filters:
      - name: envoy.http_connection_manager
        config:
          stat_prefix: ingress_http
          codec_type: AUTO
          route_config:
            name: local_route
            virtual_hosts:
            - name: local_service
              domains: ["*"]
              routes:
              - match: { prefix: "/" }
                route: { cluster: some_service }
          http_filters:
          - name: envoy.router
  clusters:
  - name: some_service
    connect_timeout: 0.25s
    type: STATIC
    lb_policy: ROUND_ROBIN
    hosts: [{ socket_address: { address: 127.0.0.2, port_value: 1234 }}]
```

### 大部分静态&动态服务发现
下面提供了一个引导配置，从上面的示例中继续，通过在`127.0.0.3:5678`上监听的EDS gRPC管理服务进行动态`endpoint`发现：

```
admin:
  access_log_path: /tmp/admin_access.log
  address:
    socket_address: { address: 127.0.0.1, port_value: 9901 }

static_resources:
  listeners:
  - name: listener_0
    address:
      socket_address: { address: 127.0.0.1, port_value: 10000 }
    filter_chains:
    - filters:
      - name: envoy.http_connection_manager
        config:
          stat_prefix: ingress_http
          codec_type: AUTO
          route_config:
            name: local_route
            virtual_hosts:
            - name: local_service
              domains: ["*"]
              routes:
              - match: { prefix: "/" }
                route: { cluster: some_service }
          http_filters:
          - name: envoy.router
  clusters:
  - name: some_service
    connect_timeout: 0.25s
    lb_policy: ROUND_ROBIN
    type: EDS
    eds_cluster_config:
      eds_config:
        api_config_source:
          api_type: GRPC
          cluster_name: [xds_cluster]
  - name: xds_cluster
    connect_timeout: 0.25s
    type: STATIC
    lb_policy: ROUND_ROBIN
    http2_protocol_options: {}
    hosts: [{ socket_address: { address: 127.0.0.3, port_value: 5678 }}]
```

注意：上面的`xds_cluster`被定义为Envoy的管理服务。即使在全动态的配置中，也需要定义一些静态资源，如指定Envoy对应的xDS管理服务。

在上面的例子中，请求EDS管理服务，然后返回[服务发现应答](../v2APIreference/CommondiscoveryAPIcomponents.md)的原始编码：

```
version_info: "0"
resources:
- "@type": type.googleapis.com/envoy.api.v2.ClusterLoadAssignment
  cluster_name: some_service
  endpoints:
  - lb_endpoints:
    - endpoint:
        address:
          socket_address:
            address: 127.0.0.2
            port_value: 1234
```

以上出现的版本控制和URL类型方案，在流式[gRPC订阅协议](https://github.com/envoyproxy/data-plane-api/blob/master/XDS_PROTOCOL.md#streaming-grpc-subscriptions)文档中有更详细的解释。


### 全动态
下面提供了一个全动态的引导配置，其中除了管理服务的其他所有资源都是通过xDS发现的：

```
admin:
  access_log_path: /tmp/admin_access.log
  address:
    socket_address: { address: 127.0.0.1, port_value: 9901 }

dynamic_resources:
  lds_config:
    api_config_source:
      api_type: GRPC
      cluster_name: [xds_cluster]
  cds_config:
    api_config_source:
      api_type: GRPC
      cluster_name: [xds_cluster]

static_resources:
  clusters:
  - name: xds_cluster
    connect_timeout: 0.25s
    type: STATIC
    lb_policy: ROUND_ROBIN
    http2_protocol_options: {}
    hosts: [{ socket_address: { address: 127.0.0.3, port_value: 5678 }}]
```

管理服务可以通过以下方式响应LDS请求：

```
version_info: "0"
resources:
- "@type": type.googleapis.com/envoy.api.v2.Listener
  name: listener_0
  address:
    socket_address:
      address: 127.0.0.1
      port_value: 10000
  filter_chains:
  - filters:
    - name: envoy.http_connection_manager
      config:
        stat_prefix: ingress_http
        codec_type: AUTO
        rds:
          route_config_name: local_route
          config_source:
            api_config_source:
              api_type: GRPC
              cluster_name: [xds_cluster]
        http_filters:
        - name: envoy.router
```

管理服务可以通过以下方式响应RDS请求：

```
version_info: "0"
resources:
- "@type": type.googleapis.com/envoy.api.v2.RouteConfiguration
  name: local_route
  virtual_hosts:
  - name: local_service
    domains: ["*"]
    routes:
    - match: { prefix: "/" }
      route: { cluster: some_service }
```

管理服务可以通过以下方式响应CDS请求：

```
version_info: "0"
resources:
- "@type": type.googleapis.com/envoy.api.v2.Cluster
  name: some_service
  connect_timeout: 0.25s
  lb_policy: ROUND_ROBIN
  type: EDS
  eds_cluster_config:
    eds_config:
      api_config_source:
        api_type: GRPC
        cluster_name: [xds_cluster]
```

管理服务可以通过以下方式响应EDS请求：

```
version_info: "0"
resources:
- "@type": type.googleapis.com/envoy.api.v2.ClusterLoadAssignment
  cluster_name: some_service
  endpoints:
  - lb_endpoints:
    - endpoint:
        address:
          socket_address:
            address: 127.0.0.2
            port_value: 1234
```

### 管理服务
一个v2 xDS管理服务，需要按照gRPC/REST服务的要求提供以下API端口。在同时支持流式gRPC和REST-JSON情况下，在接收一个发现请求时，按照xDS协议返回发现响应。


#### gRPC流端口
**`POST /envoy.api.v2.ClusterDiscoveryService/StreamClusters`**

有关服务定义，请参阅[cds.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto)协议。当Envoy作为客户端时，这被使用：

```
cds_config:
  api_config_source:
    api_type: GRPC
    cluster_name: [some_xds_cluster]
```

在引导配置的`dynamic_resources`中设置。

**`POST /envoy.api.v2.EndpointDiscoveryService/StreamEndpoints`**

有关服务定义，请参阅[eds.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/eds.proto)协议。当Envoy作为客户端时，这被使用：

```
eds_config:
  api_config_source:
    api_type: GRPC
    cluster_name: [some_xds_cluster]
```

在[集群配置](../v2APIreference/ClustersandCDS.md)的`eds_cluster_config`字段中设置。

**`POST /envoy.api.v2.ListenerDiscoveryService/StreamListeners`**

有关服务定义，请参阅[lds.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/lds.proto)。当Envoy作为客户端时，这被使用：

```
lds_config:
  api_config_source:
    api_type: GRPC
    cluster_name: [some_xds_cluster]
```

在引导配置的`dynamic_resources`中设置。

**`POST /envoy.api.v2.RouteDiscoveryService/StreamRoutes`**

有关服务定义，请参阅[rds.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto)。当Envoy作为客户端时，这被使用：

```
route_config_name: some_route_name
config_source:
  api_config_source:
    api_type: GRPC
    cluster_name: [some_xds_cluster]
```

在[Http Connection Manager](../v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)配置的rds字段中设置。

#### REST端口

**`POST /v2/discovery:clusters`**

有关服务定义，请参阅[cds.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/cds.proto)协议。当Envoy作为客户端时，这被使用：
```
cds_config:
  api_config_source:
    api_type: REST
    cluster_name: [some_xds_cluster]
```
在引导配置的`dynamic_resources`中设置。

**`POST /v2/discovery:endpoints`**

有关服务定义，请参阅[eds.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/eds.proto)协议。当Envoy作为客户端时，这被使用：

```
eds_config:
  api_config_source:
    api_type: REST
    cluster_name: [some_xds_cluster]
```

在[集群配置](../v2APIreference/ClustersandCDS.md)的`eds_cluster_config`字段中设置。


**`POST /v2/discovery:listeners`**

有关服务定义，请参阅[lds.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/lds.proto)。当Envoy作为客户端时，这被使用：

```
lds_config:
  api_config_source:
    api_type: REST
    cluster_name: [some_xds_cluster]
```

在引导配置的`dynamic_resources`中设置。

**`POST /v2/discovery:routes`**

有关服务定义，请参阅[rds.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rds.proto)。当Envoy作为客户端时，这被使用：

```
route_config_name: some_route_name
config_source:
  api_config_source:
    api_type: REST
    cluster_name: [some_xds_cluster]
```

在[Http Connection Manager](../v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)配置的rds字段中设置。

### 聚合发现服务
虽然Envoy从根本上采用了最终一致性的模型，但是ADS提供了一个机会来对API更新推送进行排序，并确保Envoy节点面向单个管理服务，进行相关API更新。ADS允许一个或多个API及其资源，由管理服务在单个双向gRPC流上进行传输。没有这个，一些API（如RDS和EDS）可能需要管理多个流和连接到不同的管理服务。

ADS将允许通过适当的排序无损地更新配置。例如，假设 *foo.com* 映射到集群X。我们希望将路由表中 *foo.com* 映射为集群Y。为此，必须首先传递包含两个集群的CDS/EDS更新X和Y。

如果没有ADS，CDS/EDS/RDS流可能指向不同的管理服务器，或者位于同一个管理服务器上的不同gRPC流/连接进行协商。EDS资源请求可以分成两个不同的流，一个用于X，另一个用于Y。ADS允许将这些流合并为单个流到单个管理服务器，避免了分布式同步对正确排序更新的需要。借助ADS，管理服务器将在单个数据流上提供CDS，EDS和RDS更新。

ADS仅适用于gRPC流（不是REST），在[本文档](https://github.com/envoyproxy/data-plane-api/blob/master/XDS_PROTOCOL.md#aggregated-discovery-services-ads)中有更详细的描述。gRPC端口是：

**`POST /envoy.api.v2.AggregatedDiscoveryService/StreamAggregatedResources`**

有关服务定义，请参阅[discovery.proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/discovery.proto)。当Envoy作为客户端时，这被使用：

```
ads_config:
  api_type: GRPC
  cluster_name: [some_ads_cluster]
```

在引导配置的`dynamic_resources`中设置。

设置此项时，可以将上述任何配置源设置为使用ADS通道。例如，一个LDS配置可以从

```
lds_config:
  api_config_source:
    api_type: REST
    cluster_name: [some_xds_cluster]
```
修改为
```
lds_config: {ads: {}}
```

其效果是LDS流将通过共享的ADS通道被引导到`some_ads_cluster`。

### 状态
除非另有说明，否则将在[v2 API参考](../v2APIreference.md)中描述的所有功能。在v2 API参考和[v2 API库](https://github.com/envoyproxy/data-plane-api/tree/master/api)中，所有接口原型都被冻结，除非它们被标记为草稿或实验原型。在这里，冻结意味着我们不会打破兼容性的底线。

通过添加新的字段，以不破坏向后兼容性的方式，尽可能的进一步延长冻结原型的期限。上述原型中的字段可能会在不再使用相关功能的情况下，随着策略的改变而被弃用。虽然冻结的API保持其格式兼容性，但我们保留更改原名，文件位置和嵌套关系的权利，这可能导致代码更改中断。我们的目标是尽量减少这里的损失。

当Protos标记为草案( *draft* )，意味着它们已经接近完成，至少可能在Envoy中部分实施，但可能会在冻结之前破坏原型格式。

当Protos标记为实验性的( *experimental* )，与原始草案有相同的告警提示，并可能在Envoy执行冻结之前做出重大改变。

当前所有v2 API[问题](https://github.com/envoyproxy/envoy/issues?q=is%3Aopen+is%3Aissue+label%3A%22v2+API%22)在这里被跟踪。

## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
