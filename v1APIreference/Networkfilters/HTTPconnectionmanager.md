### HTTP连接管理

- HTTP连接管理[架构概述](../../Introduction/Architectureoverview/HTTPconnectionmanagement.md)。
- HTTP协议[架构概述](../../Introduction/Architectureoverview/HTTPconnectionmanagement.md)。

```
{
  "name": "http_connection_manager",
  "config": {
    "codec_type": "...",
    "stat_prefix": "...",
    "rds": "{...}",
    "route_config": "{...}",
    "filters": [],
    "add_user_agent": "...",
    "tracing": "{...}",
    "http1_settings": "{...}",
    "http2_settings": "{...}",
    "server_name": "...",
    "idle_timeout_s": "...",
    "drain_timeout_ms": "...",
    "access_log": [],
    "use_remote_address": "...",
    "forward_client_cert": "...",
    "set_current_client_cert": "...",
    "generate_request_id": "..."
  }
}
```
- **codec_type**<br />
	(required, string) 提供连接管理器所使用的编解码器的类型。可能的值有：
    - **http1**<br />
    连接管理器将假定客户端正在使用HTTP/1.1。
    - **http2**<br />
    连接管理器将假定客户端正在使用HTTP/2（Envoy并不需要通过TLS发送HTTP/2或者使用ALPN，应预先设定好）。
    - **auto**<br />
    对于每个新的连接，连接管理器将决定使用哪个编解码器。此模式支持TLS监听器的ALPN以及明文监听器的协议鉴别。如果ALPN数据可用，则优选，否则使用协议鉴别。几乎在所有情况下，设置此选择的是正确的选择。

- **stat_prefix**<br />
	(required, string) 发布连接管理器的统计信息时使用的人类可读前缀。有关更多信息，请参阅[统计文档](../../Configurationreference/HTTPconnectionmanager/Statistics.md)。

- **rds**<br />
	(sometimes required, object) 连接管理器配置必须指定选择`rds`或`route_config`其中之一。如果指定了`rds`，则连接管理器的路由表将通过RDS API动态加载。有关更多信息，请参阅[文档](../../v1APIreference/HTTPRouteconfiguration/RoutediscoveryserviceRDS.md)。

- **route_config**<br />
	(sometimes required, object) 连接管理器配置必须指定选择`rds`或`route_config`其中之一。如果指定了`route_config`，则认为该连接管理器的路由表是静态的，并在此属性中指定路由配置。

- **filters**<br />
	(required, array) 构成该连接管理器请求的[过滤器链](../../Introduction/Architectureoverview/HTTPfilters.md)，包含多个HTTP过滤器的列表当请求事件发生时，将按顺序处理过滤器。

