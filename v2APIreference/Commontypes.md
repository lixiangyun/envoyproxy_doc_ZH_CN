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
- **region**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 当前属于哪个区域

- **zone**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) AWS上的可用区域（AZ），GCP区域等

- **sub_zone**</br>
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
- **id**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Envoy节点的标识符。

- **cluster**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Envoy节点所属的群集。

- **metadata**</br>
	([Struct](#)) 所在节点的扩展元数据，Envoy将直接传递给管理服务器。

- **locality**</br>
	([Locality](#)) 指定Envoy实例的运行位置。

- **build_version**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 在金丝雀（灰度发布）期间管理服务器，知道哪个版本的Envoy正在进行测试。这将由Envoy在管理服务器RPC中设置。

### Endpoint
[Endpoint proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L35)

上游主机标识符

```
{
  "address": "{...}"
}
```
- **address**</br>
	([Address](Networkaddresses.md))

### Metadata
[Metadata proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L74)

元数据为基于匹配的侦听器，过滤器链，路由和端点的过滤器提供了额外的输入。 它的结构是从过滤器名称（反向DNS格式）到特定于过滤器的元数据的映射。 过滤器的元数据键值被合并为连接并发生请求处理，同一个键的更新值将覆盖较早的值。

元数据的使用示例是在envoy.http_connection_manager.access_log命名空间中为http_connection_manager提供附加值。

为了实现负载平衡，元数据提供了一种子集群端点子集的方法。 终端具有关联的元数据对象，路由包含要匹配的元数据对象。 目前有一些明确的元数据用于此目的：

 {"envoy.lb": {"canary": <bool> }} This indicates the canary status of an endpoint and is also used during header processing (x-envoy-upstream-canary) and for stats purposes.

```
{
  "filter_metadata": "{...}"
}
```
- **filter_metadata**</br>
	(map<string, Struct>) Key is the reverse DNS filter name, e.g. com.acme.widget. The envoy.* namespace is reserved for Envoy’s built-in filters.


### RuntimeUInt32
[RuntimeUInt32 proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L81)

Runtime derived uint32 with a default when not specified.

```
{
  "default_value": "...",
  "runtime_key": "..."
}
```
- **default_value**</br>
	([uint32](#)) Default value if runtime value is not available.

- **runtime_key**</br>
	([string](#), REQUIRED) Runtime key to get value for comparison. This value is used if defined.

### HeaderValue
[HeaderValue proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L115)

Header name/value pair.

```
{
  "key": "...",
  "value": "..."
}
```
- **key**</br>
	([string](#)) Header name.

- **value**</br>
	([string](#)) Header value.


The same format specifier as used for HTTP access logging applies here, however unknown header values are replaced with the empty string instead of -.

### HeaderValueOption
[HeaderValueOption proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L128)

Header name/value pair plus option to control append behavior.

```
{
  "header": "{...}",
  "append": "{...}"
}
```
- **header**</br>
	([HeaderValue](#)) Header name/value pair that this option applies to.

- **append**</br>
	([BoolValue](#)) Should the value be appended? If true (default), the value is appended to existing values.

### ApiConfigSource
[ApiConfigSource proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/base.proto#L139)

API configuration source. This identifies the API type and cluster that Envoy will use to fetch an xDS API.

```
{
  "api_type": "...",
  "cluster_name": [],
  "refresh_delay": "{...}"
}
```
- **api_type**</br>
	([ApiConfigSource.ApiType](#))

- **cluster_name**</br>
	([string](#), REQUIRED) Multiple cluster names may be provided. If > 1 cluster is defined, clusters will be cycled through if any kind of failure occurs.

- **refresh_delay**</br>
	([Duration](#)) For REST APIs, the delay between successive polls.

### ApiConfigSource.ApiType(Enum)
[ApiConfigSource.ApiType proto]()

APIs may be fetched via either REST or gRPC.

- **REST_LEGACY**</br>
	(DEFAULT) ?REST-JSON legacy corresponds to the v1 API.

- **REST**
?REST-JSON v2 API. The canonical JSON encoding for the v2 protos is used.

- **GRPC**
?gRPC v2 API.

### AggregatedConfigSource
[AggregatedConfigSource proto]()

Aggregated Discovery Service (ADS) options. This is currently empty, but when set in ConfigSource can be used to specify that ADS is to be used.

{}

### ConfigSource

[ConfigSource proto]

Configuration for listeners, clusters, routes, endpoints etc. may either be sourced from the filesystem or from an xDS API source. Filesystem configs are watched with inotify for updates.

```
{
  "path": "...",
  "api_config_source": "{...}",
  "ads": "{...}"
}
```
- **path**</br>
	([string](#)) Path on the filesystem to source and watch for configuration updates.


Precisely one of path, api_config_source, ads must be set.

- **api_config_source**</br>
	([ApiConfigSource](#)) API configuration source.

Precisely one of path, api_config_source, ads must be set.

- **ads**</br>
	([AggregatedConfigSource](#)) When set, ADS will be used to fetch resources. The ADS API configuration source in the bootstrap configuration is used.


Precisely one of path, api_config_source, ads must be set.

### TransportSocket
[TransportSocket proto]()

Configuration for transport socket in listeners and clusters. If the configuration is empty, a default transport socket implementation and configuration will be chosen based on the platform and existence of tls_context.

```
{
  "name": "...",
  "config": "{...}"
}
```
- **name**</br>
	([string](#), REQUIRED) The name of the transport socket to instantiate. The name must match a supported transport socket implementation.

- **config**</br>
	([Struct](#)) Implementation specific configuration which depends on the implementation being instantiated. See the supported transport socket implementations for further documentation.

### RoutingPriority(Enum)
[RoutingPriority proto]()

Envoy supports upstream priority routing both at the route and the virtual cluster level. The current priority implementation uses different connection pool and circuit breaking settings for each priority level. This means that even for HTTP/2 requests, two physical connections will be used to an upstream host. In the future Envoy will likely support true HTTP/2 priority over a single upstream connection.

- **DEFAULT**</br>
	(DEFAULT)

- **HIGH**

### RequestMethod(Enum)
[RequestMethod proto]()

HTTP请求方法

- **METHOD_UNSPECIFIED**</br>
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
