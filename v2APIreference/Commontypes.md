## 通用类型

### Locality
[Locality proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L16)

标识Envoy运行所在的位置或上游主机运行所在的位置。

```
{
  "region": "...",
  "zone": "...",
  "sub_zone": "..."
}
```

- **region**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 当前属于哪个区域

- **zone**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) AWS上的可用区域（AZ），GCP区域等

- **sub_zone**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 当用于上游主机的位置时，该字段进一步将区域分成更小的子区域，从而可以独立地进行负载平衡。

### Node
[Node proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L35)

标识特定的Envoy实例。节点标识符将呈现给管理服务器，管理服务器可以使用该标识符来区分每个Envoy服务的配置。

```
{
  "id": "...",
  "cluster": "...",
  "metadata": "{...}",
  "locality": "{...}",
  "build_version": "..."
}
```

- **id**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Envoy节点的标识符。

- **cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Envoy节点所属的群集。

- **metadata**<br />
	([Struct](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#struct)) 所在节点的扩展元数据，Envoy将直接传递给管理服务器。

- **locality**<br />
	([Locality](#locality)) 指定Envoy实例的运行位置。

- **build_version**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 在金丝雀（灰度发布）期间管理服务器，知道哪个版本的Envoy正在进行测试。这将由Envoy在管理服务器RPC中设置。

### Endpoint
[Endpoint proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L35)

上游主机标识符

```
{
  "address": "{...}"
}
```

- **address**<br />
	([Address](Networkaddresses.md))

### Metadata
[Metadata proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L74)

元数据用于监听器的匹配场景，为路由、端口、过滤器链等提供了额外的输入。它的结构是从过滤器名称（反向DNS格式）到特定的过滤器元数据的映射。过滤器的元数据key/value将合并在连接并发生请求时处理，同一个key的更新值将覆盖旧值。

元数据的使用示例，在HTTP连接管理追加附加信息，将体现在`envoy.http_connection_manager.access_log`命名空间。

为了实现负载平衡，元数据提供了一种集群端口子集的方法。端口匹配关联的元数据对象，路由匹配关联的元数据对象。当前有一些定义的元数据用于此目的：

- `{"envoy.lb": {"canary": <bool> }}` 这表明了一个端口的`canary`状态，并且用于头部（x-envoy-upstream-canary）和统计处理。

```
{
  "filter_metadata": "{...}"
}
```

- **filter_metadata**<br />
	(map<string, Struct>) key是反向DNS过滤器名称，例如`com.acme.widget`。命名空间`envoy.*`保留给Envoy内置过滤器使用。

### RuntimeUInt32
[RuntimeUInt32 proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L81)

若没有指定时，则在运行时生成的uint32默认值。

```
{
  "default_value": "...",
  "runtime_key": "..."
}
```

- **default_value**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 默认值，运行时没有可用的值时。

- **runtime_key**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 运行时，通过key以获取相应的value。如果定义，则使用此值。

### HeaderValue
[HeaderValue proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L115)

Header键值对。

```
{
  "key": "...",
  "value": "..."
}
```

- **key**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Header Key.

- **value**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Header Value.
    
    HTTP访问日志记录的[格式说明符](../Configurationreference/Accesslogging.md)可以在此处应用，但未知的Header值将替换为空字符串而不是`-`。

### HeaderValueOption
[HeaderValueOption proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L128)

Header键值对追加控制选项。

```
{
  "header": "{...}",
  "append": "{...}"
}
```

- **header**<br />
	([HeaderValue](#headervalue)) 控制选项所应用的Header键值对。

- **append**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 是否添加的开关，如果为true（默认值），则该值将附加到现有值。

### ApiConfigSource
[ApiConfigSource proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L139)

API配置源。这标识了Envoy将用来获取xDS的API类型和群集。

```
{
  "api_type": "...",
  "cluster_name": [],
  "refresh_delay": "{...}"
}
```

- **api_type**<br />
	([ApiConfigSource.ApiType](#apiconfigsourceapitype)) API类型。

- **cluster_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 可以提供多个群集名称。如果定义了大于1个集群，如果发生任何类型的故障，则将循环切换。

- **refresh_delay**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 对于REST API，连续轮询之间的间隔。

### ApiConfigSource.ApiType (Enum)
[ApiConfigSource.ApiType proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L141)

可以通过REST API或gRPC获取。

- **REST_LEGACY**<br />
	(DEFAULT) REST-JSON对应于传统v1 API。

- **REST**<br />
    REST-JSON v2 API，将使用v2 protos规范的JSON编码。

- **GRPC**<br />
    gRPC v2 API。

### AggregatedConfigSource
[AggregatedConfigSource proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L162)

聚合发现服务（ADS）选项。这目前是空的，但在ConfigSource中设置时可以用来指定要使用ADS。

```
{}
```

### ConfigSource

[ConfigSource proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L171)

监听器，集群，路由，端口等配置可以从文件系统或xDS API源获取。使用`inotify`监视文件系统配置以进行更新。

```
{
  "path": "...",
  "api_config_source": "{...}",
  "ads": "{...}"
}
```

- **path**<br />
	([string](#https://developersgooglecom/protocol-buffers/docs/proto#scalar)) 配置从文件系统路径来源来更新配置。

- **api_config_source**<br />
	([ApiConfigSource](#apiconfigsource)) API配置源。

- **ads**<br />
	([AggregatedConfigSource](#aggregatedconfigsource)) 配置使用ADS将做为配置源。将使用[引导程序](Bootstrap.md)配置中的ADS API配置源。

    注意：必须选择`path`,`api_config_source`,`ads`其中一个选项配置。

### TransportSocket
[TransportSocket proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L188)

监听器和集群中传输套接字的配置。如果配置为空，则将根据`tls_context`的平台和现有的来选择默认的传输套接字实现和配置。

```
{
  "name": "...",
  "config": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 要实例化的传输套接字的名称。该名称必须匹配支持的传输套接字实现。

- **config**<br />
	([Struct](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#struct)) 具体将要被实例化的传输套接字配置。请参阅[支持的传输套接字]()实现以获取更多信息。

### RoutingPriority (Enum)
[RoutingPriority proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L96)

Envoy在路由和虚拟集群级别，都支持上游优先级路由。当前的实现是针对每个优先级别，使用不同的连接池和断路设置。这意味着即使对于HTTP/2请求，两个物理连接也将被用于上游主机。将来，Envoy可能会支持真正的HTTP/2优先级，而不是单个上行连接。

- **DEFAULT**<br />
	(DEFAULT)

- **HIGH**

### RequestMethod(Enum)
[RequestMethod proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L102)

HTTP请求方法

- **METHOD_UNSPECIFIED**<br />
	(DEFAULT)
- **GET**
- **HEAD**
- **POST**
- **PUT**
- **DELETE**
- **CONNECT**
- **OPTIONS**
- **TRACE**

## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)

