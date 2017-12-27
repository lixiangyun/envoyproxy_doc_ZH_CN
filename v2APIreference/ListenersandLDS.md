## 监听&监听发现

Listener[架构概述](../v2APIreference/ListenersandLDS.md)

### Listener
[Listener proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/lds.proto#L34)

```
{
  "name": "...",
  "address": "{...}",
  "filter_chains": [],
  "use_original_dst": "{...}",
  "per_connection_buffer_limit_bytes": "{...}",
  "drain_type": "..."
}
```
- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 配置该监听器的唯一名称。如果没有提供名称，Envoy将为监听器分配一个内部UUID。如果要通过LDS动态更新或删除侦听器，则必须提供唯一的名称。默认情况下，监听器名称的最大长度限制为60个字符。可以通过`--max-obj-name-len`命令行参数设置为所需的最大长度限制。

- **address**<br />
	([Address](../v2APIreference/Networkaddresses.md), REQUIRED) 监听器应该监听的地址。一般来说，地址必须是唯一的，尽管这是由操作系统的根据绑定规则管理的。例如，多个监听器可以监听Linux上的0端口，因为实际的端口将被OS分配。

- **filter_chains**<br />
	([FilterChain](#filterchain), REQUIRED) 这个监听器需要使用的过滤器列表。`FilterChain`在连接上使用特殊的`FilterChainMatch`。

    注意：在当前版本中，仅支持多个过滤器链，因此可以配置SNI。有关如何配置SNI以获取更多信息，请参阅[FAQ条目](../FAQ/HowdoIsetupSNI.md)。当配置多个过滤器链时，每个过滤器链必须具有相同的一组过滤器。如果过滤器不同，配置将无法加载。将来，这种限制将被放宽，使得根据哪个过滤器链匹配（基于SNI或其他参数）可以使用不同的过滤器。

- **use_original_dst**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 在连接重定向如果使用`iptables`，则代理接收连接的端口可能与原始目标地址不同。当此标志设置为`true`时，监听器将重定向的连接切换到与原始目标地址关联的监听程序。如果没有与原始目标地址关联的监听器，则连接由该监听器的接收处理。默认为`false`。

- **per_connection_buffer_limit_bytes**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 监听器的新连接读取和写入缓冲区大小的软限制。如果未指定，则使用默认值定义（1MB）。

- **drain_type**<br />
	([Listener.DrainType](#listenerdraintype-enum)) 监听器的级别，执行的逐出类型。

### Listener.DrainType (Enum)
[Listener.DrainType proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/lds.proto#L93)

- **DEFAULT**<br />
	(DEFAULT) 在调用`/healthcheck/fail`管理端口（连同健康检查过滤器），监听器删除/修改以及热重启时，进行响应处理。

- **MODIFY_ONLY**<br />
    针对监听器删除/修改和热启动而响应处理。此设置不包括健康检查失败。 如果Envoy仅当作入口和出口监听器，这个设置是可取的。


### Filter
[Filter proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/lds.proto#L107)

```
{
  "name": "...",
  "config": "{...}"
}
```
- **name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 要实例化的过滤器的名称。 该名称必须与支持的过滤器匹配。内置的过滤器有：

    - [envoy.echo](../Configurationreference/Networkfilters/Echo.md)
    - [envoy.http_connection_manager](../Configurationreference/HTTPconnectionmanager.md)
    - [envoy.mongo_proxy](../Configurationreference/Networkfilters/Mongoproxy.md)
    - [envoy.redis_proxy](../Configurationreference/Networkfilters/Redisproxy.md)
    - [envoy.tcp_proxy](../Configurationreference/Networkfilters/TCPproxy.md)

- **config**<br />
	([Struct](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#struct)) 对应的过滤器配置，这取决于被实例化的过滤器。有关更多文档，请参阅支持的过滤器。

### FilterChainMatch
[FilterChainMatch proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/lds.proto#L134)

指定用于为监听器选择特定过滤器链的匹配条件。

```
{
  "sni_domains": []
}
```
- **sni_domains**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 如果非空，则考虑`SNI`域名。可能包含通配符前缀，例如`*.example.com`的。

    注意: 有关如何配置SNI以获取更多信息，请参阅[FAQ条目](../FAQ/HowdoIsetupSNI.md)。
    

### FilterChain
[FilterChain proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/lds.proto#L178)

过滤器链包含一组匹配条件，一个TLS上下文选项，一组过滤器和各种其他参数。

```
{
  "filter_chain_match": "{...}",
  "tls_context": "{...}",
  "filters": [],
  "use_proxy_proto": "{...}"
}
```
- **filter_chain_match**<br />
	([FilterChainMatch](#filterchainmatch)) 将连接匹配到此过滤器链时使用的匹配规则。

- **tls_context**<br />
	([DownstreamTlsContext](../v2APIreference/CommonTLSconfiguration.md)) 此过滤器链的TLS上下文配置。

- **filters**<br />
	([Filter](#filter)) 构成与监听器建立连接的过滤器链，包含各个网络过滤器的列表。当连接事件发生时，按顺序处理。注意：如果过滤器列表为空，则默认关闭连接。

- **use_proxy_proto**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 监听器是否应该在新连接上使用PROXY协议V1头。如果启用此选项，则监听器将假定该连接的远程地址是在标题中指定的地址。包括AWS ELB的一些负载平衡器支持此选项。如果该选项不存在或设置为`false`，Envoy将使用物理对等地址作为远程连接地址。


## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)