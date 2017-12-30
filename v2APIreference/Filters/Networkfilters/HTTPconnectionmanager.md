## HTTP连接管理

- [filter.network.HttpConnectionManager](#filternetworkhttpconnectionmanager)
- [filter.network.HttpConnectionManager.Tracing](#filternetworkhttpconnectionmanagertracing)
- [filter.network.HttpConnectionManager.Tracing.OperationName (Enum)](#filternetworkhttpconnectionmanagertracingoperationname-enum)
- [filter.network.HttpConnectionManager.SetCurrentClientCertDetails](#filternetworkhttpconnectionmanagersetcurrentclientcertdetails)
- [filter.network.HttpConnectionManager.CodecType (Enum)](#filternetworkhttpconnectionmanagercodectype-enum)
- [filter.network.HttpConnectionManager.ForwardClientCertDetails (Enum)](#filternetworkhttpconnectionmanagerforwardclientcertdetails-enum)
- [filter.network.Rds](#filternetworkrds)
- [filter.network.HttpFilter](#filternetworkhttpfilter)

HTTP连接管理[配置概述](../../../Configurationreference/HTTPconnectionmanager.md)。

### filter.network.HttpConnectionManager
[filter.network.HttpConnectionManager proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/http_connection_manager.proto#L19)

```
{
  "codec_type": "...",
  "stat_prefix": "...",
  "rds": "{...}",
  "route_config": "{...}",
  "http_filters": [],
  "add_user_agent": "{...}",
  "tracing": "{...}",
  "http_protocol_options": "{...}",
  "http2_protocol_options": "{...}",
  "server_name": "...",
  "idle_timeout": "{...}",
  "drain_timeout": "{...}",
  "access_log": [],
  "use_remote_address": "{...}",
  "generate_request_id": "{...}",
  "forward_client_cert_details": "...",
  "set_current_client_cert_details": "{...}"
}
```

- **codec_type**<br />
	([filter.network.HttpConnectionManager.CodecType](#filternetworkhttpconnectionmanagercodectype)) 应用与连接管理器的编解码器类型。

- **stat_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 连接管理器发布的统计信息所使用的前缀。有关更多信息，请参阅[统计文档](../../../Configurationreference/HTTPconnectionmanager/Statistics.md)。

- **rds**<br />
	([filter.network.Rds](#filternetworkrds)) 通过RDS API动态加载连接管理器的路由表。

    必须正确设置`rds`，`route_config`其中一个。

- **route_config**<br />
	([RouteConfiguration](../v2APIreference/HTTProutemanagementandRDS.md#routeconfiguration)) 在此属性中指定静态的连接管理器的路由表。

    必须正确设置`rds`，`route_config`其中一个。

- **http_filters**<br />
	([filter.network.HttpFilter](#filternetworkhttpfilter)) 构成连接管理器请求的过滤器链，包括各个HTTP过滤器的列表。当请求发生时，将按照顺序处理过滤器。

- **add_user_agent**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 连接管理器是否处理[user-agent](../../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#user-agent)和[x-envoy-downstream-service-cluster](../../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-envoy-downstream-service-cluster)头。有关更多信息，请参阅相应的链接。默认为false。

- **tracing**<br />
	([filter.network.HttpConnectionManager.Tracing](#filternetworkhttpconnectionmanagertracing)) 是否定义对象，决定连接管理器是否将跟踪数据发送到已配置的跟踪服务程序中。

- **http_protocol_options**<br />
	([Http1ProtocolOptions](#http1protocoloptions)) 传递给HTTP/1编解码器，额外的HTTP/1设置选项。

- **http2_protocol_options**<br />
	([Http2ProtocolOptions](#http2protocoloptions)) 额外的HTTP/2设置选项，直接传递给HTTP/2编解码器。

- **server_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 连接管理器将在响应头中写入响应的服务名。如果未设置，则默认为Enovy。

- **idle_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) 由连接管理器管理的连接空闲超时时长。在没有活动的请求时，持续的时间超过设定的阈值，则认为连接超时。如果没有设置，则没有空闲超时。当达到空闲超时后，连接将被关闭。如果连接是HTTP/2连接，则在关闭连接之前会发生顺序排空。看[drain_timeout](#drain_timeout)。

- **drain_timeout**<br />
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Envoy将在发送HTTP/2“关闭通知”（GOAWAY帧与最大流ID）和最终GOAWAY帧之间等待的时间。这是为了让Envoy支持与最后GOAWAY帧竞争的新的流处理，所提供的宽限期。在这个宽限期间，Envoy将继续接受新的流。在宽限期之后，最终GOAWAY帧被发送，Envoy将开始拒绝新的流。在连接遇到空闲超时或通用服务器耗尽时都会发生排空。如果未指定此选项，则默认宽限期为5000毫秒（5秒）。

- **access_log**<br />
	([filter.accesslog.AccessLog](#filteraccesslogaccesslog)) 从连接管理器发出的HTTP访问日志的配置。

- **use_remote_address**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 如果设置为true，连接管理器将在确定内部和外部源以及操作各种头部时使用客户端连接的真实远程地址。如果设置为false或不存在，连接管理器将使用`x-forwarded-for`HTTP头。有关更多信息，请参阅[x-forwarded-for](../../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-forwarded-for)，[x-envoy-internal](../../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-envoy-internal)和[x-envoy-external-address](../../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-envoy-external-address)的文档。

- **generate_request_id**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 连接管理器是否会自动生成[x-request-id](../../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-request-id)头，如果该头不存在。默认为true。生成一个随机的UUID4（性能代价比较大），所以在高吞吐量的情况下，这个功能是不需要的，它可以被禁用。

- **forward_client_cert_details**<br />
	([filter.network.HttpConnectionManager.ForwardClientCertDetails](#filternetworkhttpconnectionmanagerforwardclientcertdetails)) 如何处理[x-forward-client-cert](../../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-forward-client-cert)（XFCC）HTTP头。

- **set_current_client_cert_details**<br />
	([filter.network.HttpConnectionManager.SetCurrentClientCertDetails](#filternetworkhttpconnectionmanagersetcurrentclientcertdetails)) 只有在[forward_client_cert_details](#forward_client_cert_details)为`APPEND_FORWARD`或`SANITIZE_SET`且客户端连接为`mTLS`时，此字段才有效。它指定要转发的客户端证书中的字段。请注意，在[x-forwarded-client-cert](../../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-forward-client-cert)头中，始终设置`Hash`，并在客户端证书显示SAN值时始终设置`By`。

### filter.network.HttpConnectionManager.Tracing
[filter.network.HttpConnectionManager.Tracing proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/http_connection_manager.proto#L65)

```
{
  "operation_name": "...",
  "request_headers_for_tags": []
}
```

- **operation_name**<br />
	([filter.network.HttpConnectionManager.Tracing.OperationName](#filternetworkhttpconnectionmanagertracingoperationname-enum)) span名将由此字段生成。

- **request_headers_for_tags**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 用于为活动span创建标签的标题名称列表。该标题名用于填充span标记名，标题值用于填充span标记值。如果指定头的名称出现在请求头中，则会创建该标签。

### filter.network.HttpConnectionManager.Tracing.OperationName (Enum)
[filter.network.HttpConnectionManager.Tracing.OperationName proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/http_connection_manager.proto#L66)

- INGRESS
	(DEFAULT) 标记HTTP监听器用于入站/入口请求。

- EGRESS
    标记HTTP监听器用于出站/出口请求。

### filter.network.HttpConnectionManager.SetCurrentClientCertDetails (Enum)
[filter.network.HttpConnectionManager.SetCurrentClientCertDetails proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/http_connection_manager.proto#L164)

```
{
  "subject": "{...}",
  "san": "{...}"
}
```

- **subject**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 是否转发客户端证书的标题。默认为false。

- **san**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 是否转发客户端证书的SAN。默认为false。

### filter.network.HttpConnectionManager.CodecType (Enum)
[filter.network.HttpConnectionManager.CodecType proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/http_connection_manager.proto#L20)

- **AUTO**<br />
	(DEFAULT) 对于每个新的连接，连接管理器将自行决定使用哪个编解码器。此模式支持TLS监听器的ALPN以及监听器明文的协议。如果ALPN数据可用，则优选，否则使用协议解析。在几乎所有情况下，这是优选的设置。

- **HTTP1**<br />
    连接管理器将假定客户端使用HTTP/1.1协议。

- **HTTP2**<br />
    连接管理器将假定客户端使用HTTP/2（Envoy不需要通过TLS发送HTTP/2或者使用ALPN，事先预知的）。

### filter.network.HttpConnectionManager.ForwardClientCertDetails (Enum)
[filter.network.HttpConnectionManager.ForwardClientCertDetails proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/http_connection_manager.proto#L138)

如何处理[x-forward-client-cert](#x-forward-client-cert)（XFCC）HTTP头。

- **SANITIZE**<br />
	(DEFAULT) 不要将XFCC头部发送到下一跳。这是默认值。

- **FORWARD_ONLY**<br />
    当客户端连接是mTLS（Mutual TLS）时，转发请求中的XFCC头。
    
- **APPEND_FORWARD**<br />
    当客户端连接是mTLS时，将客户端证书信息附加到请求的XFCC头并转发它。
    
- **SANITIZE_SET**<br />
    当客户端连接是mTLS时，使用客户端证书信息重置XFCC头，并将其发送到下一个跃点。
    
- **ALWAYS_FORWARD_ONLY**<br />
    始终在请求中转发XFCC头，而不管客户端连接是否为mTLS。

### filter.network.Rds
[filter.network.Rds proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/http_connection_manager.proto#L181)

```
{
  "config_source": "{...}",
  "route_config_name": "..."
}
```
- **config_source**<br />
	([ConfigSource](#configsource), REQUIRED) RDS的配置源描述符。

- **route_config_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 配置的路由名称。这个名字将被传递给`RDS API`。这允许配置多个HTTP监听器（和关联的HTTP连接管理器）使用不同的路由配置。

### filter.network.HttpFilter
[filter.network.HttpFilter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/network/http_connection_manager.proto#L192)

```
{
  "name": "...",
  "config": "{...}"
}
```

- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 要实例化的过滤器的名称。该名称必须与支持的过滤器匹配。内置的过滤器有：
    - [envoy.buffer](../../../Configurationreference/HTTPfilters/Buffer.md)
    - [envoy.cors](../../../Configurationreference/HTTPfilters/CORSfilter.md)
    - [envoy.fault](../../../Configurationreference/HTTPfilters/FaultInjection.md)
    - [envoy.http_dynamo_filter](../../../Configurationreference/HTTPfilters/DynamoDB.md)
    - [envoy.grpc_http1_bridge](../../../Configurationreference/HTTPfilters/gRPCHTTP11bridge.md)
    - [envoy.grpc_json_transcoder](../../../Configurationreference/HTTPfilters/gRPCJSONtranscoderfilter.md)
    - [envoy.grpc_web](../../../Configurationreference/HTTPfilters/gRPCWebfilter.md)
    - [envoy.health_check](../../../Configurationreference/HTTPfilters/Healthcheck.md)
    - [envoy.lua](../../../Configurationreference/HTTPfilters/Lua.md)
    - [envoy.rate_limit](../../../Configurationreference/HTTPfilters/Ratelimit.md)
    - [envoy.router](../../../Configurationreference/HTTPfilters/Router.md)

- **config**<br />
	([Struct](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#struct)) 指定的过滤器配置，这取决于被实例化的过滤器。有关更多文档，请参阅支持的过滤器。

## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)

