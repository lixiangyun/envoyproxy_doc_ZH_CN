## HTTP头部操作

HTTP连接管理器在解码期间（当接收到请求时）以及在编码期间（当响应被发送时）处理多个HTTP头部。

### user-agent

如果启用了`add_user_agent`选项，则连接管理器在解码过程中，会设置`user-agent`头部。这个头部只有未设置的情况下才会被修改。如果连接管理器确实设置了该字段，则该值由命令行选项`--service-cluster`确定。

### server
`server`头部将在编码时设置为选项[server_name](../../v1APIreference/Networkfilters/HTTPconnectionmanager.md)中的值。

### x-client-trace-id
如果一个外部客户端设置了这个头部，Envoy会将客户端提供的跟踪ID和内部生成的`x-request-id`关联起来。`x-client-trace-id`需要全局唯一性，建议生成一个uuid4。如果设置了此标志，它将具有与`x-envoy-force-trace`相似的效果。 请参阅`[tracing.client_enabled](../../Configurationreference/HTTPconnectionmanager/Runtime.md)`运行时配置设置。

### x-envoy-downstream-service-cluster
内部服务通常想知道哪个服务正在调用它们。从外部的请求中这个头部会被清除，但是对于内部请求，将包含调用者的服务集群信息。请注意，在当前的实现中，这应该被认为是一个潜规则，因为它是由调用者设置的，并且很容易被任何内部实体欺骗。将来，Envoy将支持相互认证的TLS网格，这将使这个头部完全安全。与`user-agent`一样，该值由`--service-cluster`命令行选项确定。为了启用此功能，您需要将`user_agent`选项设置为`true`。

### x-envoy-downstream-service-node
内部服务可能想知道下游节点请求来自哪里。这个头与`x-envoy-downstream-service-cluster`非常相似，除了从`--service-node`选项中取值。

### x-envoy-external-address
服务端想要根据客户端IP地址，执行分析是很常见的情况。在XFF(`x-forwarded-for`)漫长的讨论中，这可能会变得相当复杂。一个正确的实现需涉及转发XFF，然后从右边选择第一个非RFC1918地址。由于这个常用的功能，Envoy通过在解码过程中设置`x-envoy-external-address`来简化这种实现，当且仅当请求从外部进入（即它来自外部客户端）。对于内部请求，`x-envoy-external-address`不会设置或覆盖。为了分析需要，可以在内部服务之间安全地转发该头部，而不必关心XFF的复杂性。

### x-envoy-force-trace
如果一个内部请求设置了这个头部，Envoy会修改生成的`x-request-id`，使得它强制收集跟踪信息。这也迫使`x-request-id`在响应头中返回。如果这个请求标识随后被传播到其他主机，那么这些主机上的跟踪也将被收集，这将为整个请求流提供一致的跟踪。请参阅[tracing.global_enabled](Configurationreference/HTTPconnectionmanager/Runtime.md)和[tracing.random_sampling](Configurationreference/HTTPconnectionmanager/Runtime.md)运行时配置设置。

### x-envoy-internal
一个常见的情况就是服务想要知道请求是否来自内部。Envoy使用XFF来明确这一点，然后将头部的值设置为`true`。
这是就避免了需要解析和理解XFF的好处。

### x-forwarded-client-cert
x-forward-client-cert（XFCC）是一个代理头，在从客户端到服务器的所经过的路径上，表示请求已经流经的部分或全部客户端或代理的证书信息。代理可以在请求代理之前选择清除/追加/转发XFCC头。

XFCC头部值是以逗号（“,”）为分隔的字符串。每个子字符串都是XFCC元素，它保存着由一个代理所添加的信息。代理可以将当前客户端证书信息作为XFCC元素，追加到请求的XFCC头部中。