- **add_user_agent**<br />
	(optional, boolean) 连接管理器是否处理[user-agent](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#user-agent)和[x-envoy-downstream-service-cluster](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-envoy-downstream-service-cluster)头。有关更多信息，请参阅相关链接。默认为false。

- **tracing**<br />
	(optional, object) 定义的跟踪服务的配置对象，提供连接管理器跟踪数据发往的[跟踪服务程序](../../v1APIreference/Tracing.md)配置。

- **http1_settings**<br />
	(optional, object) 传递给HTTP/1编解码器的扩展配置。

    - **allow_absolute_url**<br />
	(optional, boolean) 在请求中处理具有绝对URL的http请求。这些请求通常由客户端发送到转发/显式代理。这允许客户端将envoy配置为他们的http代理。例如，在Unix中，这通常是通过设置http_proxy环境变量来完成的。

- **http2_settings**<br />
	(optional, object) 传递给HTTP/2编解码器的扩展配置。目前支持的设置有：
    
    - **hpack_table_size**<br />
    	(optional, integer) 允许编码器使用的动态HPACK表的最大值（以八位字节为单位）。有效值范围从0到4294967295（2^32-1），默认值为4096. 0表示禁用头部压缩。
    
    - **max_concurrent_streams**<br />
    	(optional, integer) 一个HTTP/2连接上同时允许的最大并发流。有效值范围从1到2147483647（2^31-1），默认值为2147483647。
    
    - **initial_stream_window_size**<br />
    	(optional, integer) 初始时控制流窗口的大小。有效值范围从65535（2^16-1，HTTP/2默认值）到2147483647（2^31-1，HTTP/2最大值），默认值为268435456（256*1024*1024）。
    
        注：65535是来自HTTP/2规范的初始窗口大小。我们现在只支持增加默认的窗口大小，所以也是最小的。
    
        这个字段也可以作为Envoy在HTTP/2编解码器缓冲区中每个字节缓冲的字节数的软限制。一旦缓冲区到达这个指针，将触发停止接收数据流到编解码器缓冲区。
    
    - **initial_connection_window_size**<br />
    	(optional, integer) 与`initial_stream_window_size`类似，但是用于连接级流量控制窗口。目前，这与`initial_stream_window_size`具有相同的最小/最大/默认值。
    
    这些与上游群集`http2_settings`中提供的选项相同。
    
- **server_name**<br />
	(optional, string) 可选，连接管理器将在响应中增加服务名的头部字段。如果未设置，则默认为Envoy。

- **idle_timeout_s**<br />
	(optional, integer) 由该连接管理器所管理的连接空闲超时时间（以秒为单位）。空闲超时被定义为一段时间内没有活动请求。如果没有设置，则没有空闲超时。当达到空闲超时时，连接将被关闭。如果连接是HTTP/2连接，则在关闭连接之前会发生顺序排空。请参阅[drain_timeout_ms](#drain_timeout_ms)。

- **drain_timeout_ms**<br />
	(optional, integer) Envoy将在发送HTTP/2“关闭通知”（GOAWAY帧与最大流ID）和最终GOAWAY帧之间等待的时间。这是为了让Envoy支持与最后GOAWAY帧竞争的新的流处理，所提供的宽限期。在这个宽限期间，Envoy将继续接受新的流。在宽限期之后，最终GOAWAY帧被发送，Envoy将开始拒绝新的流。在连接遇到空闲超时或通用服务器耗尽时都会发生排空。如果未指定此选项，则默认宽限期为5000毫秒（5秒）。

- **access_log**<br />
	(optional, array) 连接管理器发出的HTTP访问日志的配置。

- **use_remote_address**<br />
	(optional, boolean) 如果设置为true，连接管理器将在确定内部和外部源以及操作各种头部时使用客户端连接的真实远程地址。如果设置为false或不存在，连接管理器将使用`x-forwarded-for`HTTP头。有关更多信息，请参阅[x-forwarded-for](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-forwarded-for)，[x-envoy-internal](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-envoy-internal)和[x-envoy-external-address](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-envoy-external-address)的文档。

- **forward_client_cert**<br />
	(optional, string) 如何处理`x-forward-client-cert`（XFCC）HTTP头。可能的值为：
    - sanitize: 不要将XFCC头部发送到下一跳。这是默认值。
    - forward_only: 当客户端连接是mTLS（Mutual TLS）时，转发请求中的XFCC头。
    - always_forward_only: 始终在请求中转发XFCC标头，而不管客户端连接是否为mTLS。
    - append_forward: 当客户端连接是mTLS时，将客户端证书信息附加到请求的XFCC头并转发它。
    - sanitize_set: 当客户端连接是mTLS时，使用客户端证书信息重置XFCC头，并将其发送到下一个跃点。
    
    有关XFCC头的格式，请参阅[x-forward-client-cert](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-forward-client-cert)。

- **set_current_client_cert_details**<br />
	(optional, array) 字符串列表，只有在[forward_client_cert](#forward_client_cert)为`append_forward`或`sanitize_set`且客户端连接为`mTLS`时，此字段才有效。它指定要转发的客户端证书中的字段。请注意，在[x-forwarded-client-cert](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-forward-client-cert)头中，始终设置`Hash`，并在客户端证书显示SAN值时始终设置`By`。

- **generate_request_id**<br />
	(optional, boolean) 连接管理器是否自动生成[x-request-id](../../Configurationreference/HTTPconnectionmanager/HTTPheadermanipulation.md#x-request-id)头，如果该头不存在。默认为true。生成一个随机的UUID4（性能代价比较大），所以在高吞吐量的情况下，这个功能是不需要的，它可以被禁用。

### Tracing
```
{
  "tracing": {
    "operation_name": "...",
    "request_headers_for_tags": []
  }
}
```
- **operation_name**<br />
	(required, string) span的名称从`operation_name`中获取。目前仅支持“ingress” 和 “egress”。 

- **request_headers_for_tags**<br />
	(optional, array) 用于为活跃的span创建头部名称的列表。`header`名称用于填充`tag`名称，`header`值用于填充`tag`值。如果指定的`header`名称出现在请求头中，则会创建该`tag`。

### Filters
HTTP过滤器[架构概述](../../Introduction/Architectureoverview/HTTPfilters.md)。

```
{
  "name": "...",
  "config": "{...}"
}
```
- **name**<br />
	(required, string) 要实例化的过滤器的名称。该名称必须与支持的过滤器匹配。

- **config**<br />
	(required, object) 指定的过滤器配置，这取决于被实例化的过滤器类型。有关更多文档，请参阅支持的[过滤器](../../Configurationreference/HTTPfilters.md)。



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

