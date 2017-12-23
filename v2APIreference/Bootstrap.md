## 启动引导

该协议通过命令行`-c`参数提供，并作为Envoy v2根配置。有关更多详细信息，请参阅v2[配置概述](../Configurationreference/Overviewv2API.md)。

- [Bootstrap](#Bootstrap)
- [Bootstrap.StaticResources](#Bootstrap.StaticResources)
- [Bootstrap.DynamicResources](#Bootstrap.DynamicResources)
- [Bootstrap.DynamicResources.DeprecatedV1](#Bootstrap.DynamicResources.DeprecatedV1)
- [LightstepConfig](#LightstepConfig)
- [ZipkinConfig](#ZipkinConfig)
- [Tracing](#Tracing)
- [Tracing.Http](#Tracing.Http)
- [Admin](#Admin)
- [ClusterManager](#ClusterManager)
- [ClusterManager.OutlierDetection](#ClusterManager.OutlierDetection)
- [StatsdSink](#StatsdSink)
- [StatsSink](#StatsSink)
- [TagSpecifier](#TagSpecifier)
- [StatsConfig](#StatsConfig)
- [Watchdog](#Watchdog)
- [Runtime](#Runtime)
- [RateLimitServiceConfig](#RateLimitServiceConfig)

### Bootstrap

[Bootstrap proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L22)

启动引导[配置概述](../Configurationreference/Overviewv2API.md)

```
{
  "node": "{...}",
  "static_resources": "{...}",
  "dynamic_resources": "{...}",
  "cluster_manager": "{...}",
  "flags_path": "...",
  "stats_sinks": [],
  "stats_config": "{...}",
  "stats_flush_interval": "{...}",
  "watchdog": "{...}",
  "tracing": "{...}",
  "rate_limit_service": "{...}",
  "runtime": "{...}",
  "admin": "{...}"
}
```

- **node**<br />
	([Node](../v2APIreference/Commontypes.md)) 节点标识，会在管理服务器中呈现，用于标识目的（例如，头域中生成相应的字段）

- **static_resources**<br />
	([Bootstrap.StaticResources](#Bootstrap.StaticResources)) 指定静态资源配置
- **dynamic_resources**<br />
	([Bootstrap.DynamicResources](#Bootstrap.DynamicResources)) 动态发现服务源配置

- **cluster_manager**<br />
	([ClusterManager](#ClusterManager)) 该服务所有的上游群集的群集管理配置

- **flags_path**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 可选，用于启动文件标志的路径。

- **stats_sinks**<br />
	([StatsSink](#StatsSink)) 可选，统计汇总设置

- **stats_config**<br />
	([StatsConfig](#StatsConfig)) 配置内部处理统计信息

- **stats_flush_interval**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 可选，刷新到统计信息服务的周期时间。出于性能方面的考虑，Envoy锁定计数器，并且只是周期性地刷新计数器和计量器。如果未指定，则默认值为5000毫秒（5秒）。

- **watchdog**<br />
	([Watchdog](#Watchdog)) 可选，看门狗配置

- **tracing**<br />
	([Tracing](#Tracing)) 配置外置的跟踪服务程序。如果没有指定，则不会执行跟踪。

- **rate_limit_service**<br />
	([RateLimitServiceConfig](#RateLimitServiceConfig)) 配置外置的速率限制服务。如果没有指定，任何调用速率限制服务将立即返回成功。

- **runtime**<br />
	([Runtime](#Runtime)) 配置运行时配置分发服务程序。如果未指定，这将导致使用所有默认值。

- **admin**<br />
	([Admin](#Admin), 必选) 配置本地管理的HTTP服务。

### Bootstrap.StaticResources

[Bootstrap.StaticResources proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L27)

```
{
  "listeners": [],
  "clusters": []
}
```

- **listeners**<br />
	([Listener](../ListenersandLDS.md)) 静态监听器。无论LDS如何配置，这些监听器都将可用。

- **clusters**<br />
	([Cluster](../ClustersandCDS.md)) 如果[cds_config](#Bootstrap.DynamicResources)指定了基于网络的配置源，则需要提供一些初始集群定义，以便Envoy知道如何与管理服务交互。这些集群定义可能不使用EDS（即它们应该是基于静态IP或DNS）。

### Bootstrap.DynamicResources

[Bootstrap.DynamicResources proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L46)
```
{
  "lds_config": "{...}",
  "cds_config": "{...}",
  "ads_config": "{...}",
  "deprecated_v1": "{...}"
}
```

- **lds_config**<br />
	([ConfigSource](Commontypes.md)) 所有的监听器配置定义都由一个LDS配置源提供配置。

- **cds_config**<br />
	([ConfigSource](Commontypes.md)) 所有后启动引导群集定义由单个CDS配置源提供配置。

- **ads_config**<br />
	([ApiConfigSource](#Commontypes.md)) 可选，指定一个ADS源。这必须将`api_type`配置为`GRPC`。在配置源中仅ADS基于流传输通道。

- **deprecated_v1**<br />
	([Bootstrap.DynamicResources.DeprecatedV1](#Bootstrap.DynamicResources.DeprecatedV1))


### Bootstrap.DynamicResources.DeprecatedV1


[Bootstrap.DynamicResources.DeprecatedV1 proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L65)
```
{
  "sds_config": "{...}"
}
```

- **sds_config**<br />
    ([ConfigSource](Commontypes.md)) 这是使用v1 REST API提供CDS/EDS服务发现的全局SDS配置。

### LightstepConfig


[LightstepConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L117)

Configuration for the LightStep tracer.
```
{
  "collector_cluster": "...",
  "access_token_file": "..."
}
```

- **collector_cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), 必选) 配置LightStep服务的群集管理

- **access_token_file**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), 必选) 包含访问LightStep API的`token`文件。

### ZipkinConfig

[ZipkinConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L126)
```
{
  "collector_cluster": "...",
  "collector_endpoint": "..."
}
```

- **collector_cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), 必选) 
配置Zipkin服务的群集管理器。请注意，Zipkin群集必须在Bootstrap[静态群集资源](#Bootstrap.StaticResources)中定义。

- **collector_endpoint**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), 必选) 将`span`发送的Zipkin服务的API端口。当安装标准的Zipkin服务时，API端口通常是`/api/v1/spans`，这也是默认配置值。

### Tracing

[Tracing proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L143)

跟踪配置，指定Envoy使用的HTTP跟踪全局设置。该配置由[Bootstrap tracing](#Bootstrap)字段定义。未来，Envoy可能会支持其他跟踪器，但现在仅支持HTTP跟踪器。

```
{
  "http": "{...}"
}
```

- **http**<br />
	([Tracing.Http](#Tracing.Http)) 提供HTTP跟踪器的配置


### Tracing.Http

[Tracing.Http proto]()
```
{
  "name": "...",
  "config": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), 必选) 将要实例化的HTTP跟踪驱动程序的名称。该名称必须与支持的HTTP跟踪驱动程序相匹配。目前支持 `envoy.lightstep`和`envoy.zipkin`内置的跟踪驱动程序。

- **config**<br />
	([Struct](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#struct)) 跟踪驱动程序的具体配置，这取决于实例化的驱动程序。 有关示例，请参阅[LightstepConfig](#LightstepConfig)和[ZipkinConfig](#ZipkinConfig)跟踪驱动程序配置。


### Admin

[Admin proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L162)

管理接口[操作文档](../Operationsandadministration/Administrationinterface.md)

```
{
  "access_log_path": "...",
  "profile_path": "...",
  "address": "{...}"
} 
```

- **access_log_path**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), 必选) 配置管理服务的访问日志路径。如果不需要访问日志，则指定"/dev/null"。

- **profile_path**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 管理服务的cpu分析器输出路径。如果未指定配置文件路径，则默认为"/var/log/envoy/envoy.prof"。

- **address**<br />
	([Address](#), 必选) 管理服务要监听的TCP地址


### ClusterManager

[ClusterManager proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L176)

Cluster manager architecture overview.
```
{
  "local_cluster_name": "...",
  "outlier_detection": "{...}",
  "upstream_bind_config": "{...}",
  "load_stats_config": "{...}"
}
```

- **local_cluster_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 本地集群的名称（即拥有运行此配置的Envoy集群）。为了启用区域感知路由，必须设置此选项。如果定义了`local_cluster_name`，则必须在[Bootstrap.StaticResources](#Bootstrap.StaticResources)资源中定义集群。这与`--service-cluster`选项无关，该选项不影响区域感知路由。

- **outlier_detection**<br />
	([ClusterManager.OutlierDetection](#ClusterManager.OutlierDetection)) 可选，全局的异常值检测配置。

- **upstream_bind_config**<br />
	([BindConfig](../v2APIreference/Networkaddresses.md)) 可选，用于绑定新建立的上游连接。这可以通过`cds_config`中的`upstream_bind_config`以群集为基础进行覆盖。

- **load_stats_config**<br />
	([ApiConfigSource](#Commontypes.md)) 管理服务端口负载统计信息通过`StreamLoadStats`流传输。这必须将`api_type`配置为`GRPC`。


### ClusterManager.OutlierDetection

[ClusterManager.OutlierDetection proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L188)
```
{
  "event_log_path": "..."
}
```

- **event_log_path**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定异常事件日志的路径

### StatsdSink

[StatsdSink proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L206)

统计配置，用于内置`envoy.statsd`接收器的proto类型定义。

```
{
  "address": "{...}",
  "tcp_cluster_name": "..."
}
```

- **address**<br />
	([Address](../v2APIreference/Networkaddresses.md)) 可用的`statsd`兼容收集器的UDP地址。如果指定，统计数据将被刷新到这个地址。

- **tcp_cluster_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 提供基于TCP 的statsd兼容收集器群集名称。如果指定，Envoy将连接到此群集以刷新统计信息。

### StatsSink

[StatsSink proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L223)

可插拔的统计接收器配置

```
{
  "name": "...",
  "config": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 统计信息的名称可以进行实例化。该名称必须与受支持的统计信息库相匹配。`envoy.statsd`是一个适合发送到`statsd`的内置收集器。

- **config**<br />
	([Struct](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#struct)) 特定的统计收集器配置，这取决于被实例化的收集器。以[StatsdSink](#StatsdSink)为例。

### TagSpecifier

[TagSpecifier proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L238)

从指定标签抽取和剥离的标签名称，并将其作为所有统计信息的指定标签使用。只有当名称的任何部分与有一个或多个捕获组正则表达式匹配时才会发生这种情况。

```
{
  "tag_name": "...",
  "regex": "..."
}
```

- **tag_name**<br />
    ([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 将标识符附加到标签值以标识接收器中的标签。 Envoy有一组默认名称和正则表达式来提取现有统计信息的动态部分，这些信息可以在Envoy存储库中的`well_known_names.h`中找到。如果在配置中提供了一个`tag_name`，那么Envoy会尝试在默认设置中找到该名称，并使用附带的正则表达式。
	
	注意：如果任何默认标签被指定两次，配置将被视为无效。

- **regex**<br />
    ([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 第一个捕获组识别要删除的名称部分。第二个捕获组（通常嵌套在第一个捕获组中）将指定统计信息的标记值。如果没有提供第二个捕获组，第一个也将用于设置标签的值。所有其他捕获组将被忽略。
	
    例如，一个统计名称为`cluster.foo_cluster.upstream_rq_timeout`和
    <br />
```
{
  "tag_name": "envoy.cluster_name",
  "regex": "^cluster\.((.+?)\.)"
}
```

    请注意，正则表达式将删除`foo_cluster.` 使标签名称为    `cluster.upstream_rq_timeout`，并且将`envoy.cluster_name`的标签增加`foo_cluster` （注意：由于第二个捕获组，将不会有`.`字符）。

    有两个正则表达式和统计信息名称的示例`http.connection_manager_1.user_agent.ios.downstream_cx_total`：
```
[
  {
    "tag_name": "envoy.http_user_agent",
    "regex": "^http(?=\.).*?\.user_agent\.((.+?)\.)\w+?$"
  },
  {
    "tag_name": "envoy.http_conn_manager_prefix",
    "regex": "^http\.((.*?)\.)"
  }
]
```
    第一个正则表达式将删除`ios`，留下标签提取名称`http.connection_manager_1.user_agent.downstream_cx_total`。 标签`envoy.http_user_agent`将添加`ios标签值。

    第二个正则表达式将删除`connection_manager_1`。从第一个正则表达式`http.connection_manager_1.user_agent.downstream_cx_total`生成的标签提取名称中，留下`http.user_agent.downstream_cx_total`作为标签提取名称。在标签`envoy.http_conn_manager_prefix`将添加`connection_manager_1`标签值。

### StatsConfig

[StatsConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L305)

统计架构[概述](../Introduction/Architectureoverview/Statistics.md)。
```
{
  "stats_tags": [],
  "use_all_default_tags": "{...}"
}
```

- **stats_tags**<br />
	([TagSpecifier](#TagSpecifier)) 每个统计信息的名称都会通过这些标记符进行迭代处理。当标签匹配时，将从第一个捕获组名称中删除，后面的`TagSpecifiers`不能匹配相同的部分。

- **use_all_default_tags**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 使用Envoy中指定的所有默认正则表达式标记。这些可以与`stats_tags`中自定义标签结合使用。他们将在自定义标签之前先进行处理。
	
	注意：如果任何默认标签被指定两次，则配置将被视为无效。
	
    有关Envoy中默认标记的列表，请参阅[well_known_names.h](https://github.com/envoyproxy/envoy/blob/master/source/common/config/well_known_names.h)。
    
    如果没有提供，则该值默认为真。

### Watchdog

[Watchdog proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L332)

Envoy进程的看门狗配置。配置后，将监视未响应的线程，并在达到配置的阈值后终止本进程。
```
{
  "miss_timeout": "{...}",
  "megamiss_timeout": "{...}",
  "kill_timeout": "{...}",
  "multikill_timeout": "{...}"
}
```

- **miss_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Envoy统计`server.watchdog_miss`统计信息中的未响应线程的持续时间。如果没有指定，默认是200ms。

- **megamiss_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Envoy在`server.watchdog_mega_miss`统计信息中计算无响应线程的持续时间。如果未指定，则默认值为1000ms。

- **kill_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 如果被监控的线程在这段时间内没有响应，则假定编程错误并终止整个Envoy进程。设置为0可禁用kill行为。如果未指定，则默认值为0（禁用）。

- **multikill_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 如果至少有两个被监控的线程，在这个持续时间内没有响应，则假定为真正的死锁并杀死整个Envoy进程。设置为0可禁用此行为。如果未指定，则默认值为0（禁用）。


### Runtime

[Runtime proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L354)

Runtime[架构概述](../Configurationreference/Runtime.md)。
```
{
  "symlink_root": "...",
  "subdirectory": "...",
  "override_subdirectory": "..."
}
```

- **symlink_root**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), 必选) 该实现假定文件系统通过符号链接进行访问。并在切换到新文件目录时，使用原子链接交换。此参数指定符号链接的路径。Envoy将观察位置是否更改，并在发生时重新加载文件目录树。

- **subdirectory**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定要在根目录中加载的子目录。如果多个系统共享相同的更新机制，这会很有用。Envoy配置文件可以包含在一个专用的子目录中。

- **override_subdirectory**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 指定在根目录中加载的可选子目录。如果指定的目录存在，则此目录中的配置值将覆盖在主子目录中对应的值。当Envoy跨多种不同的服务器类型部署时，这很有用。有时为运行时配置提供每个服务集群目录是有用的。请参阅下面的如何使用覆盖目录。


### RateLimitServiceConfig

[RateLimitServiceConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/bootstrap.proto#L377)

速率限制[架构概述](../Configurationreference/Ratelimitservice.md)。

```
{
  "cluster_name": "..."
}
```
- **cluster_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), 必选) 指定承载速率限制服务的群集管理器名称。当需要进行速率限制服务请求时，客户端将连接到该群集，获取速率限制配置服务。


## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)