每个XFCC元素都是一个分号“;”分隔的字符串。每个子字符串都是一个键值对，由一个等号（“=”）组成。`key`不区分大小写，`value`区分大小写。如果“,”，“;”或“=”出现在一个值中，则该`value`应该用双引号。`value`中的双引号应该被反斜杠双引号（“）替换。

以下键支持：

1. `By`    当前代理证书的主题备用名称（SAN:Subject Alternative Name）。
2. `Hash`  当前客户端证书的SHA256摘要。
3. `SAN`   当前客户端证书的SAN字段（URI类型）。
4. `Subject` 当前客户端证书的主题字段。这个值总是双引号。

以下是两个XFCC头部的例子：

1. 
```
x-forwarded-client-cert: By=http://frontend.lyft.com;Hash=468ed33be74eee6556d90c0149c1309e9ba61d6425303443c0748a02dd8de688;Subject="/C=US/ST=CA/L=San Francisco/OU=Lyft/CN=Test Client";SAN=http://testclient.lyft.com
```

2. 
```
x-forwarded-client-cert: By=http://frontend.lyft.com;Hash=468ed33be74eee6556d90c0149c1309e9ba61d6425303443c0748a02dd8de688;SAN=http://testclient.lyft.com,By=http://backend.lyft.com;Hash=9ba61d6425303443c0748a02dd8de688468ed33be74eee6556d90c0149c1309e;SAN=http://frontend.lyft.com
```

Envoy处理XFCC的方式由HTTP连接管理器选项`forward_client_cert`和`set_current_client_cert_details` 指定。如果未设置`forward_client_cert`，则默认会对XFCC头部进行清除。

### x-forwarded-for
x-forwarded-for（XFF）是一个标准的代理头，它表示请求在从客户端到服务器的过程中，所经过的IP地址。一个兼容的代理服务，应在代理请求之前将最近客户端的IP地址附加到XFF列表中。XFF的一些例子是：

1. `x-forwarded-for: 50.0.0.1` (单个客户端)
2. `x-forwarded-for: 50.0.0.1, 40.0.0.1` (外部代理跳转)
3. `x-forwarded-for: 50.0.0.1, 10.0.0.1` (内部代理跳转)

如果HTTP连接管理器选项`use_remote_address`设置为true，Envoy将只附加到XFF。这意味着如果`use_remote_address`为`false`，则连接管理器将透明模式运行，不修改XFF的内容。这对于特定的网格部署类型是必需的，具体取决于Envoy是在边缘节点还是作为内部服务节点。

最终Envoy会根据的XFF内容来确定请求是从外部还是从内部发起的。这会影响`x-envoy-internal`头部是否会被设置。

关于XFF的一些非常重要的注意事项：

1. 由于IP地址被附加到XFF，只有最后一个地址（最右边）可以被信任。更具体地说，右边的第一个外部（非RFC1918）地址是唯一可信的地址。左边的任何东西都可能被欺骗。为了方便处理分析，在前面Envoy还将设置`x-envoy-external-address`头部。

2. XFF是Envoy用来确定请求是来自内部还是外部。它通过检查XFF是否包含一个单一的IP地址（RFC1918地址）来做到这一点。
 - **注意**：如果内部服务代理到另一个内部服务的外部请求，并且包含原始的XFF头，如果`use_remote_address`被设置，Envoy将在出口附加它。这会导致对方认为请求是外部的。一般来说，这是XFF被转发的目的。如果没有打算，不要转发XFF，而是转发`x-envoy-internal`。
 - **注意**：如果内部服务保留XFF并转发到另一个内部服务，Envoy不会将其视为内部服务。这是一个已知的“BUG”，因为简化XFF的解析处理，以确定请求是否是内部的。在这种情况下，不要转发XFF，并允许Envoy使用一个内部原始IP生成一个新的。


### x-forwarded-proto

一个服务若想要知道前端Envoy代理的处理的始发协议类型（HTTP或HTTPS）。这是一个常见的情况。`x-forward-proto`包含这个信息。它将会被设置为`http`或`https`。

### x-request-id
Envoy使用`x-request-id`头来标识请求，并执行访问日志记录和跟踪。Envoy将为所有来自的外部请求（头部被清理）生成一个`x-request-id`头。它还会为内部请求生成一个`x-request-id`头，这意味着`x-request-id`可以并应该在客户端应用程序之间传递，以便在整个网格中有稳定的ID。由于Envoy是外置进程架构，头部不能由Envoy自动转发。这是少数几个需要瘦客户端库就可以完成的要求之一，关于如何完成这个，不在本文档范围。如果`x-request-id`跨所有主机传递，则可以使用以下功能：

- 通过[v1 API 过滤器](../../v1APIreference/Accesslogging.md)或[v2 API过滤器](../../v2APIreference/Filters/Commonaccesslogtypes.md)进行稳定的访问[日志记录](../../Configurationreference/Accesslogging.md)。
- 当通过运行时设置`tracing.random_sampling`或通过使用`x-envoy-force-trace`和`x-client-trace-id`头强制使能跟踪，来执行随机跟踪采样。

### x-ot-span-context
Envoy使用`x-ot-span-context`HTTP头在span跟踪之间建立适当的父子关系。这个头可以用于LightStep和Zipkin跟踪服务。例如，出口span是入口span（如果存在入口span）的子关系。Envoy在入口请求上注入`x-ot-span-context`头并将其转发给本地服务。Envoy依靠应用程序在出口调用时传播`x-ot-span-context`到上游。在这里查看[更多信息](../../Introduction/Architectureoverview/Tracing.md)。

### x-b3-traceid
Zipkin跟踪会使用Envoy中的`x-b3-traceid`HTTP头。TraceId长度为64位，表示跟踪的总体ID。跟踪中的每个span都共享此ID。点击这里查看更多关于[zipkin信息](https://github.com/openzipkin/b3-propagation)。

### x-b3-spanid
Zipkin跟踪会用到Envoy中的`x-b3-spanid`HTTP头。SpanId的长度是64位，表示当前操作在跟踪树中的位置。该值不应该被解释：它可以或不可以从TraceId的值中派生出来。点击这里查看更多关于[zipkin信息](https://github.com/openzipkin/b3-propagation)。

### x-b3-parentspanid
Zipkin跟踪使用Envoy中的`x-b3-parentspanid`HTTP头。ParentSpanId的长度为64位，表示父操作在跟踪树中的位置。当span是跟踪树的根时，ParentSpanId不存在。点击这里查看更多关于[zipkin信息](https://github.com/openzipkin/b3-propagation)。

### x-b3-sampled
Zipkin跟踪会使用Envoy中的`x-b3-sampled`HTTP头。当采样标志为1时，这个索引将被报告给跟踪系统。一旦采样设置为0或1，相同的值应始终向下游发送。点击这里查看更多关于[zipkin信息](https://github.com/openzipkin/b3-propagation)。

### x-b3-flags
Zipkin跟踪会使用Envoy中的`x-b3-flags`HTTP头。编码一个或多个选项。例如，Debug被编码为`X-B3-Flags:1`。请[参阅](https://github.com/openzipkin/b3-propagation)上的更多关于zipkin跟踪的信息。

### Custom request/response headers
自定义添加请求/响应头，与特定请求/响应路由相匹配，关于虚拟主机和全局路由配置。请参阅相关的v1和v2 API文档。

**注意**：将按照以下顺序附加到请求/响应中：路由级别头标，虚拟主机级别头标以及最终全局级别头标。

Envoy还支持将动态值添加到请求头域。支持的动态值是：

**％CLIENT_IP％**
- 由Envoy添加原始客户端IP作为`x-forwarded-for`的请求头。

**％PROTOCOL％**
- 原来的协议已由Envoy添加`x-forward-proto`作为请求头。

## 返回
- [上一级](../HTTPconnectionmanager.md)
- [首页目录](../../README.md)
